# PILOT — What Must Be Measured

Не превращать предположения в вечные правила до реальной эксплуатации Studio.

## 1. Batch sizing

Измерить отдельно для:

- Browser Prototype;
- Unity.

Собирать:

- Batch Weight;
- number of Cards;
- context reloads;
- worker completion;
- rework;
- time to accepted result.

Цель — найти диапазон, после которого Batch начинает терять качество/контекст.

Не импортировать чужое правило `15–20` без собственных данных.

---

## 2. Parallelism

Измерить:

- active coding workers;
- conflict rate;
- CPU/RAM pressure;
- provider saturation;
- Producer waiting;
- Unity gate contention.

Выход:

- global WIP;
- per-zone WIP;
- Unity execution slots.

---

## 3. Free-first routing

Измерить:

- free success rate;
- average retries before success;
- free→paid escalation;
- rework caused by weak free model;
- time saved/lost;
- premium interventions per Outcome.

Free-first считается полезным только если он снижает cost **без непропорционального роста time-to-playable-change**.

---

## 4. Provider outage / FREE_ONLY drill

До того как считать resilience решённой, один раз специально проверить сценарий:

`Claude unavailable + GPT/Codex unavailable`

Во время реального Outcome проверить:

- продолжаются ли free-capable Batches;
- блокируются ли только senior-required tasks;
- теряется ли in-progress work;
- сколько занимает handoff на free fallback;
- создаются ли дубли Cards/Tasks;
- не происходит ли случайный paid fallback вопреки policy;
- не пытается ли Producer бесконечно перезапускать exhausted provider.

Успех:

- Studio не останавливается глобально;
- ready free work продолжает идти;
- изменения worker'а не теряются;
- blocked senior work остаётся явно видимым и возобновляемым.

---

## 5. Senior leverage

Проверить hypothesis:

> senior paid model эффективнее как architect/diagnostician/reviewer, чем как единственный implementation worker всей feature.

Сравнивать:

- senior solo;
- senior plan + cheap workers;
- cheap only.

---

## 6. Unity costs

Измерить:

- import;
- compile;
- tests;
- build;
- screenshots;
- playtest setup;
- 1 vs 2 concurrent heavy Unity processes.

Accelerator добавлять только после доказанного import bottleneck.

---

## 7. Browser profile costs / reliability

Измерить на реальных prototype Outcomes:

- typecheck + lint wall time;
- production build wall time;
- targeted E2E wall time;
- Playwright flake/retry rate;
- console/page errors, которые ловит integrated gate;
- bugs из background/resume lifecycle;
- save/load/versioning failures;
- необходимость production sourcemaps/trace-on-failure для диагностики.

Не добавлять browser farm, bundle budgets или performance instrumentation до реального bottleneck.

---

## 8. QA / Playtests

Измерить:

- findings per tester;
- duplicate findings;
- false findings;
- unique bugs;
- unique design findings;
- benefit от 1/2/3/5 parallel testers.

Количество testers должно зависеть от marginal information gain, а не от красивого фиксированного числа.

---

## 9. Context pack

Проверить:

- достаточно ли `_common + role + Batch + relevant truth + code`;
- сколько раз worker просит missing context;
- сколько context он читает зря;
- появляется ли design drift.

Если контекста не хватает — расширять **локально**, а не давать каждому весь GDD.

---

## 10. Primary metrics

Для каждого accepted Outcome:

- `time_to_playable_change`;
- `worker_launches`;
- `reopen_or_rework`;
- `integration_conflicts_or_failures`;
- `human_interventions`.

Diagnostic при проблемах:

- tokens/cost;
- gate duration;
- provider failures;
- free→paid escalations;
- QA false positives.

---

## 11. Kill / simplify signals

Если наблюдается:

- process work > game work;
- Producer bottleneck;
- много restarts;
- process-only backlog;
- manual board maintenance;
- gates длиннее типичной правки;
- docs растут быстрее production;

не строить новый subsystem автоматически.

Сначала удалить или упростить правило, которое создаёт overhead.
