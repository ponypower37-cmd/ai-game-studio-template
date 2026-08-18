# AI Game Studio — Orca Production Pack

Финальный минимальный production-пакет для разработки Unity и browser/TypeScript игр несколькими AI-agents через Orca.

## Цель

Studio должна ускорять выпуск **accepted playable changes**, а не увеличивать количество задач, документов или запущенных agents.

Основной flow:

`Human Intent → Player Outcome → Atomic Cards → Batch → Orca Task/Workspace → Implementation → Integration → QA/Playtest → Design/Narrative Verdict → next Outcome`

## Runtime stack

- **Orca** — execution layer: Run, Task, Dispatch, Workspace/worktree, Board, terminal lifecycle.
- **Git** — durable source of truth для code, design truth, canon и backlog.
- **OpenCode + OmniRoute** — основной бесплатный worker pool.
- **Claude / GPT-Codex** — платные senior/escalation capabilities по `agents.md`.
- **Caveman** — default для paid Claude/GPT sessions, когда совместим, без повреждения точных specs/contracts.

## Provider resilience

Studio проектируется так, чтобы исчерпание Claude/GPT quota не останавливало production.

Если paid routes недоступны:

`NORMAL → FREE_ONLY`

В `FREE_ONLY` все compatible Ready Batches продолжают выполняться бесплатными workers. Блокируется только конкретная работа, которой действительно нужна senior capability.

In-progress work при provider failure сохраняется и передаётся replacement worker; задача не стартует с нуля без необходимости.

## Roles

- `Producer` — coordination, batching, routing, integration flow.
- `Game Designer` — professional systems/game design, balance, progression, economy, pacing, design diagnosis.
- `Narrative Designer / Writer` — canon, voice, dialogue, lore и consistent player-facing narrative.
- `Architect` — high-risk system contracts и boundaries.
- `Developer` — implementation.
- `UI Developer` — UI implementation и presentation.
- `QA / Playtester` — integrated evidence/findings.
- `Reviewer` — conditional high-risk review.

Отдельных Integrator, Scrum Master, Documentation Agent, Memory Agent и Monitoring Agent v1 нет.

## Быстрый старт

1. Заполни `GAME.md`.
2. Если проект действительно имеет persistent canon — скопируй `templates/NARRATIVE.md` в корень как `NARRATIVE.md`.
3. Настрой текущие free/paid routes в `agents.md` без привязки role architecture к одной модели.
4. Запусти Producer с `START_PROMPT.md`.
5. Producer создаёт/обновляет Outcome и Atomic Cards.
6. Связанные Cards квантуются в context-local Batches.
7. На исполняемый Batch создаётся Orca Task + Workspace/worktree + Dispatch.
8. Orca Workspace Board — единственная визуальная execution board.
9. После integration запускаются risk-appropriate gates и playtest.
10. Gameplay Design Findings идут Game Designer'у; narrative/canon findings — Narrative Designer'у.
11. После acceptance переходи к следующему Outcome.

## Source of truth

- `GAME.md` — короткая constitution игры.
- Feature design truth — рядом с соответствующей системой/документом.
- `NARRATIVE.md` — только durable canon, если он вообще нужен.
- `tasks/open/` — durable atomic backlog.
- Orca — только текущий operational execution state.

Conversation history не является source of truth.

## Что НЕ строить без доказанного failure mode

- собственную Jira/board;
- orchestration framework поверх Orca;
- vector DB / persistent agent memory;
- monitoring daemon;
- custom scheduler;
- dashboards;
- event bus;
- autonomous agent society;
- massive documentation system;
- infrastructure «на будущее».

## Основные файлы

- `STUDIO.md` — production constitution.
- `START_PROMPT.md` — bootstrap Producer.
- `ORCA.md` — использование native Orca primitives.
- `agents.md` — capability/cost routing, Free-Only и provider failover.
- `CODE_QUALITY.md` — compact engineering policy для AI-written code.
- `GAME.md` — template project constitution.
- `PILOT.md` — что измерить до ужесточения правил.
- `roles/` — responsibilities.
- `workflows/unity.md` — Unity gates/worktrees/serialized assets.
- `workflows/browser.md` — TypeScript/browser quality and lifecycle.
- `workflows/design-iteration.md` — gameplay design iteration loop.
- `templates/` — Outcome/Card/Finding и optional `NARRATIVE.md`.

## Правило изменения Studio

Новая policy/script/tool добавляется только если:

1. наблюдался реальный failure mode или один дорогой failure;
2. стоимость failure можно объяснить/измерить;
3. новая мера дешевле повторения проблемы.

Если процесс начинает тормозить playable changes — сначала упрощать Studio, а не добавлять ещё один слой.
