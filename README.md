# Personal AI Game Studio

Готовый личный production-шаблон для разработки Unity и browser/TypeScript игр через Orca и Git.

Studio оптимизирована под один результат: **accepted playable change** — изменение игры, которое можно запустить, проверить и оценить глазами игрока.

## Быстрый старт

1. Создай новый репозиторий из этого шаблона.
2. Заполни `GAME.md` как GDD вертикального среза.
3. Открой `START_PROMPT.md` и передай его Producer без дополнительных инструкций.

На первом production round Producer сам:

1. читает обязательное ядро Studio и GDD;
2. проверяет репозиторий и текущий Orca state;
3. формулирует первый Player Outcome;
4. создаёт Outcome и Atomic Cards в Git;
5. собирает Cards в context-local Batches;
6. выбирает готовый route по `agents.md`;
7. запускает независимые workstreams через Orca;
8. интегрирует и проверяет playable result.

Никакой настройки ролей или model routes перед стартом не требуется.

## Готовые defaults

- **Execution:** Orca Task, Dispatch, Workspace/worktree и Board.
- **Durable truth:** Git.
- **Free workers:** `OpenCode → OmniRoute → eligible free model`.
- **Escalation:** stronger free route, затем Codex/Claude только по capability или evidence провала.
- **Provider resilience:** при недоступности paid surfaces Studio продолжает free-capable работу в `FREE_ONLY`.
- **Flow:** Kanban-style continuous flow без обязательных Scrum-ритуалов.
- **Parallelism:** по ownership; один active writer на authoritative gameplay fact.
- **Unity:** отдельные coding worktrees разрешены, heavy Editor gates ограничены, scene/prefab имеют exclusive writer.
- **Browser:** strict TypeScript, targeted tests, Playwright только для критичных player flows.

## Как устроена работа

`Human intent → Player Outcome → Atomic Cards → context-local Batches → Orca workstreams → integration → playtest → verdict`

Player Outcome — наблюдаемое изменение опыта игрока. Task, Batch, agent и commit — средства доставки результата, а не метрики успеха.

Если игра работает технически, но она скучная, медленная, непонятная или не даёт решений, это **Design Finding**:

`playtest → finding → diagnosis → hypothesis → change → re-playtest`

## Source of truth

- `GAME.md` — GDD текущего вертикального среза и глобальные design constraints.
- `tasks/outcomes/` — durable Player Outcomes.
- `tasks/open/` — durable Atomic Cards.
- Feature-local документы — детальная design truth конкретных систем.
- `NARRATIVE.md` — persistent canon, только когда он действительно появился.
- Git — code, assets, decisions и backlog.
- Orca — только текущий operational state.

Conversation history не является source of truth.

## Обязательное ядро Producer

Producer перед началом читает:

1. `STUDIO.md` — production rules;
2. `GAME.md` — GDD вертикального среза;
3. `agents.md` — готовая cost/capability policy;
4. `ORCA.md` — execution protocol;
5. `roles/producer.md` — authority и triggers.

Остальные role/workflow документы читаются только когда соответствующая capability или project profile реально нужна.

## Главные ограничения

- Human задаёт direction и принимает major design gates.
- Producer координирует, но не заменяет implementation workers.
- Не запускать максимум агентов ради активности.
- Не держать двух active writers на одном authoritative fact или recovery worktree.
- Не редактировать Unity serialized YAML вручную.
- Не превращать Design Finding в обычный Bug.
- Не добавлять process, role, tool или infrastructure без наблюдаемого failure mode.

Главная метрика: **time to accepted playable change**.
