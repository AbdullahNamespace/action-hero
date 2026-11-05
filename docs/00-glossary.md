🌐 **English** · [العربية](00-glossary.ar.md)

# 📖 00 — Glossary

Core GitHub Actions terms, in lesson order. The **Lesson** column links to where each is explained.

> 🤕 Stuck on a term? See also the [mistakes log](mistakes-log.md).

**Contents**

| Section                                            |
| -------------------------------------------------- |
| [🌱 Basics](#-basics)                              |
| [🛠️ CI & flow control](#️-ci--flow-control)         |
| [🔐 Security](#-security)                          |
| [📦 Data & run control](#-data--run-control)       |
| [🚀 Reuse & delivery](#-reuse--delivery)           |
| [🏆 Production & advanced](#-production--advanced) |

---

## 🌱 Basics

| Term                     | Meaning                                                                                                                                | Example                                                                  | Lesson                             |
| ------------------------ | -------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------ | ---------------------------------- |
| **Workflow**             | A YAML file describing an automated process, in `.github/workflows/`                                                                   | `04-ci.yml`                                                              | [01](01-anatomy.md)                |
| **YAML**                 | The text format workflow files are written in; it relies on indentation (2 spaces, never Tab)                                          | `name: CI`                                                               | [01](01-anatomy.md)                |
| **Workflow run**         | One actual execution of a workflow (has an id, log, result)                                                                            | Run #42                                                                  | [01](01-anatomy.md)                |
| **Event / Trigger**      | What starts a workflow                                                                                                                 | `push`, `pull_request`                                                   | [02](02-triggers.md)               |
| **Job**                  | A set of steps on one machine; jobs run in parallel by default                                                                         | `lint`, `build`                                                          | [01](01-anatomy.md)                |
| **Step**                 | One unit inside a job: either `run` or `uses`                                                                                          | `run: pnpm test`                                                         | [01](01-anatomy.md)                |
| **Runner**               | The machine (VM) that executes a job                                                                                                   | `ubuntu-latest`                                                          | [01](01-anatomy.md)                |
| **`runs-on`**            | The field that picks which runner executes the job                                                                                     | `runs-on: ubuntu-latest`                                                 | [01](01-anatomy.md)                |
| **GitHub-hosted runner** | A runner managed by GitHub, created clean per job then destroyed                                                                       | `ubuntu-latest`                                                          | [01](01-anatomy.md)                |
| **Self-hosted runner**   | A runner on your own machine; you maintain and secure it                                                                               | `runs-on: self-hosted`                                                   | [15](15-release-custom-actions.md) |
| **Action**               | A reusable unit called with `uses`                                                                                                     | `actions/checkout@v7`                                                    | [01](01-anatomy.md)                |
| **`workflow_dispatch`**  | Manual trigger (Run workflow button); accepts `inputs`                                                                                 | —                                                                        | [02](02-triggers.md)               |
| **Default branch**       | The repository's main branch (usually `main`); some triggers such as `workflow_dispatch` and `schedule` read their config from it only | `main`                                                                   | [02](02-triggers.md)               |
| **Filters**              | Conditions on a trigger: `branches` / `tags` / `paths`                                                                                 | `paths: ['src/**']`                                                      | [02](02-triggers.md)               |
| **cron**                 | 5-field schedule syntax (UTC)                                                                                                          | `0 6 * * 1`                                                              | [02](02-triggers.md)               |
| **Context**              | Data objects available inside `${{ }}`                                                                                                 | `github`, `env`, `matrix`, `needs`, `steps`, `runner`, `vars`, `secrets` | [03](03-contexts.md)               |
| **Expression**           | Something evaluated inside `${{ ... }}`                                                                                                | `${{ github.sha }}`                                                      | [03](03-contexts.md)               |
| **Function**             | Built-in helpers for expressions                                                                                                       | `contains()`, `startsWith()`, `format()`, `toJSON()`, `hashFiles()`      | [03](03-contexts.md)               |
| **Script injection**     | Vulnerability from putting attacker-controlled text (e.g. a PR title) directly in `run:`                                               | Fix: pass it via `env:`                                                  | [03](03-contexts.md)               |

---

## 🛠️ CI & flow control

| Term                      | Meaning                                                                                    | Example                                             | Lesson                           |
| ------------------------- | ------------------------------------------------------------------------------------------ | --------------------------------------------------- | -------------------------------- |
| **Matrix**                | Run the same job across combinations (e.g. Node versions)                                  | `node: [22, 24]`                                    | [04](04-ci-matrix-cache.md)      |
| **`fail-fast`**           | Cancel remaining matrix jobs when one fails                                                | `fail-fast: false`                                  | [04](04-ci-matrix-cache.md)      |
| **`include` / `exclude`** | Add one-off combinations to / remove combinations from a matrix                            | —                                                   | [04](04-ci-matrix-cache.md)      |
| **`needs`**               | Make a job wait for other jobs to succeed                                                  | `needs: [lint, test]`                               | [04](04-ci-matrix-cache.md)      |
| **Cache**                 | Stored dependencies to speed up runs                                                       | pnpm store                                          | [04](04-ci-matrix-cache.md)      |
| **Cache key**             | Decides whether the cache is restored or rebuilt                                           | `hashFiles('pnpm-lock.yaml')`                       | [04](04-ci-matrix-cache.md)      |
| **Frozen lockfile**       | Install that refuses to modify the lockfile                                                | `pnpm install --frozen-lockfile`                    | [04](04-ci-matrix-cache.md)      |
| **`if`**                  | Condition deciding whether a step/job runs                                                 | `if: github.ref == 'refs/heads/main'`               | [05](05-conditionals-outputs.md) |
| **Status functions**      | Functions that check previous results                                                      | `success()`, `failure()`, `always()`, `cancelled()` | [05](05-conditionals-outputs.md) |
| **`continue-on-error`**   | Let the workflow continue even if this step/job fails                                      | —                                                   | [05](05-conditionals-outputs.md) |
| **`GITHUB_OUTPUT`**       | File you append to in order to set a step output                                           | `echo "v=1.2" >> "$GITHUB_OUTPUT"`                  | [05](05-conditionals-outputs.md) |
| **`GITHUB_ENV`**          | File you append to in order to set an environment variable for later steps in the same job | `echo "NODE_ENV=test" >> "$GITHUB_ENV"`             | [05](05-conditionals-outputs.md) |
| **Step output**           | Value a step produces for later steps in the same job                                      | `steps.ver.outputs.v`                               | [05](05-conditionals-outputs.md) |
| **Job output**            | Value a job declares (`outputs:`) for other jobs via `needs`                               | `needs.build.outputs.version`                       | [05](05-conditionals-outputs.md) |
| **`GITHUB_STEP_SUMMARY`** | Markdown file shown as a summary on the run page                                           | —                                                   | [05](05-conditionals-outputs.md) |
| **Workflow command**      | Special line a step prints for GitHub to act on                                            | `::error::message`                                  | [05](05-conditionals-outputs.md) |

---

## 🔐 Security

| Term                            | Meaning                                                                                                                                                                                   | Example                            | Lesson                               |
| ------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------- | ------------------------------------ |
| **Secret**                      | Encrypted sensitive value stored in settings; masked in logs                                                                                                                              | `secrets.FIREBASE_SERVICE_ACCOUNT` | [06](06-secrets-vars-permissions.md) |
| **Variable**                    | Non-sensitive value stored in settings (visible in logs)                                                                                                                                  | `vars.FIREBASE_PROJECT_ID`         | [06](06-secrets-vars-permissions.md) |
| **`env`**                       | Environment variables defined in the file (workflow, job or step level)                                                                                                                   | `env: NODE_ENV: test`              | [06](06-secrets-vars-permissions.md) |
| **Masking**                     | Replacing a secret's value with `***` in logs                                                                                                                                             | —                                  | [06](06-secrets-vars-permissions.md) |
| **`GITHUB_TOKEN`**              | Temporary token created automatically for each job; expires when the job ends                                                                                                             | `secrets.GITHUB_TOKEN`             | [06](06-secrets-vars-permissions.md) |
| **Personal Access Token (PAT)** | A personal token with broader permissions than `GITHUB_TOKEN`; needed e.g. to reach another repository                                                                                    | `secrets.MY_PAT`                   | [06](06-secrets-vars-permissions.md) |
| **SSO / SAML SSO**              | Single sign-on enforced by an organization through an identity provider (e.g. Okta). When enabled, a PAT must be authorized for the organization before use; `GITHUB_TOKEN` is unaffected | —                                  | [06](06-secrets-vars-permissions.md) |
| **Permissions**                 | What the `GITHUB_TOKEN` may do, per workflow or job                                                                                                                                       | `contents: read`                   | [06](06-secrets-vars-permissions.md) |
| **Least privilege**             | Grant only the minimum permissions needed                                                                                                                                                 | `permissions: {}` then add         | [06](06-secrets-vars-permissions.md) |
| **Fork**                        | Someone else's copy of the repo. Runs from fork PRs **get no secrets** and a read-only token                                                                                              | —                                  | [11](11-previews.md)                 |

---

## 📦 Data & run control

| Term                  | Meaning                                                                          | Example                    | Lesson                           |
| --------------------- | -------------------------------------------------------------------------------- | -------------------------- | -------------------------------- |
| **Artifact**          | Output files saved for download or for passing to another job                    | `dist/`                    | [07](07-artifacts.md)            |
| **Retention**         | Days an artifact is kept before automatic deletion                               | `retention-days: 7`        | [07](07-artifacts.md)            |
| **Artifact vs Cache** | An artifact is an **output** to deliver; a cache is **dependencies** to speed up | —                          | [07](07-artifacts.md)            |
| **Concurrency**       | Prevent/cancel overlapping similar runs                                          | `cancel-in-progress`       | [08](08-concurrency-timeouts.md) |
| **Concurrency group** | The name under which runs count as "similar"                                     | `group: ${{ github.ref }}` | [08](08-concurrency-timeouts.md) |
| **`timeout-minutes`** | Maximum duration of a job/step before it is stopped                              | `timeout-minutes: 10`      | [08](08-concurrency-timeouts.md) |

---

## 🚀 Reuse & delivery

| Term                      | Meaning                                                                                     | Example                                            | Lesson                         |
| ------------------------- | ------------------------------------------------------------------------------------------- | -------------------------------------------------- | ------------------------------ |
| **Composite Action**      | An action you write from several **steps** (used inside a job)                              | `.github/actions/setup-pnpm`                       | [09](09-composite-actions.md)  |
| **Action inputs/outputs** | An action's `inputs:` / `outputs:` in `action.yml`                                          | `${{ inputs.node-version }}`                       | [09](09-composite-actions.md)  |
| **`shell`**               | Mandatory on every `run` step inside a composite action                                     | `shell: bash`                                      | [09](09-composite-actions.md)  |
| **Deploy**                | Publishing a version of the app to a real environment                                       | —                                                  | [10](10-deploy.md)             |
| **Firebase Hosting**      | Google's hosting service for front-ends                                                     | —                                                  | [10](10-deploy.md)             |
| **Service Account**       | Google Cloud account for automation (long-lived JSON key)                                   | `FIREBASE_SERVICE_ACCOUNT`                         | [10](10-deploy.md)             |
| **Preview channel**       | Temporary deployment with its own URL, one per PR                                           | `expires: 7d`                                      | [11](11-previews.md)           |
| **`pull_request_target`** | Trigger running in the base repo's context (has secrets) — risky with fork code             | —                                                  | [11](11-previews.md)           |
| **Reusable Workflow**     | A whole workflow called from another, at the **job** level                                  | `uses: ./.github/workflows/12-reusable-called.yml` | [12](12-reusable-workflows.md) |
| **`workflow_call`**       | The trigger that makes a workflow callable                                                  | `on: workflow_call`                                | [12](12-reusable-workflows.md) |
| **Caller / Called**       | The calling workflow / the workflow being called                                            | `12-reusable.yml` / `12-reusable-called.yml`       | [12](12-reusable-workflows.md) |
| **`secrets: inherit`**    | Pass all of the caller's secrets to the called workflow                                     | —                                                  | [12](12-reusable-workflows.md) |
| **Environment**           | A named target (production) that can be protected with approvals                            | `environment: production`                          | [13](13-environments.md)       |
| **Required reviewers**    | People who must approve before a job tied to an environment starts                          | —                                                  | [13](13-environments.md)       |
| **Review deployments**    | Button on the run page to approve or reject a job waiting for a reviewer                    | —                                                  | [13](13-environments.md)       |
| **Prevent self-review**   | Environment option that stops the person who triggered the run from approving their own run | —                                                  | [13](13-environments.md)       |
| **Environment secret**    | A secret scoped to a single environment                                                     | —                                                  | [13](13-environments.md)       |

---

## 🏆 Production & advanced

| Term                             | Meaning                                                                                                        | Example                         | Lesson                             |
| -------------------------------- | -------------------------------------------------------------------------------------------------------------- | ------------------------------- | ---------------------------------- |
| **OIDC**                         | OpenID Connect: the workflow proves its identity to the cloud with a short-lived token instead of a static key | —                               | [14](14-oidc.md)                   |
| **`id-token: write`**            | Permission that lets a job request an OIDC token                                                               | `permissions: id-token: write`  | [14](14-oidc.md)                   |
| **Workload Identity Federation** | Maps a GitHub identity to a Google Cloud service account, keylessly                                            | WIF                             | [14](14-oidc.md)                   |
| **Tag**                          | A fixed label on a commit, used for releases                                                                   | `v1.0.0`                        | [15](15-release-custom-actions.md) |
| **Release**                      | A published GitHub release tied to a tag, with notes and files                                                 | —                               | [15](15-release-custom-actions.md) |
| **GHCR**                         | GitHub Container Registry: Docker image hosting from GitHub                                                    | `ghcr.io/<user>/action-hero`    | [15](15-release-custom-actions.md) |
| **Marketplace**                  | GitHub's store of ready-made actions published by others                                                       | —                               | [15](15-release-custom-actions.md) |
| **Third-party action**           | An action from outside `actions/*`; untrusted code, so pin it by SHA                                           | `uses: owner/repo@<sha>`        | [15](15-release-custom-actions.md) |
| **Custom Action**                | An action written in JavaScript or Docker (more powerful than composite)                                       | `.github/actions/hello-custom`  | [15](15-release-custom-actions.md) |
| **Dependabot**                   | Opens PRs automatically to update dependencies and actions                                                     | `.github/dependabot.yml`        | [15](15-release-custom-actions.md) |
| **Pinning by SHA**               | Pin an action to a full commit SHA instead of a movable tag                                                    | `uses: actions/checkout@<sha>`  | [15](15-release-custom-actions.md) |
| **Container job**                | A whole job that runs inside a Docker image (`container:`)                                                     | `container: { image: node:24 }` | [15](15-release-custom-actions.md) |
| **`docker://` step**             | A single step that runs inside a ready-made Docker image                                                       | `uses: docker://alpine:3`       | [15](15-release-custom-actions.md) |
