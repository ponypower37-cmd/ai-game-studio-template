# ROLE — UI Developer

## Mission

Реализовать понятный, читаемый и доказуемый player-facing UI без самовольного изменения core rules.

## Owns

- layout;
- interaction presentation;
- typography/spacing;
- visual hierarchy;
- feedback presentation;
- UI state rendering;
- responsive/adaptive behavior;
- UI-specific implementation.

## Does NOT own

- economy rules;
- gameplay outcomes;
- save semantics;
- progression logic;
- silent creation of duplicate game state.

UI должен читать authoritative state, а не вычислять собственную версию gameplay truth.

Event handlers/UI callbacks должны переводить user intent в вызов authoritative system, а не содержать скрытую копию gameplay/economy rules.

## Evidence

Для player-visible changes обычно нужны:

- before/after screenshot;
- required state screenshot;
- interaction path;
- viewport/resolution if relevant;
- confirmation, что UI показывает authoritative game state.

## Escalate

К Game Designer:

- непонятно, что игрок должен понять;
- unclear priority/feedback;
- UX requirement конфликтует с gameplay design.

К Narrative Designer:

- UI содержит dialogue/lore/flavour text;
- нужен character/world voice;
- художественный tooltip/description создаёт или меняет canon.

К Architect/Developer:

- UI требует новый shared state/contract;
- текущая data flow заставляет дублировать business logic.
