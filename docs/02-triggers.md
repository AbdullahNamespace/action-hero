🌐 **English** · [العربية](02-triggers.ar.md)

# 02 — Triggers

> Workflow: [`02-triggers.yml`](../.github/workflows/02-triggers.yml)

**Goal:** control precisely _when_ a workflow runs.

## Events used

| Event               | Runs when           | Notes                                |
| ------------------- | ------------------- | ------------------------------------ |
| `push`              | commits are pushed  | supports `branches`, `tags`, `paths` |
| `pull_request`      | PR opened / updated | runs on the temporary merge ref      |
| `schedule`          | cron time reached   | **always UTC**, default branch only  |
| `workflow_dispatch` | manual (UI or API)  | supports `inputs`                    |

## Filters

```yaml
on:
  push:
    branches: [main] # or branches-ignore
    tags: ['v*']
    paths: ['docs/**'] # or paths-ignore (not both on one event)
```

`paths` saves minutes: no CI run when only the README changed. Filters do not affect `schedule`.

## cron

```
┌─ minute (0-59)
│ ┌─ hour (0-23)
│ │ ┌─ day of month (1-31)
│ │ │ ┌─ month (1-12)
│ │ │ │ ┌─ day of week (0-6, Sun = 0)
* * * * *
```

| Expression     | Meaning            |
| -------------- | ------------------ |
| `0 6 * * 1`    | Mondays 06:00 UTC  |
| `*/15 * * * *` | every 15 minutes   |
| `0 0 1 * *`    | 1st of every month |

Scheduled runs can be delayed a few minutes at peak times, and are disabled after 60 days of repository inactivity (public repos).

## Manual inputs

```yaml
workflow_dispatch:
  inputs:
    name: { description: Name, default: 'Hero' }
    shout: { type: boolean }
```

Read with `${{ inputs.name }}` — pass it through `env` first ([06](06-secrets-vars-permissions.md)).

## Conditions with `if`

```yaml
- if: github.event_name == 'schedule'
  run: echo "scheduled run"
```

## Pitfalls

- The **Run workflow** button appears only after the file exists on the default branch.
- cron is UTC (Cairo: UTC+2, UTC+3 in summer).
- A wrong `paths` pattern makes the workflow silently never run.

## Exercise

Run it manually with `shout=true` and watch the name turn uppercase.

⬅️ [01 — Anatomy](01-anatomy.md) · ➡️ [03 — Contexts](03-contexts.md)
