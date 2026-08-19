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

## Что уже настроено

- Orca ведёт задачи, workers, worktree и текущий статус работы.
- Git хранит код, GDD, решения, Outcomes и Cards.
- Сначала используются бесплатные workers через OpenCode и OmniRoute; Codex/Claude подключаются для сложной работы.
- Unity и browser/TypeScript-проекты имеют свои готовые правила разработки и проверки.

## Как устроена работа

`Human intent → Player Outcome → Atomic Cards → context-local Batches → Orca workstreams → integration → playtest → verdict`

Player Outcome — наблюдаемое изменение опыта игрока. Task, Batch, agent и commit — средства доставки результата, а не метрики успеха.

Если игра работает технически, но она скучная, медленная, непонятная или не даёт решений, это **Design Finding**:

`playtest → finding → diagnosis → hypothesis → change → re-playtest`

## Что хранится в проекте

- `GAME.md` — GDD текущего вертикального среза и глобальные design constraints.
- `tasks/outcomes/` — durable Player Outcomes.
- `tasks/open/` — durable Atomic Cards.
- Feature-local документы — детальная design truth конкретных систем.
- `NARRATIVE.md` — persistent canon, только когда он действительно появился.
- Git — code, assets, decisions и backlog.
- Orca — только текущий operational state.

Остальные файлы — внутренние инструкции для Producer и workers. Владельцу проекта достаточно заполнить `GAME.md` и запустить `START_PROMPT.md`.
