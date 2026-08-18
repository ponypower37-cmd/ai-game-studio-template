# WORKFLOW — Browser Prototype Profile

Browser Prototype использует тот же production protocol, что Unity:

`Outcome → Cards → Batch → Implementation → Integration → Playtest`

Цель профиля — быстрый prototype, который остаётся достаточно типобезопасным, тестируемым и диагностируемым, чтобы AI-workers не ломали его быстрее, чем развивают.

Общие engineering rules: `CODE_QUALITY.md`.

## 1. Default architecture

Для gameplay-heavy prototype по умолчанию разделяй:

- **domain/simulation state** — authoritative rules and state;
- **render/UI layer** — отображает state;
- **input/adapters** — переводят browser/user events в intents/commands;
- **storage boundary** — save/load/versioning;
- **platform boundary** — DOM, audio, browser lifecycle, external APIs.

Не требуется сложный framework или ECS.

Hard rule:

> Gameplay rule не должен существовать только внутри DOM event handler/component render function.

Core gameplay, economy и progression должны по возможности тестироваться без реального browser DOM.

---

## 2. TypeScript baseline

Для **нового TypeScript prototype** default:

- `strict: true`;
- `noUncheckedIndexedAccess: true`;
- `exactOptionalPropertyTypes: true`;
- `noImplicitReturns: true`;
- `noFallthroughCasesInSwitch: true`.

Не включай новую strict option посреди legacy project как побочный refactor текущей Card. Если существующий проект не проходит правило — отдельная migration Card только при доказанной пользе.

### `any`

В core gameplay/domain code `any` запрещён по умолчанию.

На границах:

- external JSON;
- browser storage;
- network/API;
- third-party untyped data;

принимай `unknown`/raw data, затем validate/narrow и только после этого превращай в domain type.

Type assertion (`as SomeType`) не является runtime validation.

---

## 3. Game time / frame loop

### Rendering / animation

Используй `requestAnimationFrame` для frame-driven browser rendering/animation.

- используй timestamp/delta, переданный browser;
- не предполагай фиксированные 60 Hz;
- не привязывай скорость игры к числу render frames.

### Background tabs

Browser может останавливать `requestAnimationFrame` и throttling timers в hidden/background tab.

Поэтому game/economy, которая должна корректно переживать background/resume, не может считать elapsed time только количеством callbacks.

Для таких систем:

- сохраняй authoritative timestamp/state;
- реагируй на `visibilitychange` там, где это имеет смысл;
- на resume вычисляй реальный elapsed interval и применяй правила игры;
- покрывай background/resume отдельным acceptance, если это важно для игры.

Особенно важно для incremental/idle mechanics.

---

## 4. Save / persistence

Save state должен иметь:

- явный schema/version;
- один storage owner;
- validation при чтении;
- migration или сознательный reset policy при несовместимой версии;
- regression test для значимых save bugs.

Не размазывай прямые вызовы `localStorage`/IndexedDB по gameplay code.

Выбирай simplest storage, достаточный для проекта. Не вводи IndexedDB только потому, что он мощнее.

Для automatic save не полагайся исключительно на `unload`/`beforeunload`; lifecycle/save strategy должна учитывать `visibilitychange`/обычные periodic or event-driven saves, если потеря состояния критична.

---

## 5. Typical worker gate

В зависимости от stack:

- TypeScript typecheck;
- formatter/lint, если уже настроены;
- targeted unit tests;
- local browser run для player-visible behavior;
- console/page error check;
- evidence конкретной Card.

Не добавляй lint rule только ради style preference.

---

## 6. Integrated gate

Минимум для integrated Outcome по риску:

- clean production build;
- no blocking runtime/console/page errors;
- selected deterministic/unit tests;
- smoke path по критичному flow;
- screenshots для player-visible changes;
- browser playtest.

Project Profile должен явно знать production command и target browsers. Не считать dev-server proof эквивалентом production build.

---

## 7. E2E / Playwright policy

E2E используется для **критических player flows**, а не для покрытия каждого UI элемента.

Предпочитать:

- role/accessible/test-id locators вместо brittle CSS chain;
- web-first assertions вместо ручных sleep/timeouts;
- deterministic starting fixtures/state;
- trace/screenshot/video primarily on failure/retry, а не постоянно.

Хорошие E2E candidates:

- boot → playable state;
- save/load critical path;
- purchase/unlock that crosses UI + gameplay state;
- regression, который невозможно надёжно поймать дешёвым domain test.

Если E2E дублирует простой pure-function test и стоит в 20 раз дороже — оставь дешёвый test.

---

## 8. UI changes

Для UI/UX:

- фиксировать viewport для reproducible evidence;
- проверять interaction states, а не только static page;
- UI отображает authoritative game state;
- event handler не должен повторять business/game rules;
- browser console errors считаются failure evidence, даже если экран визуально выглядит правильно.

---

## 9. Performance

Не строить performance subsystem заранее.

Когда есть реальный hotspot:

1. воспроизвести scenario;
2. измерить browser Performance tools/API;
3. исправить узкое место;
4. дать before/after measurement.

В frame hot path избегай очевидного ненужного churn: повторного создания больших массивов/объектов, expensive DOM query/layout work и других операций, которые легко вынести из per-frame loop. Но не создавай pooling/cache architecture без измерения.

Если rendering/DOM становится bottleneck, это отдельная performance Card, а не повод заранее переписать prototype на Canvas/WebGL/Worker.

---

## 10. Game design

Browser prototype не освобождает от design iteration.

Если prototype технически работает, но скучен/непонятен — это Design Finding, а не «прототип готов».

---

## 11. What NOT to build by default

Не добавлять до наблюдаемой необходимости:

- browser farm;
- full visual regression suite;
- state management framework только ради architecture fashion;
- runtime schema framework для каждого внутреннего object;
- web worker/threading;
- service worker/PWA/offline cache;
- complex telemetry;
- bundle-size bureaucracy.

Добавлять только когда конкретный failure/bottleneck уже существует.
