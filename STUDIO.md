# STUDIO — Production Rules

## 1. Единственная цель

Мы делаем игру.

Главная единица результата — **accepted playable change**, а не:

- закрытая карточка;
- количество коммитов;
- число запущенных workers;
- объём документации;
- количество тестов.

Любая новая process-подсистема обязана отвечать на вопрос:

> Какой уже наблюдаемый failure mode она устраняет?

Если ответа нет — не строить.

---

## 2. Authority

### Human / Studio Owner

Human задаёт:

- product direction;
- текущий Player Outcome;
- priority boundaries;
- ограничения scope/budget;
- major design gates.

Human обязателен для:

- смены high concept;
- major core-loop change;
- крупного изменения scope;
- удаления крупной утверждённой feature;
- milestone acceptance;
- окончательного verdict по `fun / pacing / feel`;
- решения продолжать, пивотить или закрывать проект.

Human **не обязан** подтверждать каждую техническую карточку, merge или balance number внутри уже утверждённого design intent.

### Producer

Producer координирует production и **не является implementation-worker**.

Producer:

- переводит intent в Outcome;
- управляет backlog;
- определяет WIP;
- собирает Batches;
- выбирает allowed worker/model;
- запускает Orca Tasks/Dispatches;
- управляет dependencies;
- обрабатывает escalations;
- интегрирует готовые ветки;
- запускает acceptance;
- маршрутизирует gameplay Design Findings Game Designer'у;
- маршрутизирует narrative/canon findings Narrative Designer'у;
- выносит Human gate только когда это действительно decision gate.

### On-demand specialists

Studio не держит роли активными ради занятости.

Вызываются только по trigger:

- `Game Designer` — gameplay rules, systems, balance, progression, economy, pacing, game feel, design diagnosis;
- `Narrative Designer / Writer` — canon, voice, characters, dialogue, lore, item/location descriptions и другой художественный player-facing text;
- `Architect` — system boundaries, cross-zone contracts, persistence/data model, significant refactor, technical risk;
- `Developer` — implementation;
- `UI Developer` — player-facing UI implementation;
- `QA / Playtester` — integrated evidence, bugs, UX/design/narrative findings;
- `Reviewer` — conditional high-risk review.

Отдельной постоянной роли Integrator нет.

---

## 3. Production Model

Используем:

**Kanban flow + playable iterations.**

Не требуются:

- sprint planning;
- daily standup;
- retrospective;
- velocity;
- ceremony ради ceremony.

Iteration считается завершённой, когда Outcome:

1. существует в integrated game;
2. прошёл необходимые technical gates;
3. прошёл соответствующую acceptance/playtest;
4. получил verdict.

---

## 4. Work hierarchy

Только два persistent planning-level:

### Outcome

Наблюдаемое изменение опыта игрока.

Durable Outcome-файлы живут в `tasks/outcomes/`.

Пример:

> Первые 15 минут progression больше не имеют периода, где игрок несколько минут не получает нового действия, решения или заметного усиления.

### Atomic Card

Небольшое изменение, необходимое для Outcome.

Batch — **не третий permanent backlog-level**. Это временная упаковка связанных Cards для одного worker context.

---

## 5. Atomic Cards

Atomic Cards живут в Git:

`tasks/open/`

Card обязана содержать:

- Type;
- Parent Outcome;
- Size;
- Weight;
- Goal;
- ownership Zone;
- observable Acceptance;
- Scope / Non-goals;
- hard blockers;
- risk flags;
- required Evidence.

Дополнительные relations разрешены:

- `related`;
- `found-by`;
- `supersedes`.

Scheduling учитывает только реальные hard blockers.

### Типы Cards

- `Change` — изменить игру.
- `Bug` — нарушение уже существующего contract.
- `Design Finding` — игра работает технически, но результат плох.
- `Experiment` — проверить гипотезу.
- `Tech Debt` — внутренний дефект без прямого player-facing результата.

---

## 6. Size / Weight

Шкала:

| Size | Weight |
|---|---:|
| XS | 1 |
| S | 2 |
| M | 3 |
| L | 5 |
| XL | 8 |

Это **не часы** и не velocity.

Размер учитывает:

- ширину context;
- число затронутых systems;
- ownership risk;
- design uncertainty;
- serialized Unity assets;
- validation cost;
- cross-zone impact.

