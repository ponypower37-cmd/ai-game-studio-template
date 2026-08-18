# WORKFLOW — Design Iteration

Используется для проблем, которые не являются обычным software bug.

## Trigger examples

- «скучно»;
- pacing плохой;
- игрок не понимает цель;
- mechanic работает, но не fun;
- progression перестаёт давать новое;
- feedback слабый;
- economy математически работает, но ощущается тяжело;
- core loop проходит tests, но не работает как игра.

## Flow

```text
Evidence
↓
Design Finding
↓
Game Designer diagnosis
↓
Testable hypothesis
↓
Human gate? only if major
↓
Change Cards
↓
Producer Batch
↓
Implementation
↓
Integrated Build
↓
Re-playtest
↓
Verdict
```

## Diagnosis rules

Не перепрыгивать сразу:

`симптом → случайный фикс`.

Пример:

Плохо:

> Игроку скучно → income ×2.

Лучше:

1. Что именно повторяется?
2. Сколько длится участок без нового решения?
3. Есть ли новый mechanic/reward?
4. Где ожидание перестаёт быть anticipation и становится dead time?
5. Это economy problem, progression problem, feedback problem или core-loop problem?

## Hypothesis

Хорошая hypothesis:

- минимальна;
- observable;
- reversible;
- имеет acceptance;
- не требует переписать пол-игры без необходимости.

## Persistent truth

Если hypothesis после playtest стала новым постоянным rule — сохранить её в authoritative feature truth / `GAME.md` по locality.
