# ROLE COMMON — Rules for All Workers

Implementation roles также соблюдают `CODE_QUALITY.md`. Не дублируй его правила в каждой Card.

## 1. Work only inside assigned scope

Ты получаешь Batch с:

- Goal;
- Cards;
- Zone;
- Acceptance;
- Non-goals;
- Evidence.

Не расширяй задачу молча.

## 2. Do not invent design or narrative truth

Если gameplay rule не определён или противоречив:

- не угадывай;
- сформулируй конкретный blocking question;
- направь через Producer к Game Designer;
- продолжи после design answer.

Если нужен значимый художественный текст/canon/character voice:

- не вставляй случайный LLM filler;
- запроси Narrative Designer через Producer;
- интегрируй утверждённый content.

Чисто функциональный UI text не требует Narrative handoff.

## 3. Choose implementation mechanism yourself

Если spec задаёт результат, а не механизм — ты имеешь право выбрать локальный technical mechanism.

Но нельзя без approval менять:

- player goal;
- economy intent;
- progression rules;
- persistent state semantics;
- cross-system contracts;
- scope.

## 4. One owner of truth

Не создавай второй источник одного gameplay fact.

Если видишь duplicate ownership — остановись и эскалируй.

## 5. Evidence

Сдача должна содержать доказательство, соответствующее задаче.

Примеры:

- test result;
- before/after measurement;
- screenshot;
- runtime log;
- reproduction steps;
- exact changed behavior.

`Tests green` не заменяет player-facing acceptance.

## 6. Git

- Работай в своём assigned worktree.
- Делай осмысленные commits.
- Не merge main самостоятельно.
- Не оставляй случайный dirty state.
- Не коммить generated/cache files, если Project Profile это запрещает.
- Не трогай запрещённые zones.

## 7. Process discipline

Не создавать:

- новый framework;
- monitoring;
- reusable abstraction;
- migration;
- documentation subsystem;

если это не требуется Batch spec и не устраняет конкретный наблюдаемый failure.

## 8. Unity

Если Project Profile = Unity:

- не редактируй `.unity` / `.prefab` / Unity serialized YAML вручную;
- учитывай `.meta`;
- не шарь mutable Library между active worktrees;
- serialized asset может иметь exclusive writer.

Подробности: `workflows/unity.md`.

## 9. Status

Обновляй Workspace comment на значимых checkpoints:

- started/reproduced;
- implementation complete;
- validating;
- blocked;
- ready for review.

Не спамь статусами.

## 10. Completion

Перед `worker_done`:

- проверь acceptance;
- собери required evidence;
- укажи commits;
- укажи известные ограничения;
- не скрывай незакрытые findings.
