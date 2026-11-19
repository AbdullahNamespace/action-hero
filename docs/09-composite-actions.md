🌐 **English** · [العربية](09-composite-actions.ar.md)

# 09 — Composite Actions

> Workflow: [`09-composite.yml`](../.github/workflows/09-composite.yml)
> Action: [`setup-pnpm`](../.github/actions/setup-pnpm/action.yml)

**Goal:** package repeated **steps** into one reusable action.

## Anatomy

```yaml
# .github/actions/setup-pnpm/action.yml
name: Setup pnpm
description: Install pnpm and Node.js with caching, then optionally install dependencies.
inputs:
  node-version:
    description: Node.js version to install.
    required: false
    default: '22'
  install:
    description: Run `pnpm install --frozen-lockfile` when set to "true".
    required: false
    default: 'true'
outputs:
  pnpm-version:
    description: The installed pnpm version.
    value: ${{ steps.ver.outputs.version }}
runs:
  using: composite
  steps:
    - uses: pnpm/action-setup@v4
    - uses: actions/setup-node@v7
      with: { node-version: '${{ inputs.node-version }}', cache: pnpm }
    - if: inputs.install == 'true'
      shell: bash
      run: pnpm install --frozen-lockfile
    - id: ver
      shell: bash
      run: echo "version=$(pnpm --version)" >> "$GITHUB_OUTPUT"
```

## Using it

```yaml
- uses: actions/checkout@v7 # FIRST — the action lives in the repo
- id: setup
  uses: ./.github/actions/setup-pnpm
  with:
    node-version: '24'
- run: echo "${{ steps.setup.outputs.pnpm-version }}"
```

## Rules

- `runs.using: composite` + `steps`.
- `name` and `description` are required for the action, and `description` is required for every input and output.
- **`shell:` is mandatory** on every `run` step.
- Inputs are always strings (`"true"`, not `true`).
- Outputs must be mapped from a step: `value: ${{ steps.<id>.outputs.x }}`.
- **No automatic `secrets`** — pass them as inputs.
- Files shipped next to the action: `${{ github.action_path }}`.
- Local path (`./.github/actions/x`) needs `checkout` first; a published action uses `owner/repo/path@ref`.

## Composite vs Reusable workflow

|             | Composite action       | Reusable workflow ([12](12-reusable-workflows.md)) |
| ----------- | ---------------------- | -------------------------------------------------- |
| Bundles     | **steps**              | **jobs**                                           |
| Called with | `uses:` inside `steps` | `uses:` on a **job**                               |
| Runs on     | the caller's runner    | its own runner(s)                                  |
| Secrets     | via inputs             | `secrets:` / `inherit`                             |
| Use when    | repeating a few steps  | sharing a whole pipeline                           |

For logic in JavaScript or Docker → [15](15-release-custom-actions.md).

## Pitfalls

- Missing `shell` → `Required property is missing: shell` ([mistakes #16](mistakes-log.md)).
- Local action called before `checkout` → `Can't find 'action.yml'` ([mistakes #15](mistakes-log.md)).
- Order matters inside `setup-pnpm`: `pnpm/action-setup` before `setup-node` with `cache: pnpm`.

## Exercise

Add an input `run-build` (default `"false"`) to `setup-pnpm` that runs `pnpm build` when `"true"`.

⬅️ [08 — Concurrency & Timeouts](08-concurrency-timeouts.md) · ➡️ [10 — Deploy](10-deploy.md)
