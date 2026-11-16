🌐 **English** · [العربية](07-artifacts.ar.md)

# 07 — Artifacts

> Workflow: [`07-artifacts.yml`](../.github/workflows/07-artifacts.yml)

**Goal:** move output files between jobs, or keep them for download.

**Problem:** every job runs on a separate machine; what `build` creates doesn't exist in `verify`. Solution: upload as an artifact, then download.

```
 build job                     verify job
 pnpm build                    download-artifact
 upload-artifact ──► storage ──► check files
```

## Commands

```yaml
# upload
- uses: actions/upload-artifact@v7
  with:
    name: dist-${{ github.sha }}
    path: dist/
    retention-days: 3
    if-no-files-found: error

# download
- uses: actions/download-artifact@v8
  with:
    name: dist-${{ github.sha }}
    path: downloaded-dist
```

## Notes

- Artifact names must be **unique** within a run — add `github.sha` or `matrix.node`.
- `retention-days` controls how long it's kept (default 90; counts against storage).
- `if-no-files-found: error` prevents silent success.
- You can also download artifacts manually from the run page.
- Never upload files containing secrets (`.env`).

## Artifact vs Cache

|                 | Artifact                   | Cache                         |
| --------------- | -------------------------- | ----------------------------- |
| Purpose         | an **output** to pass/keep | **dependencies**, faster runs |
| Manual download | yes                        | no                            |
| Example         | `dist/`                    | pnpm store                    |

## Pitfalls

- Missing `needs` → `verify` starts before `build` finishes.
- Uploading huge folders (`node_modules`) for no reason.
- Same name from parallel jobs → conflict error.

## Exercise

Run it, then download the `dist-...` artifact from the run page and open it locally.

⬅️ [06 — Secrets · Variables · Permissions](06-secrets-vars-permissions.md) · ➡️ [08 — Concurrency & Timeouts](08-concurrency-timeouts.md)
