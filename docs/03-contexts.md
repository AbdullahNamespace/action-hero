🌐 **English** · [العربية](03-contexts.ar.md)

# 03 — Contexts & Expressions

> Workflow: [`03-contexts.yml`](../.github/workflows/03-contexts.yml)

**Goal:** read information about the run with contexts, and use expressions safely.

## Syntax

`${{ <expression> }}` is evaluated and **substituted as text before** the step runs.

## Contexts

| Context   | Holds                                        | Example                    |
| --------- | -------------------------------------------- | -------------------------- |
| `github`  | run, repo, event, actor                      | `github.ref_name`          |
| `runner`  | the machine                                  | `runner.os`, `runner.temp` |
| `env`     | variables set with `env:`                    | `env.GREETING`             |
| `vars`    | repository/environment variables             | `vars.FIREBASE_PROJECT_ID` |
| `secrets` | secrets                                      | `secrets.DEMO_SECRET`      |
| `inputs`  | `workflow_dispatch` / `workflow_call` inputs | `inputs.name`              |
| `matrix`  | current matrix combination                   | `matrix.node`              |
| `steps`   | earlier steps' outputs/outcomes              | `steps.ver.outputs.v`      |
| `needs`   | outputs/results of required jobs             | `needs.build.result`       |
| `job`     | current job status                           | `job.status`               |

Common `github.*` fields: `event_name`, `ref`, `ref_name`, `sha`, `actor`, `repository`, `run_id`, `event.pull_request.number`.

## Operators & functions

- Operators: `==` `!=` `&&` `||` `!` `<` `>` (string comparison is case-insensitive). Strings use **single quotes**: `'main'`.
- Default-value idiom: `matrix.experimental || false`.

| Function                    | Purpose                      | Example                                        |
| --------------------------- | ---------------------------- | ---------------------------------------------- |
| `contains(a, b)`            | substring / array membership | `contains(github.ref, 'main')`                 |
| `startsWith` / `endsWith`   | prefix / suffix test         | `startsWith(github.ref, 'refs/tags/')`         |
| `format(fmt, …)`            | build a string               | `format('{0}-{1}', github.job, github.run_id)` |
| `join(array, sep)`          | array → string               | `join(matrix.os, ',')`                         |
| `toJSON(x)` / `fromJSON(s)` | serialize / parse            | `toJSON(github)`                               |
| `hashFiles(glob)`           | hash of files (cache keys)   | `hashFiles('pnpm-lock.yaml')`                  |

Status functions (`success()`, `failure()`, `always()`) → [05](05-conditionals-outputs.md).

## Debug a context

```yaml
env:
  GITHUB_CTX: ${{ toJSON(github) }}
run: |
  echo "::group::github context"
  echo "$GITHUB_CTX"
  echo "::endgroup::"
```

The token appears as `***`.

## ⚠️ Script injection

Values controlled by others (PR title, branch name, issue body) must **never** go inline in `run:`. Pass them through `env:` ([06](06-secrets-vars-permissions.md)):

```yaml
env:
  TITLE: ${{ github.event.pull_request.title }}
run: echo "$TITLE"
```

## Pitfalls

- Contexts are not available everywhere: `secrets` can't be used in a job-level `if:` — copy it to `env` first.
- `${{ }}` is substituted _before_ the shell, so quoting rules are the shell's, not YAML's.
- Using double quotes inside an expression (`"main"`) is a syntax error.

## Exercise

Open a PR that touches the workflow, run it, and find your PR title printed safely from `$TITLE`.

⬅️ [02 — Triggers](02-triggers.md) · ➡️ [04 — CI · Matrix · Cache](04-ci-matrix-cache.md)
