---
# Shared tooling for agentic workflows.
# This file has no `on:` trigger — it is imported by other agentic workflows.
# Consumer example (in another repo):
#   imports:
#     - an-lee/workflows/agentic/shared/tools/common-tools.md@v1

import-schema:
  max-comment-length:
    type: integer
    required: false
    default: 2000
    description: Maximum characters per review comment

  include-line-numbers:
    type: boolean
    required: false
    default: true
    description: Include line numbers in code references
---

## Comment Tool

Use the `comment` tool to add inline comments to PRs and issues.

## Lint Patterns

| Pattern | Severity | Description |
|---|---|---|
| `console.log(` | 🟡 Warning | Debug code left in |
| `TODO(` | 💡 Suggestion | Incomplete work marker |
| `password\s*=` | 🔴 Critical | Hardcoded secret |
| `.innerHTML` | 🔴 Critical | XSS risk |
| `eval(` | 🔴 Critical | Code injection risk |
| `catch (e) {}` | 🟡 Warning | Swallowed exception |
| `// eslint-disable` | 💡 Suggestion | Linting suppression |

## Formatting Standards

- Use backticks for code: `variableName`, `functionCall()`
- Use underscores for file names: `src/utils/helper.ts`
- Keep comments under the max-comment-length threshold
