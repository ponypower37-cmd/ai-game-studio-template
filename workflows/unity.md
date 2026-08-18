# WORKFLOW — Unity Project Profile

## 1. Source parallelism != Editor parallelism

Можно иметь несколько coding worktrees, не запуская Unity Editor в каждом.

По умолчанию Pilot использует:

- параллельный source editing;
- **1 heavy Unity execution slot** одновременно.

Увеличивать до 2+ только после измеренного выигрыша.

---

## 2. Worktrees / Library

Каждый active source worker получает отдельный worktree.

Если конкретному worktree нужен Unity import/editor validation — у него должен быть собственный mutable Unity project state.

Не шарить один mutable `Library` между одновременно изменяемыми worktrees.

`Library` не source of truth.

Accelerator/cache не является обязательной частью v1. Добавлять только если imports стали измеримым bottleneck.

---

## 3. Serialized assets

`.unity`, `.prefab` и значимые serialized `.asset` имеют exclusive active writer.

Разные independent assets можно менять параллельно.

Merge tool — safety net, не scheduler.

### Hard rule

AI не редактирует Unity serialized YAML вручную.

Использовать:

- Unity Editor;
- Editor API;
- SerializedObject;
- prefab/scene APIs;
- Editor utilities.

---

## 4. `.meta`

Asset и соответствующий `.meta` должны рассматриваться как единая identity pair.

Не:

- терять `.meta`;
- создавать duplicate GUID случайными копиями;
- коммитить asset без корректной metadata.

---

## 5. Code vs Editor setup

Не делать всё code-generated только ради merge convenience.

Предпочитать:

- runtime systems → код;
- reusable config/data → нормальные Unity assets;
- repetitive setup → Editor tooling при доказанной пользе;
- scene composition → Unity assets с ownership lock.

---

## 6. Risk-tiered gates

### Worker/local

Когда применимо:

- targeted compile/test;
- static sanity;
- dirty-state check;
- evidence конкретной Card.

### Merge gate

По риску:

- Unity compile;
- relevant EditMode tests;
- relevant PlayMode tests;
- runtime/log sanity;
- serialized reference checks;
- merge marker/meta sanity.

### Integrated Outcome acceptance

По необходимости:

- playable launch/build;
- runtime exception scan;
- selected tests;
- screenshots;
- QA/playtest.

Не запускать самый дорогой полный pipeline после каждой мелкой Card без причины.

---

## 7. Tests policy

Особенно полезны:

- deterministic gameplay rules;
- save/load;
- progression conditions;
- economy calculations;
- regressions;
- complex state transitions.

Player feel не доказывается unit tests.

---

## 8. Build policy

Полноценный executable build:

- после integrated Outcome;
- раньше только если конкретный Batch без build-validation не доказуем.

---

## 9. QA build

QA получает authoritative integrated state/build, а не случайный worker branch, если задача не является branch-specific regression reproduction.

---

## 10. Pilot measurements

Измерить:

- cold import;
- warm import;
- compile wall time;
- EditMode/PlayMode wall time;
- build wall time;
- contention при 1 vs 2 Unity processes;
- frequency scene/prefab conflicts;
- benefit/cost Accelerator.

До измерения не усложнять pipeline.

---

## 11. Unity code quality / hot paths

Общие правила: `CODE_QUALITY.md`.

### Serialized fields

Для MonoBehaviour/ScriptableObject mutable inspector reference/value по умолчанию:

- private field + `[SerializeField]`, если внешний код не должен менять его напрямую;
- public API выдаёт только тот доступ, который реально нужен;
- public mutable field не использовать как shortcut ради скорости прототипирования, если он становится shared state contract.

### Per-frame allocations

`Update`, `FixedUpdate` и другие реально горячие loops требуют отдельной дисциплины.

По умолчанию избегай внутри hot path:

- ненужных временных collections/arrays;
- повторной string construction;
- LINQ/closures, если они создают managed allocations;
- Instantiate/Destroy churn для высокочастотных объектов;
- allocating Unity APIs, когда существует reusable/non-alloc path и это действительно hotspot.

Цель — минимизировать managed allocations per frame, **но `> 0 B` не считается автоматическим архитектурным браком**. Решение принимается по Profiler и frame budget.

Pooling/preallocation применять для доказанного или очевидного high-frequency churn, а не ко всем объектам проекта.

### Garbage collector

Не менять GC mode как default optimization.

`Disabled`/manual collection допускаются только как отдельная performance change с:

- profiler evidence;
- понятным memory budget;
- target-platform validation;
- before/after measurement;
- безопасным восстановлением normal mode.

Incremental GC/write-barrier tuning тоже не трогать без измеренной проблемы.

### Async in Unity 6

Для Unity-native frame/main-thread async flow предпочитай `UnityEngine.Awaitable`, когда его semantics подходят.

Не запрещай `.NET Task` глобально:

- third-party/.NET API может возвращать `Task`;
- `Awaitable` можно использовать рядом с task-based API;
- pooled `Awaitable` нельзя бездумно await несколько раз.

Выбор должен следовать lifetime/threading semantics конкретной операции, а не slogan «Task запрещён».

---

## 12. Performance evidence

Performance Card должна называть:

- scenario/hardware/platform;
- baseline metric;
- profiler evidence/hotspot;
- изменение;
- after metric;
- trade-off, если выросла сложность или memory use.

Не принимать performance refactor только по аргументу «это быстрее теоретически».
