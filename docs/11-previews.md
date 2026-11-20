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

## The general concept: Preview Environments

"A temporary URL per PR" is not a Firebase-only idea. It is generally called a **Preview Environment** (or Ephemeral Environment), and each platform has its own name for it:

| Platform                  | Their name                       | Note                                                               |
| ------------------------- | -------------------------------- | ------------------------------------------------------------------ |
| Firebase Hosting          | Preview Channels                 | we deploy it ourselves in this lesson via `hosting:channel:deploy` |
| Vercel                    | Preview Deployments              | automatic once the repo is connected                               |
| Netlify                   | Deploy Previews                  | automatic; also supports Branch Deploys                            |
| Cloudflare Pages          | Preview Deployments              | automatic for every branch and PR                                  |
| AWS Amplify               | Pull Request Previews            | enabled in the app settings                                        |
| Azure Static Web Apps     | Staging Environments             | created per PR by the workflow                                     |
| Kubernetes (ArgoCD, etc.) | Ephemeral / Preview Environments | a full environment (app + database) per PR                         |

> The name differs, the idea is the same: **own URL + limited lifetime + never touches the live site.**
> Some platforms (Vercel, Netlify) do this with no workflow at all; others (Firebase) need us to build it, as this lesson does.

## Key parts

**Channel** — Firebase's term for a preview: a version of the site with its own URL:

```yaml
- uses: ./.github/actions/deploy-firebase-hosting
  with:
    channel-id: pr-${{ github.event.pull_request.number }}
    expires: 7d
```

Redeploying to the same channel updates it. Expired channels disappear; delete one earlier with `firebase hosting:channel:delete pr-12 --project <id>`.

**Comment** — needs `pull-requests: write`, plus a token passed to `gh` via `GH_TOKEN`. `--edit-last` updates **the last comment written by the same user** (here, the bot) instead of adding a new one per push:

```bash
gh pr comment "$PR" --edit-last --body "$BODY" || gh pr comment "$PR" --body "$BODY"
```

**Concurrency** — one group per PR; a new push cancels the older preview (`cancel-in-progress: true`), which is safe because it is not the live site. For the difference between cancelling and queueing, and why live deploys must not be cancelled, see [08 — Concurrency & Timeouts](08-concurrency-timeouts.md).

## Forks and secrets

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
- Forgetting `GH_TOKEN` → `gh` fails because it finds no token.
- Comments piling up: use `--edit-last`.

## Exercise

Open a PR and find the preview link in the comment; push again and watch the same comment update.

⬅️ [10 — Deploy](10-deploy.md) · ➡️ [12 — Reusable Workflows](12-reusable-workflows.md)
