🌐 **English** · [العربية](06-secrets-vars-permissions.ar.md)

# 06 — Secrets · Variables · Permissions

> Workflow: [`06-secrets.yml`](../.github/workflows/06-secrets.yml)

**Goal:** protect secrets and limit the damage if something leaks.

## Three sources of values

| Source      | Defined in                     | Sensitive? | In logs | Access             |
| ----------- | ------------------------------ | ---------- | ------- | ------------------ |
| `env`       | the workflow file              | no         | visible | `$APP_ENV`         |
| `vars.*`    | Settings → Variables → Actions | no         | visible | `${{ vars.X }}`    |
| `secrets.*` | Settings → Secrets → Actions   | **yes**    | `***`   | `${{ secrets.X }}` |

Pass them through `env` and read them as shell variables:

```yaml
env:
  DEMO_SECRET: ${{ secrets.DEMO_SECRET }}
  DEMO_VAR: ${{ vars.DEMO_VAR }}
```

## Masking

GitHub replaces the secret value with `***` in logs, but it:

- ❌ misses transformed copies (base64, split, reversed)
- ❌ cannot stop you from sending the secret to an external service
- ✅ can mask a generated value: `echo "::add-mask::$VALUE"`

## Least privilege

`GITHUB_TOKEN` is created per run. Restrict it:

```yaml
permissions: {} # file level: nothing

jobs:
  my-job:
    permissions:
      contents: read # job level: only what's needed
```

| Permission             | Allows                     |
| ---------------------- | -------------------------- |
| `contents: read`       | read code (checkout)       |
| `contents: write`      | push, create releases      |
| `pull-requests: write` | comment on PRs             |
| `packages: write`      | push to GHCR               |
| `id-token: write`      | OIDC (keyless cloud login) |

Error `Resource not accessible by integration` = a missing permission.

## Script injection

`${{ }}` is substituted as text **before** the shell runs, so a crafted PR title can run commands.

```yaml
# ❌ dangerous
run: echo "${{ github.event.pull_request.title }}"

# ✅ safe
env:
  TITLE: ${{ github.event.pull_request.title }}
run: echo "$TITLE"
```

## Forks

- `pull_request` from a fork gets **no secrets** and a read-only token.
- Avoid `pull_request_target` unless you fully understand the risk.

## Pinning actions

`@v7` is fine for official actions. For third-party actions, pin a **full commit SHA**.

## Checklist

- [ ] `permissions` set explicitly in every workflow
- [ ] no external input directly inside `run`
- [ ] secrets passed via `env`
- [ ] production secrets behind an Environment with approval ([13](13-environments.md))
- [ ] never print secrets, even masked

## Exercise

Create `DEMO_SECRET` and `DEMO_VAR`, run the workflow, and compare how each appears in the log.

⬅️ [05 — Conditionals & Outputs](05-conditionals-outputs.md) · ➡️ [07 — Artifacts](07-artifacts.md)
