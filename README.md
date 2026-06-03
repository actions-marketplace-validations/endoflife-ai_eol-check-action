# EOL Runtime Check — GitHub Action

**Catch end-of-life runtimes in CI before they become a security incident.**

Powered by [endoflife.ai](https://endoflife.ai) — free EOL intelligence for Node.js, Python, PHP, Ruby, Go, and 450+ more products.

---

## What it does

Scans your repository for version pins and checks each one against the [endoflife.ai API](https://endoflife.ai/api). If any runtime is EOL — or approaching EOL — the action fails your build, posts annotations, and writes a detailed summary to the job.

**Detects versions from:**
- `.nvmrc` / `.node-version` — Node.js
- `.python-version` — Python
- `.ruby-version` / `Gemfile` — Ruby
- `.tool-versions` (asdf) — Node.js, Python, Ruby, Go
- `package.json` `engines` field — Node.js
- `go.mod` — Go
- `Dockerfile` `FROM` statements — Node.js, Python, PHP, Ruby
- `composer.json` — PHP
- `.terraform-version` — Terraform

---

## Quick start

```yaml
- name: Check runtimes against endoflife.ai
  uses: endoflife-ai/eol-check-action@v1
```

That's it. The action auto-detects your version files and fails the build if anything is EOL.

---

## Full example

```yaml
name: EOL Runtime Check

on:
  push:
    branches: [main]
  pull_request:
  schedule:
    - cron: '0 9 * * 1'  # Weekly Monday morning check

jobs:
  eol-check:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - uses: endoflife-ai/eol-check-action@v1
        with:
          fail-on-eol: true
          fail-on-warning: false
          warning-days: 90
          # api-key: ${{ secrets.EOL_API_KEY }}
```

---

## Inputs

| Input | Description | Default |
|---|---|---|
| `fail-on-eol` | Fail the build if any runtime is EOL | `true` |
| `fail-on-warning` | Fail the build if any runtime is approaching EOL | `false` |
| `warning-days` | Days before EOL to start warning | `90` |
| `api-key` | endoflife.ai Pro API key (higher rate limits) | — |
| `paths` | Comma-separated paths to scan | `.` |
| `products` | Additional products to check: `slug:version,slug:version` | — |

---

## Outputs

| Output | Description |
|---|---|
| `eol-count` | Number of EOL runtimes found |
| `warning-count` | Number of runtimes approaching EOL |
| `results-json` | Full results as JSON |

---

## Examples

**Check specific versions manually:**
```yaml
- uses: endoflife-ai/eol-check-action@v1
  with:
    products: 'nodejs:18,python:3.11,php:8.1'
```

**Warn but don't fail (informational mode):**
```yaml
- uses: endoflife-ai/eol-check-action@v1
  with:
    fail-on-eol: false
    fail-on-warning: false
```

**Fail 6 months before EOL:**
```yaml
- uses: endoflife-ai/eol-check-action@v1
  with:
    fail-on-warning: true
    warning-days: 180
```

**Use Pro API key for higher rate limits:**
```yaml
- uses: endoflife-ai/eol-check-action@v1
  with:
    api-key: ${{ secrets.EOL_API_KEY }}
```

---

## Job summary

Every run writes a full summary table to the GitHub Actions job summary, including status, EOL dates, and links to the endoflife.ai score card for each runtime.

---

## API & rate limits

The free tier allows 100 API requests per day per IP. For most repos this is sufficient. For large monorepos or high-frequency CI, add an endoflife.ai Pro API key via repository secret.

[Get a Pro API key →](https://endoflife.ai/api)

---

## Data

EOL dates and risk scores are sourced from [endoflife.ai](https://endoflife.ai), which aggregates data from endoflife.date, NVD, CISA KEV, and official vendor lifecycle pages. Data is refreshed daily.

---

## License

MIT — endoflife.ai
