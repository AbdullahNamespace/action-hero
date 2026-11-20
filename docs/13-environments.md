🌐 **English** · [العربية](13-environments.ar.md)

# 13 — Environments & Approvals

> Workflow: [`13-environments.yml`](../.github/workflows/13-environments.yml)

**Goal:** protect production: staging goes automatically, production waits for a human.

```
 staging (auto) ──► production (⏸️ waits for approval)
```

## Setup (Settings → Environments)

| Environment  | Protection                                                                      |
| ------------ | ------------------------------------------------------------------------------- |
| `staging`    | none                                                                            |
| `production` | **Required reviewers** (you), optional wait timer, deployment branches = `main` |

An environment referenced in a workflow but not yet created is created **without** protection.
Protection rules on private repositories may require a paid plan.

## In the workflow

```yaml
jobs:
  production:
    needs: staging
    environment:
      name: production
      url: https://example.com # shown on the run and in Deployments
```

The job **pauses before it starts**; reviewers approve or reject from the run page.

## Environment secrets and variables

Same names, different values per environment — and only jobs targeting that environment can read them:

```yaml
env:
  ENV_SECRET: ${{ secrets.ENV_SECRET }} # staging value or production value
  ENV_NAME: ${{ vars.ENV_NAME }}
```

Keep production credentials **only** in the production environment.

## Protection rules

| Rule                | Effect                                            |
| ------------------- | ------------------------------------------------- |
| Required reviewers  | up to 6 people/teams; any one approval is enough  |
| Wait timer          | delay before the job starts                       |
| Deployment branches | only listed branches/tags may deploy              |
| Prevent self-review | the person who triggered the run can't approve it |

## Pitfalls

- Reviewers enabled but nobody assigned → waits forever ([mistakes #19](mistakes-log.md)).
- Secrets placed at repository level are visible to every job — move production ones into the environment.
- A job that doesn't declare `environment:` can't read that environment's secrets.

## Exercise

Create both environments, add a `production` reviewer, run the workflow, and approve from the run page.

⬅️ [12 — Reusable Workflows](12-reusable-workflows.md) · ➡️ [14 — OIDC](14-oidc.md)
