# Rendering And UI Architecture

## Current Shape

The render layer is rich and already has useful internal categories:

```text
src/render/
  assets/
  hooks/
  primitives/
  screens/
  *Context.tsx
  *Config.ts
  layout helpers
  CSS
```

The screens and primitives split is healthy:

- Screens represent game phases and rooms.
- Primitives represent reusable visual building blocks like cards, relics, HUD bars, offers, icons, and overlays.
- Context providers orchestrate visual effects such as card travel, gold travel, socket flips, hit effects, poison/fire tints, cast bursts, and transitions.

## Main Opportunity

The UI is sophisticated, but it is hard to inspect without playing through the game. This is where Storybook would be especially useful.

Agents are much better at UI work when they can:

- render a component in isolation
- see multiple states side by side
- run screenshots
- compare before/after visual states
- avoid inventing state objects from scratch

## Storybook Targets

### Card And Card-Like Components

Start with:

- `Card`
- `GameCardView`
- `CardDesc`
- `KeywordLine`
- `KeywordTooltips`

Stories should cover:

- normal card
- upgraded card
- foil card
- socketed card
- potion card
- exhausted card
- disabled card
- selected card
- long card name
- long description
- keyword descriptions
- missing art fallback

Cards are central to the game, so this is the best first Storybook slice.

### HUD And Resource Display

Stories:

- normal HP/ink/gold/key state
- low HP
- high shield and locked shield
- no relics
- many relics
- in-combat versus out-of-combat HUD

### Screens

Use fixture states for:

- Title
- Starter relic selection
- Path selection with locked/unlocked paths
- Combat player ready
- Combat resolving
- Reward screen with uncollected loot
- Reward screen after loot collection
- Shop
- Rest
- Gemstone Cavern
- Font of Lethe
- Printer
- Collector
- Victory and defeat

The important idea is not pixel-perfect visual testing on day one. The important idea is a fast workshop for exploring state.

## Fixture Strategy

Storybook will be much easier if the project has reusable fixture builders:

```text
src/test/fixtures/state.ts
src/test/fixtures/cards.ts
src/test/fixtures/combat.ts
src/test/fixtures/render.ts
```

Even if these start under `src/stories/fixtures`, the pattern matters:

- Build a valid baseline state.
- Apply small overrides.
- Prefer domain helpers over giant object literals.
- Reuse the same fixtures for tests and stories when practical.

## Animation Architecture

The project has two animation paths:

1. Reducer-owned animation jobs in `state.ui.anim.jobs`.
2. Render-local effect providers in React contexts.

That split can work, but it needs a written rule of thumb:

- Use reducer events/jobs when an animation blocks game progression or represents a domain consequence.
- Use render-local providers for visual flourish that should not decide game rules.
- If a visual effect completion dispatches a system action, document why that completion matters to gameplay flow.

This would prevent future agents from hiding game rules in React effects or overloading reducer events with purely visual concerns.

## Accessibility And Interaction

The project already includes some keyboard and ARIA handling, such as clickable card roles and keyboard handling. That is worth encouraging.

Recommended next checks:

- All button-like controls should be real buttons when possible.
- Non-button clickable elements should have keyboard handlers and `aria-disabled` where appropriate.
- Dialogs should have labels and escape behavior.
- Hover-only tooltip information should be available to keyboard users eventually.
- Text should be stress-tested with long card names and descriptions.

Storybook can help here too because interaction and accessibility addons can catch issues early.

## Visual Regression Later

After Storybook exists, consider screenshot testing for a small number of stable states:

- Card variants.
- Combat ready screen.
- Reward screen.
- Shop.
- Path selection.

Do this after the design is somewhat stable. Early visual regression tests can be noisy if art and layout are changing every day.

## Agent Prompt

```text
Add Storybook for the render layer. Start with stories for Card, GameCardView, RelicIcon, BarHud, CounterHud, and one RewardScreen fixture. Create small fixture builders so stories do not contain huge GameState literals. Do not change gameplay behavior.
```
