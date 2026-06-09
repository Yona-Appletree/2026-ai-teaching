# Testing Strategy

## Current State

The repo does not currently have a test framework configured. `package.json` exposes:

- `dev`
- `build`
- `lint`
- `preview`

There are no visible `*.test.*`, `*.spec.*`, `vitest.config.*`, `playwright.config.*`, or Storybook files.

This is the clearest first improvement. The game rules are unusually suitable for tests because much of the logic is pure or nearly pure:

- `src/core/rng/rng.ts`
- `src/core/rng/dice.ts`
- `src/reducers/reduceGame.ts`
- `src/reducers/steps/phaseGating.ts`
- `src/systems/cards/*`
- `src/systems/combat/*`
- `src/systems/relics/*`
- `src/systems/rewards/*`
- `src/systems/events/*`

## Why Testing Matters Here

Game projects invite regressions because rules interact combinatorially. A change to a relic can affect card play, turn start, combat end, reward flow, and animations. Without tests, agents must rely on local inspection and manual playthroughs.

Tests would give you three benefits:

- Confidence to add new cards, relics, enemies, and rooms.
- Faster feedback than playing through the game.
- Better agent performance, because agents can run a narrow command and know whether behavior still holds.

## Recommended Layers

### 1. Pure Unit Tests

Start with deterministic helpers and small rule functions.

Good first targets:

- RNG sequence from a fixed seed.
- Dice rolls are deterministic and stay in range.
- Card ink cost with exhausted, disabled, cost override, and free-fire-spell modifiers.
- Foil and upgrade scaling.
- Shield, firepower, poison, and critical calculations.
- Reward loot collection gates.
- Path lock determination.

Suggested tools:

- `vitest`
- `@vitest/coverage-v8`

Example script names:

```json
{
  "scripts": {
    "test": "vitest run",
    "test:watch": "vitest",
    "coverage": "vitest run --coverage"
  }
}
```

### 2. Reducer Scenario Tests

The reducer pipeline is the heart of the game:

```text
action -> applyAction -> resolveEventQueue -> deriveAnimationsFromEvents -> phase updates
```

Tests can construct a `GameState`, dispatch actions, and assert the resulting phase, deck, player stats, combat state, and emitted event-driven state.

Good scenarios:

- Boot assets ready sends the player to title.
- New game creates starter relic choices and starter deck.
- Choosing a starter relic advances to path selection.
- Illegal actions do not mutate state.
- Playing an unaffordable card does nothing.
- Playing a card spends ink, applies effects, moves the card zone, and returns to `COMBAT_PLAYER_READY`.
- Defeating an enemy produces victory and reward flow.
- Mystery rooms enforce their local proceed gates.

These tests should use fixtures/builders so individual tests read like domain examples, not giant state blobs.

### 3. Catalog Validation Tests

The data catalogs are a form of content database. Add validation tests that scan `src/data/*` and catch broken references.

Examples:

- Every card id matches its object key.
- Every referenced effect kind is supported by the resolver or intentionally metadata-only.
- Every relic trigger references supported trigger names.
- Every enemy intent id exists.
- Every image filename referenced by templates resolves through the render asset maps.
- Every path id has legal frequency/minimum-level settings.

These tests are boring in the best way. They catch agent mistakes very early.

### 4. UI Component Stories

Use Storybook for visual states rather than trying to drive the whole game.

Good first stories:

- `Card`: normal, upgraded, exhausted, disabled, foil, socketed, potion, long text, keyword tooltip.
- `RelicIcon`: normal, disabled, rejected, tooltip states.
- HUD components: low HP, high shield, keys/gold/gems/relics.
- Reward screens: card reward, relic reward, uncollected loot, collected loot.
- Combat screen fixtures: player ready, resolving, defeated enemy, disabled card, hand selection.
- Mystery rooms: Font of Lethe, Printer, Collector.

Storybook gives you a professional UI workflow without requiring complex browser automation at first.

### 5. Browser Smoke Tests

After unit and story coverage, add a tiny Playwright smoke suite.

Useful smoke tests:

- The app loads and reaches title after assets are ready.
- New game can start.
- A seed-driven helper or test-only route can put the app into a known combat state.
- Basic interactions do not throw console errors.

Do not start with exhaustive end-to-end game tests. They are slower, more brittle, and less useful than rule-level tests at this stage.

## Suggested First Milestone

Add Vitest and write 10 to 15 tests:

- RNG/dice: 3 tests.
- Card cost/effect scaling: 4 tests.
- Phase gating: 3 tests.
- One reducer scenario for new game.
- One reducer scenario for an illegal action.
- One catalog validation pass.

That is enough to teach the habit and create a template for future agent work.

## Agent Prompt

```text
Add Vitest to this Vite React TypeScript repo. Start with focused tests for deterministic RNG, dice, card ink cost, phase gating, and one reducer scenario. Keep test helpers small and place them near the tests. Do not refactor production code unless a small export is needed for testing.
```
