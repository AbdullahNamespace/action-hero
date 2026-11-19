🌐 **English** · [العربية](10-deploy.ar.md)

# 10 — Deploy to Firebase Hosting

> Workflow: [`10-deploy.yml`](../.github/workflows/10-deploy.yml)
> Action: [`deploy-firebase-hosting`](../.github/actions/deploy-firebase-hosting/action.yml)

**Goal:** every push to `main` updates the live site. (PR previews → [11](11-previews.md); keyless auth → [14](14-oidc.md).)

## One-time setup

1. Create a project in the [Firebase Console](https://console.firebase.google.com) and enable **Hosting**.
2. **Project settings → Service accounts** → generate a JSON key; grant **Firebase Hosting Admin**.
3. GitHub → **Settings → Secrets and variables → Actions**:
   - Secret `FIREBASE_SERVICE_ACCOUNT` = the **content** of the JSON file
   - Variable `FIREBASE_PROJECT_ID` = project ID
4. Delete the JSON from your machine (or store it safely). **Never commit it.**

## Flow

```
push to main → checkout → setup-pnpm → build → prepare site/ → deploy → URL in summary
```

## The composite action

| Step                 | What it does                                                        | Why                              |
| -------------------- | ------------------------------------------------------------------- | -------------------------------- |
| Write credentials    | JSON → `$RUNNER_TEMP`, sets `GOOGLE_APPLICATION_CREDENTIALS`        | Firebase CLI reads a file        |
| Ensure firebase.json | creates a minimal config if missing                                 | no extra file needed in the repo |
| Deploy               | `deploy --only hosting`, or `hosting:channel:deploy` with a channel | depends on `channel-id`          |
| Cleanup              | deletes the credentials file (`if: always()`)                       | no secret left on the runner     |

| Input             | Required | Default | Description                                                       |
| ----------------- | -------- | ------- | ----------------------------------------------------------------- |
| `service-account` | ❌       | empty   | JSON content; empty = use existing credentials ([14](14-oidc.md)) |
| `project-id`      | ✅       | —       | Firebase project ID                                               |
| `public-dir`      | ❌       | `site`  | folder to publish                                                 |
| `channel-id`      | ❌       | empty   | empty = live deploy                                               |
| `expires`         | ❌       | `7d`    | preview channel lifetime                                          |

Output: `url` — the deployed address.

## About this project

The NestJS app is a **server**, not a static site, so `dist/` isn't deployable to Hosting. The workflow publishes a small `site/index.html` (commit + branch) to teach the deploy mechanics. For a real API use Cloud Run or Cloud Functions.

## Security

- The secret goes through `env`, never inline in `run`.
- `cancel-in-progress: false` prevents cutting a running deploy ([08](08-concurrency-timeouts.md)).
- A long-lived JSON key is the weak point → replace it with **OIDC** ([14](14-oidc.md)).
- Gate production with an **Environment** + approval ([13](13-environments.md)).

## Pitfalls

- Pasting the file **path** instead of its **content** into the Secret.
- Hosting not enabled / missing role → 403.
- Composite actions can't see `secrets` — pass them via `inputs`.

## Exercise

Push a change to `main`, open the run's summary and click the deployed URL.

⬅️ [09 — Composite Actions](09-composite-actions.md) · ➡️ [11 — Previews](11-previews.md)