Максимальный допустимый Batch Weight **не фиксируется теорией**. Он определяется Pilot отдельно для Unity и Browser.

---

## 7. Batching

Producer объединяет Cards прежде всего по **context locality**.

Хороший Batch:

- обслуживает один Outcome;
- имеет один доминирующий ownership domain;
- затрагивает связанный код/данные;
- не содержит конфликтующих authoritative facts;
- помещается в измеренный context/risk budget.

Плохой Batch:

- собран только ради нужного Weight;
- смешивает независимые systems;
- требует постоянно переключать mental context;
- содержит несколько крупных cross-zone изменений.

Не запускать отдельного worker на каждую мелкую Card, если связанные Cards можно закрыть одним context load.

---

## 8. Ownership / Zones

Zone — это **ownership domain**, а не просто папка.

Примеры:

- Core Progression;
- Economy;
- Gameplay;
- UI;
- Save/Persistence;
- Content/Data;
- Scene Composition.

File globs помогают найти пересечения, но не определяют смысл ownership.

Главное правило:

> Один active writer на один authoritative gameplay fact.

Два workers могут работать параллельно внутри одной большой области только после явного разделения ownership/contracts.

### Cross-zone change

Cross-zone change получает временного **Change Owner**.

Change Owner:

- фиксирует общий contract;
- определяет границы подзадач;
- следит за совместимостью;
- не становится новой постоянной ролью.

---

## 9. Design / Narrative ambiguity

Implementation-worker не должен самовольно придумывать gameplay truth или значимый художественный canon.

### Gameplay ambiguity

Если gameplay rule не определён:

1. worker формулирует конкретный blocking question;
2. Producer направляет его Game Designer'у;
3. Game Designer возвращает конкретное правило, numbers/constraints и acceptance;
4. worker продолжает тот же Batch/context;
5. новая Card создаётся только если выяснилась отдельная design-работа.

### Narrative ambiguity

Если implementation требует значимый художественный текст, lore, dialogue, character voice или новый canon:

1. worker не генерирует это «временно» от себя;
2. Producer направляет запрос Narrative Designer'у;
3. Narrative Designer возвращает usable text и указывает canon impact;
4. worker интегрирует готовый content.

Не создавать Narrative handoff для чисто функционального UI вроде `Buy`, `Back`, `+10% Speed`.

Где хранить решение:

- одноразовая implementation detail → Card/Batch;
- постоянное gameplay rule feature → authoritative feature truth;
- глобальное gameplay rule → `GAME.md`;
- durable narrative truth → `NARRATIVE.md`, **только если persistent narrative state реально появился**.

Conversation history не является source of truth.

---

## 10. WIP / Parallelism

Не оптимизировать под максимальное число активных агентов.

Оптимизировать под:

> максимальный независимый progress при минимальном coordination/integration cost.

Использовать:

- global WIP limit;
- ownership/zone WIP;
- exclusive writer для serialized assets;
- ограниченный Unity execution pool.

Конкретные лимиты определяет Pilot.

---

## 11. Done

Worker не имеет права объявить production Outcome завершённым только потому, что код написан.

### Low-risk technical Card

Может считаться Done после:

- required evidence;
- нужных gates;
- нахождения изменения в authoritative integrated branch.

### Player-facing Card

Нужна integrated acceptance.

### Design Outcome

Нужен playtest/design verdict.

Общее правило:

> Done = изменение существует и проверено в authoritative integrated game.

---

## 12. Integration

Отдельной роли Integrator v1 нет.

Producer управляет integration flow.

Для готовой ветки:

1. обновить ветку относительно актуального main;
2. разрешить конфликты по ownership semantics, а не механически;
3. выполнить appropriate merge gate;
4. только затем интегрировать в main;
5. дорогую acceptance не повторять после каждой мелкой ветки без причины.

Принцип:

**continuous source integration + batched expensive acceptance.**

---

## 13. QA / Playtest

Developer доказывает локальную корректность.

QA проверяет **integrated game**.

Количество AI playtests зависит от риска:

- small UI/bug → обычно 1;
- обычный Outcome → обычно 2;
- systemic progression/economy/core loop → обычно 3;
- milestone/major redesign → 3–5.

Это стартовая policy, а не догма.

Каждый tester получает другой:

