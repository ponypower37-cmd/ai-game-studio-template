# AGENTS — Capability, Cost, Routing and Provider Resilience

## 1. Главный принцип

Producer выбирает не «самую сильную модель», а:

> самую дешёвую разрешённую модель, которая достаточно сильна для конкретной задачи.

Owner может вручную override'ить любой Batch.

Статическая role architecture не привязана к конкретным моделям: providers и модели могут меняться без переписывания ролей.

---

## 2. Execution surfaces

### OpenCode + OmniRoute

Основной **FREE worker pool**.

Использовать как default путь к бесплатным моделям и бесплатным fallback routes.

Studio policy — **free-first**. Не полагаться на economic defaults router'а, если они сначала расходуют subscription/paid quota. Настройка route должна отражать policy Studio.

### Claude

Paid/high-quality surface.

Использовать для задач, где free route не соответствует capability/quality policy, и для senior leverage.

### GPT / Codex

Paid/high-quality surface.

Использовать по тем же принципам: не как default bulk worker, а когда capability/quality оправдывает расход.

Другие surfaces можно добавлять только при реальном capability/cost benefit.

---

## 3. Cost ladder

### Tier 0 — FREE

Default:

`OpenCode → OmniRoute → eligible free model`

Использовать для всей работы, которую free model способна закрыть с приемлемым rework rate:

- обычный implementation;
- простые/средние bugs;
- content/data;
- tests;
- refactors в чёткой scope;
- UI implementation при наличии нужных capabilities;
- bulk narrative/content по уже установленным правилам;
- повторяемые production tasks.

### Tier 1 — PAID NORMAL

Использовать, когда:

- нет eligible free capability;
- free attempts доказанно не справились;
- cost очередного free retry уже выше разумного;
- задача требует более надёжного reasoning.

### Tier 2 — PAID SENIOR

Использовать для leverage:

- fundamental architecture;
- hard cross-system contracts;
- complex game-design diagnosis;
- сложное narrative foundation / canon architecture;
- difficult debugging;
- high-risk review;
- decomposition сложной системы;
- разблокировка провалившихся cheap workers.

Senior paid model **не должна по умолчанию писать всю feature/проект в одиночку**.

Предпочтительно:

`Senior diagnoses/plans/contracts → cheap workers implement → senior reviews only if needed.`

### Tier 3 — OWNER GATE

Если Batch фундаментальный, особенно дорогой или route помечен manual-only — Producer запрашивает Owner.

---

## 4. Capability model

Role file описывает required capabilities, а не конкретную модель.

### Developer

- strong code reasoning;
- repository edit;
- tests;
- language/toolchain compatibility.

### UI Developer

- code;
- visual reasoning;
- screenshot/image understanding when relevant.

### QA / Playtester

- browser/game control;
- vision;
- evidence reporting.

### Architect

- strong system reasoning;
- cross-system design;
- codebase reading;
- contract definition.

### Game Designer

- systems reasoning;
- economy/progression reasoning;
- design diagnosis;
- game-feel/pacing analysis.

### Narrative Designer / Writer

- long-range consistency;
- canon tracking;
- voice/character consistency;
- dialogue/lore writing;
- ability to follow gameplay constraints instead of inventing mechanics.

Для bulk narrative не требуется premium автоматически. Premium полезен прежде всего для foundation, difficult continuity diagnosis и high-stakes key scenes.

---

## 5. Free escalation ladder

Не использовать:

`free failed → сразу premium`.

Использовать:

1. Free attempt #1.
2. Provider/network/rate failure → free fallback/retry.
3. Capability mismatch → stronger eligible free route.
4. Spec/gameplay ambiguity → Game Designer/Architect, а не более дорогой implementation model.
5. Narrative/canon ambiguity → Narrative Designer, а не случайный stronger coding model.
6. Repeated quality failure → paid-normal или senior diagnosis.
7. Senior по возможности возвращает implementation обратно cheap worker.

Provider failure и quality failure — разные причины.

`429 / quota / auth / capacity / transport failure` не доказывают, что модель «слишком слабая».

---

## 6. Caveman policy

Для платных Claude/GPT workers Caveman — default, если совместим с используемым surface/session.

Но Caveman не имеет права повреждать точные данные:

- Batch spec;
- acceptance criteria;
- JSON/config;
- interfaces/contracts;
- exact formulas;
- code;
- critical design truth;
- canon facts.

Цель — уменьшить conversational/context overhead, а не потерять информацию.

