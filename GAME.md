# GAME — Vertical Slice GDD

> Главный вход нового проекта. Заполни этот файл до запуска `START_PROMPT.md`.
> Описывай только вертикальный срез, который реально должен стать playable. Не проектируй всю будущую игру.

## 1. Working Title

`TODO`

## 2. High Concept

`TODO: 2–5 предложений — что это за игра, что делает игрок и чем опыт отличается.`

## 3. Target Player Experience

Игрок должен чувствовать:

- `TODO`
- `TODO`
- `TODO`

Не должен чувствовать:

- `TODO`
- `TODO`

## 4. Vertical Slice Promise

После запуска slice игрок может:

- `TODO: начать игру/сессию`;
- `TODO: выполнить основной набор действий`;
- `TODO: принять хотя бы одно meaningful decision`;
- `TODO: получить заметный результат или трансформацию`;
- `TODO: завершить slice понятным финалом/следующей целью`.

Ожидаемая длительность одной проверки: `TODO`.

## 5. Core Loop

```text
TODO: observe / choose
↓
TODO: act
↓
TODO: resolve consequence
↓
TODO: reward / transformation / new option
↓
repeat with a new decision
```

## 6. Player Goal and Failure

Primary goal: `TODO`

Success state: `TODO`

Failure/pressure state: `TODO`

Recovery/retry behavior: `TODO`

## 7. Mechanics Included in the Slice

| Mechanic/System | Player-facing purpose | Minimum playable behavior |
|---|---|---|
| `TODO` | `TODO` | `TODO` |

## 8. Content Included

| Content type | Slice budget | Required examples/notes |
|---|---:|---|
| Levels/arenas/rooms | `TODO` | `TODO` |
| Enemies/challenges | `TODO` | `TODO` |
| Items/abilities/resources | `TODO` | `TODO` |
| UI screens/states | `TODO` | `TODO` |
| Narrative content | `TODO` | `TODO or none` |

## 9. Progression and Economy

Start state: `TODO`

Progression steps:

1. `TODO`
2. `TODO`
3. `TODO`

Resources/currencies and sinks:

| Resource | Earned by | Spent/used for | Intended decision |
|---|---|---|---|
| `TODO` | `TODO` | `TODO` | `TODO` |

## 10. UX and Readability

- Required controls/input: `TODO`.
- First-use teaching: `TODO`.
- Critical feedback: `TODO`.
- Required HUD/information: `TODO`.
- Accessibility/readability constraints: `TODO`.

## 11. Art, Audio and Presentation Direction

- Visual target/reference: `TODO`.
- Camera/presentation: `TODO`.
- Required feedback/VFX: `TODO`.
- Required audio/music: `TODO`.
- Acceptable prototype shortcuts: `TODO`.

## 12. Technical Profile

- Engine/Profile: `Unity 6 | Browser/TypeScript`.
- Target platform: `TODO`.
- Input devices: `TODO`.
- Save/persistence required: `no | yes — describe minimum`.
- Existing project/repository state: `TODO`.
- Required integrations/dependencies: `TODO or none`.
- Performance constraints: `TODO`.

## 13. Global Gameplay Invariants

Rules that workers must not silently redefine:

- `TODO`
- `TODO`
- `TODO`

## 14. Scope and Non-goals

The vertical slice includes:

- `TODO`
- `TODO`

Do not build now:

- `TODO`
- `TODO`
- `TODO`

## 15. Acceptance

The slice is accepted when:

- [ ] `TODO: clean-start player flow works`.
- [ ] `TODO: core loop can be completed`.
- [ ] `TODO: meaningful decision and consequence are observable`.
- [ ] `TODO: success/failure/retry behavior works`.
- [ ] `TODO: required technical gate passes`.
- [ ] Human completes final fun/pacing/feel verdict.

## 16. Authoritative Truth Map

| Feature/Fact | Authoritative location |
|---|---|
| Vertical slice intent and global rules | `GAME.md` |
| Core progression | `GAME.md until feature-local truth is created` |
| Economy | `GAME.md until feature-local truth is created` |
| Save/Persistence | `TODO path or GAME.md` |
| UI contracts | `TODO path or GAME.md` |
| Narrative/canon | `NARRATIVE.md if persistent canon exists; otherwise GAME.md/Card` |

## 17. Terminology

| Term | Meaning |
|---|---|
| `TODO` | `TODO` |

## 18. Human-only Gates

In addition to Studio-wide gates, Human approval is required for:

- `TODO if any; otherwise none`.
