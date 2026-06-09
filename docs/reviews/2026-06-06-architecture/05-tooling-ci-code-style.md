# Tooling, CI, And Code Style

## Current State

The repo is a Vite + React + TypeScript app with ESLint configured through `.eslintrc.cjs`.

Current scripts:

```json
{
  "dev": "vite",
  "build": "tsc && vite build",
  "lint": "eslint src --ext ts,tsx --report-unused-disable-directives --max-warnings 0",
  "preview": "vite preview"
}
```

Missing or not visible:

- test script
- typecheck-only script
- formatter
- format check
- CI workflow
- Storybook
- Playwright
- dependency update policy
- contributor/development guide

## Recommended Tooling Baseline

Keep this lightweight. The goal is professional habits, not ceremony.

### Scripts

Suggested scripts:

```json
{
  "dev": "vite",
  "typecheck": "tsc --noEmit",
  "lint": "eslint src --ext ts,tsx --report-unused-disable-directives --max-warnings 0",
  "format": "prettier --write .",
  "format:check": "prettier --check .",
  "test": "vitest run",
  "test:watch": "vitest",
  "build": "npm run typecheck && vite build",
  "check": "npm run format:check && npm run lint && npm run test && npm run build",
  "preview": "vite preview"
}
```

If Storybook is added:

```json
{
  "storybook": "storybook dev -p 6006",
  "build:storybook": "storybook build"
}
```

### Prettier

Add Prettier early. It removes style debates and keeps agent diffs cleaner.

Suggested files:

```text
.prettierrc
.prettierignore
```

Keep config boring. For a solo/learning game project, the important thing is consistency.

### ESLint

The repo already has ESLint. Good next steps:

- Consider migrating to flat config only when convenient, not as a first priority.
- Add test file overrides once Vitest exists.
- Add React accessibility linting when UI work stabilizes.
- Keep `--max-warnings 0` if you like strict feedback.

### CI

Add one GitHub Actions workflow:

```text
.github/workflows/check.yml
```

Run on pull requests and pushes to main:

- install dependencies with `npm ci`
- run `npm run check`
- optionally upload build artifacts later

This gives you the professional loop:

```text
branch -> commit -> push -> CI says whether the repo is healthy
```

### Deployment

The deployed app appears to be on Cloudflare Workers/Pages. The public repo is [Anthoussae/lapin_lazuli](https://github.com/Anthoussae/lapin_lazuli), with review context taken from [`main`](https://github.com/Anthoussae/lapin_lazuli/tree/main) at commit [`5370196`](https://github.com/Anthoussae/lapin_lazuli/commit/5370196). The repo also has a remote branch named [`cloudflare/workers-autoconfig`](https://github.com/Anthoussae/lapin_lazuli/tree/cloudflare/workers-autoconfig), so deployment work may already be partially configured outside `main`.

Recommended docs:

```text
docs/development/deployment.md
```

Cover:

- where the app deploys
- what command builds it
- which branch/environment deploys
- how to roll back
- how to preview locally

## Code Style Guidance

### Keep The Domain Language Consistent

Prefer terms already in the game:

- card template
- card instance
- deck
- draw pile
- hand
- discard pile
- effect
- relic trigger
- path
- room
- phase
- event
- render effect

Avoid synonyms unless a new concept is truly being introduced.

### Prefer Small Rule Functions

New gameplay rules should usually be small functions in `src/systems/<domain>/`.

Good:

```text
systems/cards/inkCost.ts
systems/cards/upgrades.ts
systems/combat/damageEnemy.ts
systems/rewards/rewardLoot.ts
```

Less good:

```text
one more branch inside a 900-line action handler
one more rule hidden in a React component
one more hard-coded behavior in a CSS or layout helper
```

### Make Temporary Decisions Searchable

The repo has comments like `MVP`, `Bugtesting`, `legacy`, and `not yet implemented`. Those are useful while moving fast, but they should become either:

- tracked issues
- ADR notes
- TODOs with owner/context
- removed if obsolete

Agents can clean up comments safely only when the intent is explicit.

### Avoid Over-Abstracting Too Early

This game is still growing. Do not extract a general engine just because the code has two examples of something.

Good extraction signals:

- three or more similar features
- tests are duplicated
- a file is hard to navigate
- an agent repeatedly edits the wrong area
- a concept needs a name in docs

## Professional Process, Light Version

Recommended daily loop:

1. Create a branch.
2. Write a short plan or issue note for non-trivial work.
3. Implement in small commits.
4. Run `npm run check`.
5. Ask an agent for a review.
6. Push and let CI verify.
7. Keep a short changelog or release note when deploying.

This is enough structure to teach professional habits without slowing down creativity.

## Agent Prompt

```text
Add a lightweight professional tooling baseline: Prettier, Vitest, a check script, and a GitHub Actions workflow that runs format check, lint, tests, and build. Keep config minimal and document the commands in README.md.
```
