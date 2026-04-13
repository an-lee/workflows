# Workflows Repo — Design Specification

## Overview

A centralized repository of reusable GitHub Actions workflows (YAML) and agentic workflows (gh-aw Markdown), consumable by all other repos.

## Repo Structure

```
workflows/
├── .github/
│   └── workflows/
│       ├── ci/
│       │   ├── node.yml
│       │   ├── python.yml
│       │   └── ...
│       ├── cd/
│       │   ├── docker-build-push.yml
│       │   ├── release.yml
│       │   └── ...
│       └── security/
│           ├── codeql.yml
│           ├── dependency-review.yml
│           └── ...
├── agentic/
│   ├── shared/
│   │   ├── tools/
│   │   │   └── common-tools.md
│   │   └── mcp/
│   │       └── tavily.md
│   ├── ci/
│   │   └── code-review.md
│   ├── cd/
│   │   └── deploy.md
│   └── security/
│       └── audit.md
└── README.md
```

Consumers reference workflows like:
- YAML: `an-lee/workflows/.github/workflows/ci/node.yml@v1`
- Agentic: `an-lee/workflows/agentic/shared/tools/common-tools.md@main`

---

## Section 1 — YAML Reusable Workflow Conventions

Each YAML file under `.github/workflows/{category}/` uses `workflow_call` as its trigger.

### Conventions

- Inputs declared with `type`, `description`, `required`, and `default` where sensible
- Secrets declared explicitly (no blanket `secrets: inherit`) so callers know exactly what's needed
- Outputs declared at workflow level so callers can chain jobs with `needs`
- Pinned action versions in the workflow body (SHA or tag) for supply-chain safety
- Inline comments explaining non-obvious inputs/outputs and any constraints

### Example Call

```yaml
jobs:
  test:
    uses: an-lee/workflows/.github/workflows/ci/node.yml@v1
    with:
      node-version: '20'
    secrets:
      npm-token: ${{ secrets.NPM_TOKEN }}
```

---

## Section 2 — Agentic Workflow Conventions

Each Markdown file under `agentic/{category}/` uses gh-aw frontmatter. Shared components live in `agentic/shared/` and are imported by other agentic workflows.

### File Anatomy

```markdown
---
# agentic/ci/code-review.md
on:
  pull_request:

imports:
  - ../shared/tools/common-tools.md
  - ../shared/mcp/tavily.md

import-schema:       # only for shared/ files that accept parameters
  languages:
    type: array
    required: false
---

Natural language instructions for the agent...
```

### Conventions

- Shared files (no `on:` field) declare `import-schema` for typed parameters
- Cross-repo import path: `an-lee/workflows/agentic/shared/tools/common-tools.md@v1`
- Cached imports land in `.github/aw/imports/` in the consumer repo (gitignored or committed, consumer's choice)
- Use `inlined-imports: true` for cross-org `workflow_call` scenarios requiring self-contained lock files

---

## Section 3 — Versioning & Release Strategy

Tags follow semver with major aliases: `v1.2.3` plus a floating `v1` tag that always points to the latest `v1.x.x`.

| Reference | Stability | Use Case |
|---|---|---|
| `@v1.2.3` | Exact release | Production, maximum stability |
| `@v1` | Major floating | Projects that want fixes automatically |
| `@main` | Bleeding edge | Active development |

### Release Process

1. Commit changes to `main`
2. Tag: `git tag v1.2.3 && git push origin v1.2.3`
3. Force-update floating major tag: `git tag -f v1 && git push -f origin v1`

No changelog file — commit messages serve as history.

---

## Section 4 — Categories

| Category | YAML Examples | Agentic Examples |
|---|---|---|
| **CI** | `node.yml`, `python.yml` | `code-review.md` |
| **CD** | `docker-build-push.yml`, `release.yml` | `deploy.md` |
| **Security** | `codeql.yml`, `dependency-review.yml` | `audit.md` |
