🌐 **English** · [العربية](08-concurrency-timeouts.ar.md)

# 08 — Concurrency & Timeouts

> Workflow: [`08-concurrency.yml`](../.github/workflows/08-concurrency.yml)

**Goal:** stop wasting minutes on stale runs, avoid conflicting runs, and kill hung jobs.

## `concurrency`

Runs sharing a **group** don't run at the same time.

```yaml
concurrency:
  group: demo-${{ github.workflow }}-${{ github.ref }}
  cancel-in-progress: true
```

| Setting                     | Behavior                                                 |
| --------------------------- | -------------------------------------------------------- |
| `cancel-in-progress: true`  | a new run **cancels** the running one                    |
| `cancel-in-progress: false` | a new run **waits**; only the newest waiting run is kept |

Group names are expressions — one group per branch / PR / environment.

| Use for             | Setting | Why                            |
| ------------------- | ------- | ------------------------------ |
| CI, tests, previews | `true`  | only the latest commit matters |
| **Deploys**         | `false` | never cut a deploy in half     |

It works at workflow level (whole run) or job level (that job only — see the `queued` job).

## Timeouts

```yaml
jobs:
  slow:
    timeout-minutes: 5 # whole job (default is 360!)
    steps:
      - timeout-minutes: 1 # one step
        run: sleep 120
```

A timed-out job is marked failed and cancelled. Set a job timeout everywhere: the default is 6 hours of billed minutes.

## Pitfalls

- `cancel-in-progress: true` on deploys → half-finished deploy ([mistakes #14](mistakes-log.md)).
- A group that's too broad (a fixed string) makes unrelated runs cancel each other.
- Cancelled runs skip normal steps; use `if: always()` / `cancelled()` for cleanup.

## Exercise

Push two commits to `main` in quick succession and watch the first run get cancelled. Then run manually with `hang=true` and see the step time out after a minute.

⬅️ [07 — Artifacts](07-artifacts.md) · ➡️ [09 — Composite Actions](09-composite-actions.md)
