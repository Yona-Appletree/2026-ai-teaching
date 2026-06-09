# Structure And Modularity

## Current Shape

The top-level `src` layout is already legible:

```text
src/
  animation/
  assets/
  core/
  data/
  input/
  reducers/
  render/
  runtime/
  styles/
  systems/
  ui/
```

That is a good start. It communicates a useful separation between data, rules, state transition, rendering, and runtime concerns.

## Where The Structure Is Working

### `data` Is A Useful Content Layer

The data catalogs make it natural to add content without touching every screen:

- cards
- relics
- enemies
- enemy intents
- paths
- gems
- enchantments
- mystery rooms

This is an excellent teaching point. Data-driven content is one of the best ways to make agents productive.

### `systems` Contains Game Rules

The `systems` directory is organized around gameplay domains:

- `cards`
- `combat`
- `enchantments`
- `events`
- `gems`
- `paths`
- `relics`
- `rest`
- `rewards`
- `shop`

This is the right instinct. Most new rule logic should probably start here rather than inside React components.

### `reducers` Gives The Game A Central Pipeline

The reducer pipeline in `src/reducers/reduceGame.ts` is a strong architectural anchor:

```text
apply action
resolve event queue
derive animations
stamp debug events
enter blocking animation phase if needed
log the action/event batch
prune stale combat intents
```

That pipeline is worth documenting and preserving.

## Pressure Points

### `applyAction.ts` Is A Gravity Well

`src/reducers/steps/applyAction.ts` is doing a lot:

- boot actions
- player action dispatch
- title/new game setup
- reward selection
- shop buying
- relic selection
- path rolling
- rest
- treasure room
- gemstone cavern
- mystery rooms
- level advancement

Large dispatcher files are common in reducer architectures, but they become hard for agents because many unrelated changes touch the same file.

Suggested direction:

```text
src/reducers/steps/applyAction.ts
src/reducers/handlers/title.ts
src/reducers/handlers/path.ts
src/reducers/handlers/reward.ts
src/reducers/handlers/shop.ts
src/reducers/handlers/rest.ts
src/reducers/handlers/rooms.ts
src/reducers/handlers/boot.ts
```

Do this gradually. Start by moving one low-risk section, such as rest or shop, with tests.

### Phase Gating Duplicates Domain Knowledge

`src/reducers/steps/phaseGating.ts` is valuable, but it mirrors a lot of action handling knowledge. That is acceptable, but it should be treated as a public contract:

- Every player action should be intentionally legal in one or more phases.
- Every new phase should document legal actions.
- Tests should assert important illegal actions are ignored.

If it grows much larger, consider a table-driven phase/action map plus per-phase predicates.

### CSS Is Centralized

`src/render/game.css` is very large, and `src/styles/tokens.css` is also large. The token file has useful organization comments, but the styling system is still difficult to navigate.

Suggested direction:

```text
src/render/styles/
  hud.css
  cards.css
  combat.css
  path.css
  shop.css
  rewards.css
  rooms.css
  effects.css
```

Keep tokens centralized if that is working, but split component/screen CSS by domain. Avoid doing this as a giant mechanical rewrite unless you have tests or screenshots to catch regressions.

### Render Providers Are Deeply Nested

`src/render/GameView.tsx` has a long stack of providers for visual effects. That is not inherently wrong, but it is hard to scan.

Suggested direction:

- Extract a `GameFxProviders` component.
- Group providers by purpose:
  - travel effects
  - combat hit/effect overlays
  - card effects
  - global transitions
- Add a short comment or doc explaining provider ordering if order matters.

### Some Types Are Centralized In One Large State File

`src/core/types/state.ts` is a useful single source of truth, but it will continue to grow. Consider splitting by domain when it becomes painful:

```text
src/core/types/phases.ts
src/core/types/cards.ts
src/core/types/combat.ts
src/core/types/rewards.ts
src/core/types/rooms.ts
src/core/types/player.ts
```

This is not urgent. The current file is still readable because it has comments and TypeScript unions.

## Professional Habit To Teach

When a file becomes hard to navigate, do not immediately invent a framework. Instead:

1. Name the responsibilities.
2. Add tests around the behavior.
3. Extract one responsibility into a nearby module.
4. Keep imports and naming boring.
5. Repeat only when the next extraction is obvious.

That is a very good habit for working with agents.

## Agent Prompt

```text
Refactor one section of src/reducers/steps/applyAction.ts into a focused handler module. Start with rest or shop behavior. Add or update Vitest reducer tests first, preserve all behavior, and keep the public GameAction and GameState types unchanged.
```
