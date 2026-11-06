🌐 **English** · [العربية](01-anatomy.ar.md)

# 01 — Anatomy of a Workflow

> Workflow: [`01-hello.yml`](../.github/workflows/01-hello.yml)

**Goal:** understand the four parts of every workflow.

## Structure

```yaml
name: 01 · Hello # 1) label in the Actions tab

on: # 2) when it runs
  push:
    branches: [main]
  workflow_dispatch:

jobs: # 3) what it runs
  hello:
    runs-on: ubuntu-latest
    steps: # 4) ordered steps
      - uses: actions/checkout@v7
      - run: echo "Hello"
```

## Concepts

- **`on`** — events that trigger the run → [02](02-triggers.md).
- **`jobs`** — each job gets its own clean runner. Jobs run in parallel unless linked with `needs`. The key (`hello`) is what `needs` refers to; `name:` is display only.
- **`steps`** — each step is one of:

| Type   | Purpose                  | Example                     |
| ------ | ------------------------ | --------------------------- |
| `run`  | run a shell command      | `run: ls -la`               |
| `uses` | call a ready-made action | `uses: actions/checkout@v7` |

Multi-line commands use `|`.

- **Contexts** — `${{ ... }}` expressions (details in [03](03-contexts.md)):

| Expression          | Value             |
| ------------------- | ----------------- |
| `github.repository` | repository name   |
| `github.ref_name`   | branch / tag name |
| `github.sha`        | commit SHA        |
| `github.actor`      | who triggered it  |
| `github.event_name` | event type        |

## Pitfalls

- Forgetting `checkout` → the runner starts empty.
- Indent with 2 spaces, never Tab.
- Values containing `:` or `#` need quotes.

➡️ Next: [02 — Triggers](02-triggers.md)