- scenario;
- seed/state;
- objective;
- slice of the game.

AI не является финальным судьёй fun.

Он может выявлять:

- long dead time;
- repeated action;
- weak reward cadence;
- confusion;
- economy stalls;
- dead strategies;
- poor readability;
- feedback gaps.

Финальный существенный verdict по fun/pacing/feel остаётся за Human.

---

## 14. Tests

Не оптимизировать под enterprise coverage.

Автоматические tests особенно нужны для:

- deterministic systems;
- save/load;
- economy formulas;
- progression conditions;
- tricky state machines;
- regressions найденных bugs.

Не писать дорогой test harness там, где дефект дешевле и надёжнее увидеть коротким playtest.

---

## 15. Context discipline

Producer перед первым production round читает полное обязательное ядро:

1. `STUDIO.md`;
2. `GAME.md`;
3. `agents.md`;
4. `ORCA.md`;
5. `roles/producer.md`.

Implementation worker при старте читает только:

1. `roles/_common.md`;
2. свою role instruction;
3. Batch spec;
4. relevant design truth;
5. relevant code/data;
6. Project Profile commands.

Worker **не читает автоматически**:

- весь GDD;
- все open Cards;
- все role docs;
- прошлые conversations;
- весь Orca history.

`GAME.md` содержит GDD текущего вертикального среза. Producer извлекает из него только relevant truth в Batch spec; implementation worker не должен читать весь GDD без необходимости.

Детальная truth растущих feature переносится в feature-local документы, чтобы `GAME.md` оставался ограничен текущим slice, а не становился энциклопедией всей будущей игры.

---

## 16. Model / Cost policy

Routing выбирает:

> самую дешёвую разрешённую модель, которая достаточно сильна для задачи.

Готовый порядок, не требующий project setup:

1. `OpenCode → OmniRoute → eligible free model`.
2. Stronger/equivalent free route.
3. Codex/Claude paid normal.
4. Codex/Claude senior для leverage/escalation.
5. Human gate, если policy требует.

Сильная paid model не должна по умолчанию писать весь проект в одиночку.

Предпочтительный pattern:

`Senior reasoning → decomposition/contract/diagnosis → cheap workers implement → senior review only if risk justifies.`

### Provider resilience

Исчерпание Codex/Claude quota **не останавливает Studio целиком**.

Если paid routes недоступны, Studio входит в `FREE_ONLY` mode:

- все compatible Ready Batches продолжают исполняться через free pool;
- senior-required Batch блокируется локально, а не глобально;
- Producer по возможности декомпозирует blocked Batch так, чтобы independent cheap work продолжилось;
- уже начатая free работа не прерывается только потому, что paid quota восстановилась;
- после восстановления paid route senior-only work возвращается в scheduling естественным образом.

Главное правило:

> Пока существует Ready Batch, который может корректно выполнить доступный route, Studio продолжает production.

Provider outage — штатное operating condition, а не production emergency.

Подробности — `agents.md` и `ORCA.md`.

---

## 17. Monitoring

V1 не имеет собственного monitoring framework.

Использовать native Orca:

- Task status;
- Dispatch lifecycle;
- heartbeat/status;
- question/escalation;
- worker_done;
- terminal/worktree state;
- workspace comments/status.

Worker считается stuck по **evidence**, а не просто по таймеру.

Свой script/tool появляется только после реального повторяющегося failure mode.

---

## 18. Primary metrics

Сохраняем только пять основных:

1. `time_to_playable_change`;
2. `worker_launches_per_accepted_playable_change`;
3. `reopen_or_rework_rate`;
4. `integration_conflict_or_failure_rate`;
5. `human_interventions_per_outcome`.

Diagnostic metrics включать при проблеме:

- token usage;
- context reloads;
- gate duration;
- model/provider failure rate;
- QA false positives;
- free→paid escalation rate.

---

## 19. Health rule

Workflow упрощается, если регулярно наблюдается хотя бы одно:

- coordination/documentation занимает больше времени, чем implementation;
- workers ждут Producer;
- playable change требует множества restarts;
- process-only Cards растут;
- board требует ручного обслуживания;
- gates существенно длиннее типичной правки;
- agents чаще читают process docs, чем меняют игру.

Реакция на такой сигнал:

**сначала удалить/упростить процесс, а не строить новый слой автоматизации.**
