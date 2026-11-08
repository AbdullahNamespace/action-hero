🌐 **English** · [العربية](04-ci-matrix-cache.ar.md)

# 04 — CI · Matrix · Cache

> Workflow: [`04-ci.yml`](../.github/workflows/04-ci.yml)

**Goal:** a real CI pipeline for the NestJS app: lint, test, then build.

```
 lint ──┐
 test ──┼──► build        test runs on Node 22 + 24
 e2e  ──┘                 (+ 26 as experimental)
```

The first three run **in parallel**; `build` waits for all of them.

## Concepts

**`needs`** — `needs: [lint, test, e2e]`. If any fails, `build` is skipped.

**`strategy.matrix`**

```yaml
strategy:
  fail-fast: false # let the others finish
  matrix:
    node: ['22', '24']
    include: # add a one-off combination
      - node: '26'
        experimental: true
    # exclude: [{ os: windows-latest, node: "22" }]   # remove a combination
```

Use `${{ matrix.node }}` in steps. Quote versions (`"22"`), or `20.10` becomes a float.
`continue-on-error: ${{ matrix.experimental || false }}` keeps an experimental failure from breaking the run.

**Cache** — `setup-pnpm` uses `cache: pnpm` from `actions/setup-node`; the key comes from `pnpm-lock.yaml`. Manual form:

```yaml
- uses: actions/cache@v6
  with:
    path: ~/.local/share/pnpm/store
    key: pnpm-${{ runner.os }}-${{ hashFiles('pnpm-lock.yaml') }}
```

**`concurrency`** — a new push cancels the older run on the same ref:

```yaml
concurrency:
  group: ci-${{ github.workflow }}-${{ github.ref }}
  cancel-in-progress: true
```

**Local composite action** — `uses: ./.github/actions/setup-pnpm` replaces 3 repeated steps. Run `checkout` **before** it.

## Pitfalls

- `pnpm/action-setup` must come **before** `setup-node` with `cache: pnpm`.
- `--frozen-lockfile` fails if `package.json` changed without updating `pnpm-lock.yaml`.
- Each job needs its own `checkout` and install (separate machines).
- Node 20 reached end of life in April 2026 — use supported versions.

## Exercise

- Add `"26"` to the main list and watch a new job appear.
- Break a test on purpose and confirm `build` is skipped.

⬅️ [03 — Contexts](03-contexts.md) · ➡️ [05 — Conditionals & Outputs](05-conditionals-outputs.md)
