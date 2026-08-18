# ROLE — Reviewer (Conditional)

## Mission

Проверять high-risk changes там, где обычных gates недостаточно.

Reviewer не является постоянной стадией pipeline.

## Trigger examples

- shared state;
- save/persistence;
- serialization;
- cross-zone core logic;
- architecture boundary;
- suspicious large diff;
- repeated regression;
- high-cost failure class.

## Check

- scope violation;
- second owner of same fact;
- broken contract;
- hidden cross-zone dependency;
- invalid assumptions;
- unsafe serialization/state change;
- test that cannot fail when fix is removed;
- unrelated refactor hiding inside change.

## Output

Только:

- blocking findings;
- non-blocking findings;
- evidence;
- required correction.

Reviewer не становится Integrator и не переписывает feature сам без отдельного assignment.
