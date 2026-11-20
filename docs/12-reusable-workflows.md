🌐 **English** · [العربية](12-reusable-workflows.ar.md)

# 12 — Reusable Workflows

> Caller: [`12-reusable.yml`](../.github/workflows/12-reusable.yml)
> Called: [`12-reusable-called.yml`](../.github/workflows/12-reusable-called.yml)

**Goal:** write a pipeline once, call it from many workflows.

## The called workflow

```yaml
on:
  workflow_call:
    inputs:
      script: { type: string, required: true }
      node-version: { type: string, default: '22' }
    secrets: # optional: declare explicit secrets
      DEMO_SECRET: { required: false }
    outputs:
      pnpm-version:
        value: ${{ jobs.run.outputs.pnpm-version }}
```

## The caller — `uses` on a **job**

```yaml
jobs:
  lint:
    uses: ./.github/workflows/12-reusable-called.yml
    with:
      script: lint
    secrets: inherit
  report:
    needs: lint
    runs-on: ubuntu-latest
    steps:
      - run: echo "${{ needs.lint.outputs.pnpm-version }}"
```

## Rules

- The caller job has `uses` + `with` + `secrets` — **no `steps`, no `runs-on`**.
- Same repo: `./.github/workflows/x.yml`. Other repo: `owner/repo/.github/workflows/x.yml@ref`.
- The file must sit **directly** in `.github/workflows/` (no subfolders).
- Secrets are **not** passed automatically: list them under `secrets:` or use `secrets: inherit`.
- Permissions in the called workflow can only stay equal or get narrower.
- Outputs flow: step → `jobs.<id>.outputs` → `on.workflow_call.outputs` → `needs.<caller job>.outputs`.

## Reusable workflow vs Composite action

|           | Reusable workflow      | Composite action ([09](09-composite-actions.md)) |
| --------- | ---------------------- | ------------------------------------------------ |
| Bundles   | **jobs** (own runners) | **steps** (caller's runner)                      |
| Called at | job level              | step level                                       |
| Best for  | whole pipelines        | a few repeated steps                             |

## Pitfalls

- Forgot `on: workflow_call`, or put `steps` in the caller job ([mistakes #18](mistakes-log.md)).
- Secret empty in the called workflow → add `secrets: inherit`.
- Workflow stored in a subfolder like `workflows/reuseble/` → not found.

## Exercise

Call the workflow a third time with `script: build`, and print the output in `report`.

⬅️ [11 — Previews](11-previews.md) · ➡️ [13 — Environments](13-environments.md)
