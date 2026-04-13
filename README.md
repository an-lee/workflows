# workflows

Centralized reusable GitHub Actions workflows (YAML) and agentic workflows (gh-aw Markdown).

See [SPEC.md](SPEC.md) for full conventions and design decisions.

---

## Consuming YAML Workflows

Add a job that `uses` a reusable workflow:

```yaml
jobs:
  test:
    uses: an-lee/workflows/.github/workflows/ci/node.yml@v1
    with:
      node-version: '20'
    secrets:
      npm-token: ${{ secrets.NPM_TOKEN }}
```

### Available YAML Workflows

| Path | Description |
|---|---|
| `.github/workflows/ci/node.yml` | Node.js CI — install, cache, test |
| `.github/workflows/ci/bun.yml` | Bun CI — mise setup, install, test |
| `.github/workflows/ci/python.yml` | Python CI — install, cache, test |
| `.github/workflows/ci/rubyonrails.yml` | Ruby on Rails CI — install, lint, audit, test |
| `.github/workflows/cd/docker-build-push.yml` | Build and push Docker image |
| `.github/workflows/cd/release.yml` | GitHub Releases with changelog |
| `.github/workflows/security/codeql.yml` | CodeQL security analysis |
| `.github/workflows/security/dependency-review.yml` | Dependency review |

#### Bun CI — example usage

Minimal (all defaults — self-hosted runner, `bun test`):

```yaml
# .github/workflows/ci.yml in your repo
name: CI
on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  test:
    uses: an-lee/workflows/.github/workflows/ci/bun.yml@v1
```

With custom options:

```yaml
jobs:
  test:
    uses: an-lee/workflows/.github/workflows/ci/bun.yml@v1
    with:
      runs-on: '["ubuntu-latest"]'        # switch to GitHub-hosted runner
      compile: 'bun run build'            # default is 'bun run compile'
      run-tests: 'bun run test:ci'        # default is 'bun test'
```

#### Ruby on Rails CI — example usage

Minimal (all defaults — self-hosted runner, postgres DB named after the repo):

```yaml
# .github/workflows/ci.yml in your Rails repo
name: CI
on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  rails:
    uses: an-lee/workflows/.github/workflows/ci/rubyonrails.yml@v1
```

With custom options:

```yaml
jobs:
  rails:
    uses: an-lee/workflows/.github/workflows/ci/rubyonrails.yml@v1
    with:
      runs-on: '["self-hosted", "Linux", "ci"]' # default; pass '["ubuntu-latest"]' for GitHub-hosted
      database: postgres          # or 'none' to skip all DB steps
      database-name: myapp        # DATABASE_URL → .../myapp_test (defaults to repo name)
      rails-env: test             # default
      run-tests: bin/rspec        # default is 'bin/rails test'
      enable-linting: true        # set false if rubocop runs in a separate job
      enable-security-audit: false
```

---

## Consuming Agentic Workflows

Agentic workflows use [gh-aw](https://github.github.io/gh-aw/guides/packaging-imports/) and are Markdown files with frontmatter. Import them into your repo:

```bash
gh aw import an-lee/workflows/agentic/ci/code-review.md@v1
```

This caches the workflow in `.github/aw/imports/` in your repo. Run it with:

```bash
gh aw run agentic/ci/code-review.md
```

### Available Agentic Workflows

| Path | Description |
|---|---|
| `agentic/ci/code-review.md` | AI-powered code review on PRs |
| `agentic/cd/deploy.md` | Deployment agent |
| `agentic/security/audit.md` | Security audit agent |
| `agentic/shared/tools/common-tools.md` | Shared tooling for agents |
| `agentic/shared/mcp/tavily.md` | Tavily search MCP config |

---

## Versioning

| Ref | Stability | Use Case |
|---|---|---|
| `@v1.2.3` | Exact release | Production |
| `@v1` | Major (floating) | Auto-fixes within major |
| `@main` | Bleeding edge | Active development |

To release a new version:

```bash
git tag v1.2.3 && git push origin v1.2.3
git tag -f v1 && git push -f origin v1  # update major alias
```
