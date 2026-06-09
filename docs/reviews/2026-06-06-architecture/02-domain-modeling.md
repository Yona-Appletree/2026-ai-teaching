# Domain Modeling

## Current Strength

The game already has a meaningful domain model. It is visible in:

- `src/core/types/state.ts`
- `src/core/types/ids.ts`
- `src/data/effects.ts`
- `src/data/cards.ts`
- `src/data/relics.ts`
- `src/data/enemies.ts`
- `src/data/paths.ts`
- `src/reducers/actions.ts`
- `src/reducers/events.ts`

The best thing about this architecture is that many rules are data-driven. Cards, relics, enemies, paths, and mystery rooms are not hard-coded only in React components. That is exactly the kind of shape agents can extend well.

## Main Opportunity

The domain concepts exist, but they are not yet explained as a domain language.

An agent can see the types, but it still has to infer:

- The difference between a card template and a card instance.
- The difference between card zones and deck ownership.
- Which effects are card-play effects, pickup effects, relic effects, or enemy effects.
- Which rules belong in `data`, `systems`, `reducers`, `render`, or `runtime`.
- What an action is allowed to mutate directly.
- What an event means, and whether it is gameplay, animation, logging, or all three.
- How phases relate to screen state and combat lifecycle.
- Which animations are domain-blocking versus render-local flourish.

That is not a failure. It is normal for a fast-moving game. But it is exactly where a short domain guide would pay off.

## Recommended Domain Docs

Create:

```text
docs/architecture/domain-model.md
docs/architecture/game-loop.md
docs/architecture/content-catalogs.md
docs/architecture/animations-and-render-effects.md
```

Keep each file short. The point is to help a new contributor or agent orient quickly.

## Suggested Domain Glossary

### Card Template

A catalog entry in `src/data/cards.ts`.

Defines the stable card identity:

- id
- name
- cost
- tags
- effects
- starter/reward frequency
- potion/upgrade/socket flags

### Card Instance

A concrete owned card in `GameState.player.deck.cardById`.

Tracks per-run/per-card state:

- instance id
- template id
- upgrades
- exhausted/disabled state
- socketed gem
- foil
- combat-ephemeral flags

### Effect

A declarative game effect in `src/data/effects.ts`.

Effects are the shared vocabulary for cards, relics, enchantments, and some enemy behavior. Because this union is central, new effect kinds should be added with tests and clear resolver ownership.

### Action

A player or system command in `src/reducers/actions.ts`.

Actions are inputs to the reducer. Player actions should pass through phase gating. System actions should represent completion of visual or queued work.

### Phase

The coarse game state machine in `src/core/types/state.ts`.

Phases decide what screen is visible and what actions are legal. `COMBAT_RESOLVING` and `ANIMATING` are especially important because they protect the game from accepting inputs while consequences are still resolving.

### Event

A gameplay consequence emitted by systems and consumed by event resolution, animation derivation, debug display, and game logging.

Events should be treated as domain facts, not arbitrary UI callbacks. If an event exists only to make one component sparkle, it may belong in render-local state instead.

### Render Effect

A visual effect owned by React providers or animation contexts.

Examples include card travel, gold travel, socket flip, cast burst, fire release, poison hit, and similar effects. Many are driven by state changes or event batches, but they should not become hidden game-rule owners.

## Modeling Improvements

### Make Catalog Boundaries Explicit

The data files are strong, but each catalog should say:

- What the catalog owns.
- Which fields are gameplay rules.
- Which fields are display/render hints.
- Which fields are temporary or debugging-only.
- Which invariants tests should enforce.

This could be done with TSDoc above each template type and one `docs/architecture/content-catalogs.md` file.

### Clarify Effect Ownership

`Effect` is one of the most important domain types. It would help to classify effect kinds:

- card-play effects
- pickup effects
- relic-trigger effects
- combat-only effects
- non-combat effects
- metadata/keyword effects

This could be encoded as docs first, then perhaps tests. Do not over-engineer it into a heavy runtime schema unless needed.

### Use Builders For Domain Examples

Tests and Storybook will need stable state examples. Add small builders like:

- `makeTestState`
- `withPlayerDeck`
- `withCombat`
- `makeCardInstance`
- `makeEnemyInstance`

These should live under test utilities at first. If they become useful production factories, graduate them carefully.

### Consider Richer Domain Subdirectories Later

The project currently separates by technical layer. That is fine. If it keeps growing, consider feature/domain grouping for larger areas:

```text
src/domain/cards/
src/domain/combat/
src/domain/relics/
src/domain/rooms/
src/domain/progression/
```

This is a future option, not a required rewrite. A gentle middle step is adding `README.md` files to existing folders.

## Agent Prompt

```text
Write docs/architecture/domain-model.md for this game. Explain the domain terms used by the code: card template, card instance, deck zones, effect, relic trigger, action, event, phase, render effect, and catalog. Reference the existing source files and keep it concise enough for a coding agent to read before making changes.
```
