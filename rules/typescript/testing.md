---
paths:
  - "**/*.ts"
  - "**/*.tsx"
  - "**/*.js"
  - "**/*.jsx"
---
# TypeScript/JavaScript Testing

> This file extends [common/testing.md](../common/testing.md) with TypeScript/JavaScript specific content.

## E2E Testing

**Prefer agent-browser** for E2E testing (semantic selectors, AI-optimized, auto-waiting).
**Fallback to Playwright** when agent-browser is unavailable.

## Agent Support

- **e2e-runner** - E2E testing specialist (agent-browser preferred, Playwright fallback)
