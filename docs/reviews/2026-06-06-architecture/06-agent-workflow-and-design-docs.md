# Agent Workflow And Design Docs

## Why This Matters

James, because you are building with agents, the repo should teach agents how to help.

Agents work best when the repo contains:

- a short architecture map
- clear development commands
- examples of good tests
- domain vocabulary
- docs for major decisions
- a repeatable planning/review process

The goal is not heavyweight process. The goal is fewer guesses.

## Recommended Docs Structure

```text
docs/
  architecture/
    00-overview.md
    domain-model.md
    game-loop.md
    content-catalogs.md
    rendering-and-effects.md
    deployment.md
  adr/
    0001-reducer-and-event-pipeline.md
    0002-data-driven-card-and-relic-catalogs.md
  plans/
  reviews/
  archive/
    plans/
    reviews/
  skills/
    plan.md
    implement.md
    review.md
```

You do not need all of this immediately. Start with:

1. `docs/architecture/00-overview.md`
2. `docs/architecture/domain-model.md`
3. `docs/adr/0001-reducer-and-event-pipeline.md`
4. `docs/skills/review.md`

## ADR Candidates

### ADR 0001: Reducer And Event Pipeline

Decision to document:

- Use a custom reducer/store architecture rather than Redux/Zustand/XState.
- Process game actions through a central reducer pipeline.
- Use events as domain consequences for follow-up resolution, animation derivation, debug state, and logging.

Why it matters:

- It tells future agents where game rules should go.
- It preserves the deterministic testable core.
- It distinguishes domain events from render-only effects.

### ADR 0002: Data-Driven Game Content

Decision to document:

- Cards, relics, enemies, paths, enchantments, and rooms are catalog-driven.
- Systems interpret catalog data.
- Adding content should usually mean adding data plus tests, not adding one-off UI logic.

Why it matters:

- It makes the project easier to extend.
- It helps agents add content consistently.
- It keeps the game from becoming a pile of special cases.

### ADR 0003: UI Animation Ownership

Decision to document:

- Some animations are reducer/state driven and can block gameplay.
- Many visual effects are render-local React providers.
- Completion callbacks should dispatch system actions only when gameplay is truly waiting on them.

Why it matters:

- It prevents hidden game rules in visual components.
- It prevents every animation from becoming reducer state.

## Skills For This Repo

You could copy the teaching repo's idea and add lightweight skills:

```text
docs/skills/plan.md
docs/skills/implement.md
docs/skills/review.md
```

Keep them short and project-specific.

### `plan.md`

Should tell an agent:

- read `docs/architecture/00-overview.md` first
- identify domain modules touched
- write a small plan under `docs/plans/<date>-<topic>/`
- list tests to add or update
- identify whether an ADR is needed

### `implement.md`

Should tell an agent:

- follow an existing plan
- preserve reducer/system/render boundaries
- add tests before or with behavior changes
- run `npm run check`
- write a completion note

### `review.md`

Should tell an agent:

- review behavior, tests, architecture boundaries, accessibility, and docs
- write findings under `docs/reviews/<date>-<topic>/`
- include file references and validation commands

## Good Agent Guardrails

Add a repo-local agent instruction file if your tool supports it, such as:

```text
AGENTS.md
```

Suggested content:

- Use `rg` to search.
- Run `npm run check` before finalizing.
- Put game rules in `src/systems` unless the reducer must orchestrate phase flow.
- Put catalog entries in `src/data`.
- Put visual components in `src/render`.
- Do not hide gameplay mutations in React effects.
- Add tests for rule changes.
- Update architecture docs when introducing a new domain concept.

## Mentoring Frame

A useful frame:

> You are not adding process because the project is broken. You are adding process because the project is becoming real enough to deserve it.

Professional structure should feel like better roads, not a gate. The docs and skills should make it easier to keep making the game.

## Agent Prompt

```text
Create lightweight repo-local architecture docs and agent workflow docs for this game. Start with docs/architecture/00-overview.md, docs/architecture/domain-model.md, docs/adr/0001-reducer-and-event-pipeline.md, and docs/skills/review.md. Keep them concise and specific to the existing codebase.
```
