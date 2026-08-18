# ORCA — Studio Usage

Этот файл описывает, как Studio использует уже существующие primitives Orca. Не дублировать их собственным task engine.

## 1. Mapping

### Run

Контейнер coordination state для текущего supervised production work.

Default:

- один Run на один активный Outcome;
- при продолжении того же Outcome Run можно продолжить;
- новый независимый Outcome обычно получает новый Run.

Это operational default, не immutable architecture rule.

### Atomic Card

Живёт в Git backlog.

Не обязана становиться отдельной Orca Task.

### Batch

Context-local набор Atomic Cards для одного worker context.

### Orca Task

Создаётся на **Batch**.

Task spec должен содержать готовое к исполнению указание, а не ссылку «прочитай файл и разберись».

### Dispatch

Конкретная попытка назначить Orca Task worker'у.

### Workspace / Worktree

Изолированная рабочая копия для Batch.

### Workspace Board

Используется как native визуальная доска исполнения.

По умолчанию:

`Todo → In Progress → In Review → Done`

Не создавать отдельную Jira/board поверх этого.

---

## 2. What is source of truth

### Git

Отвечает за:

- code;
- assets;
- `GAME.md`;
- feature design truth;
- open backlog;
- commits.

### Orca

Отвечает за:

- текущий Run;
- Task;
- Dispatch;
- worker lifecycle;
- blocking questions;
- escalation;
- Workspace status;
- execution comments.

Не хранить один и тот же operational status в двух собственных системах.

---

## 3. Board semantics

Одна Board Card/Workspace обычно соответствует **одному Batch**, а не одной мелкой Atomic Card.

Название Workspace должно описывать изменение, например:

`progression-earlygame-pass`

Не:

`fix-button-color-3`

если это одна из нескольких связанных мелких Cards.

Workspace comment используется для короткого актуального статуса:

- `reproduced; implementing`;
- `implementation complete; running targeted test`;
- `blocked: needs design answer`;
- `ready for integration`.

Board не является design document.

---

## 4. Task creation policy

Task spec должна содержать:

- Action verb;
- Parent Outcome;
- Cards included;
- acceptance;
- non-goals;
- ownership boundaries;
- evidence;
- risk flags;
- required role;
- relevant context paths.

Task не должна требовать от worker читать весь проектный backlog.

---

## 5. Supervised orchestration

Для Studio production используется supervised orchestration, потому что Producer:

- следит за DAG;
- ждёт `worker_done`;
- обрабатывает `question`;
- обрабатывает `escalation`;
- управляет integration.

Full handoff используется только когда supervision действительно не требуется.

---

## 6. Blocking questions

Worker не должен гадать при ambiguity.

Gameplay:

`Worker question → Producer → Game Designer/Architect → answer → same worker continues`

Narrative:

`Worker content need → Producer → Narrative Designer → usable text/canon answer → same worker continues`

Если вопрос меняет major design/narrative boundary — Producer поднимает Human gate.

---

## 7. Status / completion

`dispatched` не означает, что работа успешно началась или завершилась.

Producer различает:

- alive;
- blocked;
- waiting for answer;
- failed provider;
- no work started;
- completed.

`worker_done` — сигнал завершения Dispatch, но production acceptance всё ещё проверяет Producer.

---

## 8. Stuck detection

Не использовать правило:

`N минут → kill`.

Смотреть сочетание evidence:

- heartbeat/status;
- terminal activity;
- provider error;
- file/commit progress;
- blocking question;
- Dispatch state.

Различать:

- worker работает;
- worker ждёт answer;
- provider rate-limited;
- provider quota exhausted;
- auth/capacity/network failure;
- quality failure;
- task реально stuck.

Restart/fallback только после diagnosis.

---

## 9. Worktree lifecycle

Один Batch → один worker-owned worktree, если нужна изоляция source edits.

После accepted integration:

1. убедиться, что worktree clean;
2. убедиться, что нужные commits в main;
3. закрыть/удалить отработанный worktree;
4. не держать десятки мёртвых terminals/worktrees.

---

## 10. Integration authority

Workers не merge'ят самостоятельно в authoritative main.

Producer управляет landing order.

Reviewer даёт findings, но не становится Integrator.

---

## 11. Dependencies

Orca Task DAG хранит только реальные execution blockers.

Не моделировать каждую смысловую связь как dependency.

Использовать dependencies, когда:

> Task B технически не может начать полезную работу до результата Task A.

---

## 12. Provider failure and work preservation

Provider/quota failure не должен превращаться в дубликат Git Card или потерю работы.

Если Dispatch умер из-за provider:

1. зафиксировать failure reason;
2. сохранить существующий branch/worktree state;
3. проверить commits и dirty diff;
4. завершить authority старого worker как active writer;
5. выбрать новый route по `agents.md`;
6. использовать native Orca retry/new Dispatch semantics, не создавая второй backlog item для той же работы;
7. передать replacement worker original spec + current code state + remaining acceptance.

Если Task/Dispatch state требует recovery/update — использовать native Orca lifecycle, а не собственный task engine.

Не считать `quota exhausted` доказательством дефекта реализации.

## 13. FREE_ONLY operation

Когда paid surfaces недоступны:

- Producer продолжает dispatch всех free-capable Batches;
- senior-only work получает локальный blocker;
- Board не замораживается глобально;
- новые free workers можно запускать в независимых worktrees;
- heavy Unity WIP/resource limits продолжают действовать;
- восстановление paid quota не является причиной прерывать живой free Dispatch.

## 14. Native-first rule

Перед созданием собственного Studio tool проверить, решает ли Orca это уже через:

- Task;
- Dispatch;
- Worktree;
- Board;
- comments;
- terminal state;
- messages;
- questions;
- escalation;
- worker_done.

Если решает — использовать native primitive.