Если конкретный agent требует per-session activation — активировать в начале paid session.

---

## 7. Routing fields

Для route/model можно поддерживать статическое описание:

```yaml
id: example-free-code
surface: opencode
cost_tier: free
capabilities:
  code_reasoning: strong
  vision: false
  computer_control: false
roles:
  - developer
priority: 10
requires_owner_approval: false
caveman: false
max_quality_attempts_before_escalation: 2
```

Для premium:

```yaml
id: example-senior
surface: claude
cost_tier: premium
capabilities:
  code_reasoning: very_strong
  architecture: very_strong
roles:
  - architect
  - reviewer
requires_owner_approval: true
caveman: true
```

### Не хранить volatile provider status в Git

`available / exhausted / rate-limited / degraded` — runtime state.

Не коммить в `agents.md`:

```yaml
status: exhausted
retry_after: ...
```

Такая информация быстро устаревает.

Producer проверяет availability/quota при планировании wave и держит её в текущем operational context/Orca state.

---

## 8. Routing decision

Для каждого Batch Producer:

1. определяет required capabilities;
2. исключает routes, нарушающие Owner policy;
3. проверяет runtime availability;
4. сортирует eligible routes по cost;
5. выбирает cheapest route с достаточной capability;
6. сохраняет fallback ladder;
7. эскалирует только по evidence.

Не отправлять trivial Batch senior-модели только потому, что quota сейчас доступна.

---

## 9. Provider resilience modes

### NORMAL

Free pool доступен; paid routes могут использоваться по policy.

### FREE_ONLY

Включается, если:

- Owner явно требует free-only;
- Claude/GPT quota исчерпаны;
- paid surfaces недоступны/сломаны;
- budget policy временно запрещает paid.

В `FREE_ONLY`:

- все compatible Ready Batches продолжаются через free pool;
- Producer может переключать free providers/models;
- senior-required Batch становится локально `blocked: senior capability unavailable`;
- другие Outcomes/Batches не блокируются;
- Producer ищет безопасную декомпозицию blocked feature на независимые free-capable части;
- нельзя тихо включать pay-per-token route вопреки budget policy.

### SENIOR_REQUIRED / BLOCKED

Используется только для конкретного Batch, если free routes не имеют необходимой hard capability или повторно провалили quality acceptance.

Это **не состояние всей Studio**.

### Exit from FREE_ONLY

Когда paid quota вернулась:

- не прерывать живых free workers;
- не мигрировать уже успешно исполняющийся Batch ради «более сильной модели»;
- возвращать blocked senior work в Ready на следующем естественном scheduling point.

---

## 10. Mid-task quota failure / failover

Если paid worker исчерпал quota посреди Batch:

1. диагностировать provider failure;
2. не удалять worktree и не терять diff;
3. зафиксировать текущие commits/dirty state/evidence;
4. убедиться, что старый worker больше не является active writer;
5. выбрать fallback route;
6. новый worker получает original Batch spec, acceptance, current branch state, commits/diff, completed work, remaining work и failure reason;
7. продолжить существующую работу вместо старта с нуля.

Если безопасно — новый worker продолжает тот же worktree после завершения/смерти старого terminal/Dispatch.

Если reuse небезопасен — создать replacement worktree от последнего надёжного commit и перенести только проверенные изменения.

Никогда не держать двух active writers одновременно на одном recovery worktree.

---

## 11. Protect scarce paid capacity

Premium capacity не использовать на:

- backlog triage;
- task formatting;
- простую документацию;
- мелкий UI polish;
- trivial bugs;
- monitoring;
- routine tests;
- массовый однотипный content;
- bulk item descriptions после того, как narrative voice уже определён.

Premium должен увеличивать leverage дешёвых workers, а не заменять их.

---

## 12. Narrative cost pattern

Для narrative-heavy project:

`Senior narrative foundation (если нужен) → compact NARRATIVE.md/rules/examples → free/cheap bulk generation → consistency review`.

Не оплачивать senior-моделью сотни однотипных описаний, если задача уже формализована.

---

## 13. Pilot metrics for routing

Измерять:

- free success rate;
- free→paid escalation rate;
- rework rate по route;
- paid interventions per accepted Outcome;
- provider outage count;
- recovery/handoff time;
- work lost/repeated after provider failure;
- cases where expensive model could have been avoided.

Tokens/cost — diagnostic metric, не самостоятельная цель.

Если free-first увеличивает `time_to_playable_change` из-за постоянного rework, policy пересматривается.
