🌐 **English** · [العربية](11-previews.ar.md)

# 11 — Previews

> Workflow: [`11-preview.yml`](../.github/workflows/11-preview.yml)

**Goal:** every PR gets its own temporary URL, posted as a PR comment.

## Flow

```
PR opened/updated → build → deploy to channel pr-<number> → comment the URL
                                      │
                              expires after 7 days
```

## Key parts

**Channel** — a preview version of the site with its own URL:

```yaml
- uses: ./.github/actions/deploy-firebase-hosting
  with:
    channel-id: pr-${{ github.event.pull_request.number }}
    expires: 7d
```

Redeploying to the same channel updates it. Expired channels disappear; delete one earlier with `firebase hosting:channel:delete pr-12 --project <id>`.

**Comment** — needs `pull-requests: write`. `--edit-last` updates one comment instead of adding a new one per push:

```bash
gh pr comment "$PR" --edit-last --body "$BODY" || gh pr comment "$PR" --body "$BODY"
```

**Concurrency** — one group per PR; a new push cancels the older preview (safe: it's not the live site).

## 🔒 Forks and secrets

| Trigger               | Secrets for fork PRs | Verdict                                   |
| --------------------- | -------------------- | ----------------------------------------- |
| `pull_request`        | ❌ none              | safe — **use this**, skip forks with `if` |
| `pull_request_target` | ✅ yes               | dangerous with untrusted code — avoid     |

```yaml
if: github.event.pull_request.head.repo.full_name == github.repository
```

## Pitfalls

- Fork PR → empty secret → deploy fails ([mistakes #17](mistakes-log.md)).
- Missing `pull-requests: write` → `Resource not accessible by integration`.
- Comments piling up: use `--edit-last`.

## Exercise

Open a PR and find the preview link in the comment; push again and watch the same comment update.

⬅️ [10 — Deploy](10-deploy.md) · ➡️ [12 — Reusable Workflows](12-reusable-workflows.md)
