🌐 **English** · [العربية](05-conditionals-outputs.ar.md)

# 05 — Conditionals & Outputs

> Workflow: [`05-conditionals.yml`](../.github/workflows/05-conditionals.yml)

**Goal:** control which steps/jobs run, and pass values between steps and jobs.

```
 version ──► report          conditions (7 steps, if / failure() / always())
 (output)    (reads output)
```

## `if`

```yaml
- if: github.ref == 'refs/heads/main'
  run: echo "on main"
```

Write it as **one plain expression** (no `${{ }}` needed). A step/job has an implicit `success() &&`.

| Function      | True when                                   |
| ------------- | ------------------------------------------- |
| `success()`   | all previous steps/jobs succeeded (default) |
| `failure()`   | any previous step/job failed                |
| `always()`    | always — even on failure or cancellation    |
| `cancelled()` | the run was cancelled                       |

`always()` is for cleanup; `failure()` is for alerts.

## `continue-on-error`

```yaml
- continue-on-error: true
  run: exit 1 # job continues; counts as success
```

On a matrix job it keeps an experimental combination from failing the run ([04](04-ci-matrix-cache.md)).

## Step outputs

```yaml
- id: pkg
  run: echo "version=1.2.3" >> "$GITHUB_OUTPUT"
- run: echo "${{ steps.pkg.outputs.version }}"
```

Multi-line values use a delimiter:

```bash
{ echo "notes<<EOF"; cat notes.txt; echo "EOF"; } >> "$GITHUB_OUTPUT"
```

## Job outputs

Steps' outputs don't cross jobs by themselves — declare them on the job and read through `needs`:

```yaml
jobs:
  version:
    outputs:
      version: ${{ steps.pkg.outputs.version }}
  report:
    needs: version
    env:
      VERSION: ${{ needs.version.outputs.version }}
```

Outputs are **strings**. For files use [artifacts](07-artifacts.md).

## Summary & annotations

```bash
echo "### Result ✅" >> "$GITHUB_STEP_SUMMARY"   # Markdown on the run page
echo "::notice::done"  ·  "::warning::careful"  ·  "::error::broken"
echo "::group::title" … "::endgroup::"           # collapsible log section
echo "::add-mask::$VALUE"                         # hide a value
```

## Pitfalls

- `if: ${{ x }} == 'y'` is always true (non-empty string) — see [mistakes #9](mistakes-log.md).
- Output empty? You forgot job-level `outputs:` or `needs:` ([mistakes #10](mistakes-log.md)).
- A skipped job reports its outputs as empty strings.
- `failure()` doesn't fire for a step with `continue-on-error: true`.

## Exercise

Run it manually with `fail=true`; watch step 5 fail, step 6 (`failure()`) and 7 (`always()`) run.

⬅️ [04 — CI · Matrix · Cache](04-ci-matrix-cache.md) · ➡️ [06 — Secrets · Variables · Permissions](06-secrets-vars-permissions.md)
