# Lapin Lazuli Architecture Review

Subject repo: [Anthoussae/lapin_lazuli](https://github.com/Anthoussae/lapin_lazuli)

Subject branch: [`main`](https://github.com/Anthoussae/lapin_lazuli/tree/main)

Subject commit: [`5370196`](https://github.com/Anthoussae/lapin_lazuli/commit/5370196) - `reduced png size by aprox 75%`

Commit date: `2026-06-06 12:12:27 +0800`

Review date: `2026-06-09`

## General Take

James, Lapin Lazuli has much better bones than an early agent-assisted game usually has. The project already has a recognizable architecture:

- `src/data/` holds catalogs for cards, relics, enemies, paths, gems, enchantments, and events.
- `src/systems/` holds much of the game-rule logic.
- `src/reducers/` owns the action pipeline, phase gating, event resolution, and animation ticking.
- `src/core/` holds shared state, ids, rng, and store primitives.
- `src/render/` owns React screens, primitives, visual effects, layout helpers, and asset mapping.
- `src/runtime/` owns browser/runtime concerns such as asset preloading and game logs.

That separation gives you a real foundation to build on. The most useful next step is not "rewrite it"; it is "make the existing ideas more explicit so you and your agents can extend the game with confidence."

## Highest Leverage Improvements

1. Add a test harness around pure game logic.
   The reducer and systems code is deterministic enough to be very testable. Start with Vitest tests for `rng`, card effects, combat turns, relic triggers, rewards, and phase gating.

2. Add Storybook or an equivalent UI workshop.
   The game has many rich components and visual states. Storybook would let you inspect cards, relics, HUD states, screens, and animation start/end states without playing through a whole run.

3. Document the domain model.
   The project has strong domain concepts, but many contracts live in file names and TypeScript unions rather than in a short guide. A `docs/architecture/domain-model.md` would help agents know what a card, card instance, effect, relic trigger, phase, event, animation, and render primitive mean.

4. Split a few high-gravity files by responsibility.
   `src/reducers/steps/applyAction.ts`, `src/render/game.css`, and some data catalogs are carrying a lot of surface area. They do not need a dramatic refactor, but they would benefit from gradual extraction along existing domain boundaries.

5. Add lightweight design docs and ADRs.
   This game has real architectural decisions: custom reducer store, deterministic RNG, data-driven effects, event-derived animations, render-local animation providers, and Vite/Workers deployment. A small ADR trail would make these decisions legible.

6. Professionalize tooling without turning the repo into a bureaucracy.
   Add Prettier, test scripts, CI, branch validation, and a short contributing/development guide. This is a good way to practice professional engineering habits while keeping the project fun.

## Suggested Reading Order

- [Testing Strategy](./01-testing.md)
- [Domain Modeling](./02-domain-modeling.md)
- [Structure And Modularity](./03-structure-and-modularity.md)
- [Rendering And UI Architecture](./04-rendering-and-ui.md)
- [Tooling, CI, And Code Style](./05-tooling-ci-code-style.md)
- [Agent Workflow And Design Docs](./06-agent-workflow-and-design-docs.md)

## Note For James

You have built enough that the next level is not "write more code faster." The next level is making the project easier to reason about.

That means tests for the game rules, docs for the game concepts, examples for the UI states, and small workflow habits that let an agent make changes without guessing. None of that needs to be heavy. A few well-named files and a few focused tests will go a long way.

## Good First Projects To Ask An Agent To Do

- "Add Vitest and write tests for the deterministic RNG and dice helpers."
- "Write tests for card upgrade and foil effect scaling."
- "Create Storybook stories for `Card`, `GameCardView`, `RelicIcon`, and the HUD components."
- "Write `docs/architecture/domain-model.md` explaining phases, actions, events, effects, cards, relics, and render effects."
- "Split `applyAction.ts` into feature-specific action handlers while preserving behavior."
- "Add Prettier, CI, and a `check` script that runs typecheck, lint, tests, and build."
- "Create ADR 0001 for the reducer/event architecture."

## Tone

The right framing is: "This is a real project now. Real projects need a map."

This review is meant to encourage you to keep building. The point is not that you should have anticipated all of this on day one. The point is that the game has become substantial enough that a little structure will make it more fun, not less.
