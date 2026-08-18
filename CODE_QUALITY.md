# CODE QUALITY — Shared Engineering Contract

Этот файл содержит только правила, которые повышают надёжность, скорость изменения и читаемость кода для людей и AI-workers.

Это **не** энциклопедия стиля. Отступы, пробелы, переносы и другие механические правила должны контролироваться formatter/linter, а не занимать контекст агента.

## 1. Readability is throughput

Код читается и модифицируется чаще, чем пишется с нуля.

Поэтому:

- имена должны передавать смысл domain, а не форму реализации;
- избегай безликих `Manager`, `Processor`, `Helper`, `Data`, `Node`, `Thing`, если можно назвать конкретную ответственность;
- не используй непонятные сокращения;
- bool должен читаться как утверждение/вопрос (`isReady`, `hasTarget`, `canPurchase`);
- имя метода с side effect должно ясно описывать действие;
- один и тот же gameplay concept должен называться одинаково в code, Card и design truth.

Имена — часть machine-readable context. Плохие имена напрямую увеличивают вероятность неверной правки другим worker.

## 2. One responsibility, one owner

Перед созданием нового class/module/function ответь:

> Какую одну ответственность он владеет и почему существующий owner не должен делать это сам?

Правила:

- не создавай generic abstraction без текущего use case;
- публичный API должен быть минимально достаточным;
- не создавай второй owner одного state/rule;
- UI не должен вычислять собственную версию gameplay truth;
- предпочитай composition явному inheritance ради reuse;
- если новая abstraction не делает следующую реальную правку проще — не создавай её.

Никаких искусственных лимитов вида «класс обязан быть < N строк». Размер — сигнал для review, а не архитектурный закон.

## 3. Make mutation explicit

Код должен позволять быстро понять, где меняется state.

- разделяй query/read и mutation/command;
- избегай скрытых side effects в properties/getters;
- не передавай mutable state через неочевидные global/static channels;
- shared mutable state должен иметь authoritative owner;
- изменения persistent/gameplay state должны проходить через понятный contract.

## 4. Failure semantics must be visible

Не скрывай failure.

- не используй exceptions как обычный gameplay/control flow;
- перехватывай ошибку только там, где можешь осмысленно обработать её или добавить context;
- не проглатывай exception/log error и не продолжай как будто операция успешна;
- expected domain failure (`not enough currency`, `slot occupied`, `invalid move`) должен быть явным результатом/состоянием API, а не случайным exception;
- внешний/untrusted input сначала validate/narrow, потом используй как domain data.

Не вводи обязательный `Result<T>` framework только ради этого правила. Используй самый простой явный contract, подходящий проекту.

## 5. Constants and configuration

Не прячь gameplay/configuration facts в случайных местах кода.

- не оставляй необъяснимые magic numbers для balance, durations, thresholds, paths;
- значение, которое является game tuning/data, должно жить у authoritative data owner;
- техническая локальная константа может оставаться рядом с кодом, если у неё нет самостоятельного design meaning;
- не создавай config-файл для каждого числа только ради «чистоты».

## 6. Comments explain why

Комментарий нужен, когда из кода нельзя восстановить **почему** решение именно такое.

Хорошие причины:

- non-obvious invariant;
- platform workaround;
- measured performance constraint;
- intentional compromise;
- external contract.

Не комментируй то, что уже ясно из имени и структуры. Не создавай prose-дубликат кода.

## 7. Tests protect behavior

Тесты пишутся на сценарии/invariants, а не ради покрытия строк.

Приоритет:

- regression найденного bug;
- deterministic gameplay rule;
- save/load and migrations;
- economy/progression calculation;
- tricky state transition;
- external-data boundary;
- critical player flow, если дешёво автоматизируется.

Не строить test framework, если ручная/интеграционная проверка конкретного поведения дешевле и надёжнее.

## 8. Performance is evidence-driven

Не заниматься speculative optimization.

Но очевидный hot path должен быть написан так, чтобы не создавать заведомо лишнюю работу каждый frame/tick.

Правила:

1. Сначала baseline/profiler/measurement.
2. Оптимизировать доказанный hotspot или очевидный per-frame churn.
3. После изменения дать before/after evidence.
4. Не добавлять pooling/cache/worker/threading без use case.
5. Performance fix не должен создавать более сложный architecture owner без измеримой выгоды.

Platform-specific hot-path rules находятся в `workflows/unity.md` и `workflows/browser.md`.

## 9. Tool-enforced style

Если правило можно надёжно автоматизировать formatter/compiler/linter — автоматизируй его один раз в Project Profile и не заставляй каждого worker помнить prose checklist.

Примеры:

- formatting;
- TypeScript strictness;
- compile warnings/errors;
- unused imports;
- basic naming/style checks, если проект их действительно использует.

Новая lint rule разрешена только если она ловит реальный класс ошибок или снижает review cost. Не превращай linter в архитектуру проекта.

## 10. AI changeability check

Перед сдачей спроси:

- следующий worker поймёт, где authoritative owner этого state/rule?
- имена объясняют domain без чтения половины проекта?
- mutation/failure видны из API?
- не появился ли второй источник истины?
- не добавлена ли abstraction «на будущее»?
- тест/evidence защищает важное поведение, а не implementation detail?

Если нет — исправь локально, не устраивая refactor всего проекта.
