🌐 **English** · [العربية](mistakes-log.ar.md)

# 🤕 Mistakes Log

> Every mistake is a free lesson. Each problem, its cause and its fix are recorded here.

**Contents**

| Section                                 | What's in it                                                  |
| --------------------------------------- | ------------------------------------------------------------- |
| [🌍 Common problems](#-common-problems) | Mistakes most learners hit, grouped by lesson                 |
| [🧑‍💻 My own problems](#-my-own-problems) | Mistakes I actually hit, with date and the real error message |

> 📖 Unknown term? See the [glossary](00-glossary.md).

---

## 🌍 Common problems

> Read these before you hit them.

### 📝 Template

```md
### N. Short title

- **Lesson:** [0X — Name](0X-name.md)
- **Symptom:** what you see
- **Cause:** why it happens
- **Fix:** what to change
- **Takeaway:** one sentence
```

---

### 🌱 Basics (01–03)

### 1. The workflow doesn't show in the Actions tab

- **Lesson:** [01 — Anatomy](01-anatomy.md)
- **Symptom:** pushed the file, nothing happens.
- **Cause:** file is outside `.github/workflows/`, wrong extension (`.yaml.txt`), or invalid YAML.
- **Fix:** check the path and extension, and check the indentation in the file.
- **Takeaway:** the file must live in `.github/workflows/`, end in `.yml` or `.yaml`, and be valid YAML (2 spaces, never Tab).

### 2. `ls` shows no project files

- **Lesson:** [01 — Anatomy](01-anatomy.md)
- **Cause:** missing `actions/checkout`.
- **Fix:** add `uses: actions/checkout@v7` as the first step (check the latest version; Dependabot will keep it updated).
- **Takeaway:** the runner always starts empty.

### 3. Run workflow button is missing

- **Lesson:** [02 — Triggers](02-triggers.md)
- **Cause:** `workflow_dispatch` isn't on the default branch yet.
- **Fix:** merge the file into `main` first.
- **Takeaway:** some triggers read their config from the default branch only.

### 4. cron ran at an unexpected time (or stopped)

- **Lesson:** [02 — Triggers](02-triggers.md)
- **Cause:** schedules use UTC, can be delayed a few minutes at peak, and are disabled after 60 days of repository inactivity (public repos).
- **Fix:** convert the time manually; re-enable the workflow from the Actions tab.
- **Takeaway:** comment the intended time in UTC next to the cron; don't rely on it for second-level precision.

### 5. Workflow doesn't run on push (`paths` issue)

- **Lesson:** [02 — Triggers](02-triggers.md)
- **Cause:** `paths: ['src/**']` but the changed files are elsewhere.
- **Fix:** review the patterns (`**` matches any depth) or test with `workflow_dispatch`.
- **Takeaway:** filters cut runs, but a wrong one makes the workflow silently never run.

### 6. Script injection: user value inside `run:`

- **Lesson:** [03 — Contexts](03-contexts.md)
- **Symptom:** `${{ github.event.pull_request.title }}` directly in `run:` can execute commands hidden in the title.
- **Cause:** the expression is substituted as text before the script runs.
- **Fix:** pass it via `env:` and use the variable: `TITLE: ${{ github.event.pull_request.title }}` then `echo "$TITLE"`.
- **Takeaway:** never trust attacker-controlled values inside `run:`.

---

### 🛠️ CI & flow control (04–05)

### 7. `pnpm: command not found` at setup-node

- **Lesson:** [04 — CI · Matrix · Cache](04-ci-matrix-cache.md)
- **Cause:** `actions/setup-node` with `cache: pnpm` placed before `pnpm/action-setup`.
- **Fix:** swap the order.
- **Takeaway:** the cache step needs the tool installed first.

### 8. `ERR_PNPM_OUTDATED_LOCKFILE`

- **Lesson:** [04 — CI · Matrix · Cache](04-ci-matrix-cache.md)
- **Cause:** edited `package.json` without running `pnpm install` locally and pushing `pnpm-lock.yaml`.
- **Fix:** run `pnpm install` locally and commit the lockfile.
- **Takeaway:** `--frozen-lockfile` protects you; don't bypass it.

### 9. `if` always runs

- **Lesson:** [05 — Conditionals & Outputs](05-conditionals-outputs.md)
- **Symptom:** step runs even though the condition looks false.
- **Cause:** `if: ${{ github.ref }} == 'refs/heads/main'` — the whole text is a non-empty string, so it's `true`.
- **Fix:** `if: github.ref == 'refs/heads/main'`, or put the entire expression inside `${{ }}`.
- **Takeaway:** write the condition as one complete expression.

### 10. Job output arrives empty

- **Lesson:** [05 — Conditionals & Outputs](05-conditionals-outputs.md)
- **Cause:** wrote the step output but didn't declare `outputs:` on the job, or forgot `needs`.
- **Fix:** declare `outputs:` in the producing job; read `needs.<job>.outputs.<name>` in the consumer.
- **Takeaway:** step outputs cross jobs only via `outputs` + `needs`.

---

### 🔐 Security (06)

### 11. Secret is empty or not working

- **Lesson:** [06 — Secrets · Vars · Permissions](06-secrets-vars-permissions.md) / [09 — Composite Actions](09-composite-actions.md)
- **Cause:** typo in the secret name, secret used directly inside a composite action, or the run came from a fork PR (secrets aren't passed).
- **Fix:** match the name exactly; pass the secret through `inputs` to the composite action.
- **Takeaway:** check the name first; composite actions don't see `secrets` automatically, and fork PRs get no secrets at all.

### 12. `Resource not accessible by integration`

- **Lesson:** [06 — Secrets · Vars · Permissions](06-secrets-vars-permissions.md)
- **Cause:** `GITHUB_TOKEN` lacks the needed permission (e.g. commenting on a PR).
- **Fix:** add only that permission under `permissions:` (e.g. `pull-requests: write`).
- **Takeaway:** least privilege means you add what you need explicitly.

---

### 📦 Data & run control (07–08)

### 13. Artifact name conflict

- **Lesson:** [07 — Artifacts](07-artifacts.md)
- **Symptom:** `Failed to CreateArtifact: Conflict`
- **Cause:** uploading several artifacts with the same name within one run (v4+), e.g. from matrix jobs.
- **Fix:** add a per-job value to the name, such as `matrix.node` or `matrix.os`. `github.sha` doesn't help here: it's identical for every job in the run, and artifact names don't clash across different runs anyway.
- **Takeaway:** in v4 the name must be unique within a single run.

### 14. `cancel-in-progress` cut a deploy in half

- **Lesson:** [08 — Concurrency & Timeouts](08-concurrency-timeouts.md)
- **Cause:** `cancel-in-progress: true` on the deploy workflow; a new push cancelled a running deploy.
- **Fix:** use it for CI only; set `false` for deploys so the running deploy finishes and the new run waits its turn. (Note: only one run stays pending; if a newer run arrives, the older pending one is cancelled.)
- **Takeaway:** cancelling is great for tests, dangerous for deploys.

---

### 🚀 Reuse & delivery (09–13)

### 15. `Can't find 'action.yml'` with a local action

- **Lesson:** [09 — Composite Actions](09-composite-actions.md)
- **Cause:** called `./.github/actions/setup-pnpm` before `checkout`.
- **Fix:** put `checkout` first.
- **Takeaway:** local actions are part of the code you must fetch.

### 16. `Required property is missing: shell`

- **Lesson:** [09 — Composite Actions](09-composite-actions.md)
- **Cause:** a `run` step inside a composite action without `shell`.
- **Fix:** add `shell: bash`.
- **Takeaway:** `shell` is mandatory in composite actions.

### 17. Preview fails on a PR from a fork

- **Lesson:** [11 — Previews](11-previews.md)
- **Symptom:** Firebase secret is empty, deploy step fails.
- **Cause:** fork PR runs get no secrets.
- **Fix:** skip the preview for forks with an `if`, e.g. `if: github.event.pull_request.head.repo.full_name == github.repository`. `pull_request_target` is risky because it exposes secrets to untrusted code; use it only with great care.
- **Takeaway:** security beats preview convenience.

### 18. Reusable workflow doesn't run or can't see secrets

- **Lesson:** [12 — Reusable Workflows](12-reusable-workflows.md)
- **Cause:** missing `on: workflow_call`, assuming secrets pass automatically, or writing `steps` in the caller job instead of `uses`.
- **Fix:** add `workflow_call`; pass `secrets:` explicitly or `secrets: inherit`; call with `uses:` at the job level.
- **Takeaway:** reusable workflows are called at the job level, not the step level.

### 19. Job waits for approval forever

- **Lesson:** [13 — Environments](13-environments.md)
- **Cause:** the assigned reviewer hasn't approved yet (or never noticed the request), or the only reviewer is the person who triggered the run and Prevent self-review is on, so they can't approve their own run.
- **Fix:** approve from the run page via Review deployments, add a second reviewer in the environment settings, or turn off self-review prevention if that suits your project.
- **Takeaway:** real protection needs a real, different person to approve.

---

### 🏆 Production (14–15)

### 20. `Unable to get ACTIONS_ID_TOKEN_REQUEST_URL`

- **Lesson:** [14 — OIDC](14-oidc.md)
- **Symptom:** the Google Cloud authentication step fails.
- **Cause:** `permissions: id-token: write` is missing from the job.
- **Fix:** add it, ideally with `contents: read`.
- **Takeaway:** default `GITHUB_TOKEN` permissions don't allow requesting an OIDC token.

---

## 🧑‍💻 My own problems

> What I actually hit: the error as it appeared, what I tried, and how I found the cause.

### 📝 Template

```md
### N. Short title

- **Date:** YYYY-MM-DD
- **Lesson:** [0X — Name](0X-name.md)
- **Symptom:** what appeared (paste the error)
- **Cause:** why it happened
- **What I tried first:** (optional)
- **Fix:** what I changed
- **Takeaway:** one sentence
```

---

### 1. `No pnpm version is specified` when running `pnpm/action-setup@v4`

- **Date:** 2026-11-08
- **Lesson:** [04 — CI · Matrix · Cache](04-ci-matrix-cache.md) / [09 — Composite Actions](09-composite-actions.md)
- **Symptom:** The `pnpm/action-setup@v4` step failed with:
  ```
  Error: No pnpm version is specified.
  Please specify it by one of the following ways:
    - in the GitHub Action config with the key "version"
    - in the package.json with the key "packageManager"
  ```
- **Cause:** The action didn't know which pnpm version to install. There was no `packageManager` field in `package.json` and no `version:` in the action config.
- **What I tried first:** Adding `with: version:` to the action. This works, but it can later conflict with `packageManager` if both are set differently.
- **Fix:** Added `"packageManager": "pnpm@12.16.1"` to `package.json` using `pnpm pkg set packageManager="pnpm@12.16.1"`.
- **Takeaway:** `pnpm/action-setup` needs one clear source of truth for the version. The best source is `packageManager` in `package.json`.

---

### 2. `ERR_PNPM_FROZEN_LOCKFILE_WITH_OUTDATED_LOCKFILE`

- **Date:** 2026-11-08
- **Lesson:** [04 — CI · Matrix · Cache](04-ci-matrix-cache.md)
- **Symptom:** `pnpm install --frozen-lockfile` failed with:
  ```
  ERR_PNPM_FROZEN_LOCKFILE_WITH_OUTDATED_LOCKFILE
  Cannot update packageManagerDependencies with "frozen-lockfile" because the lockfile is not up to date
  ```
- **Cause:** I added the `packageManager` field to `package.json` but didn't update `pnpm-lock.yaml` locally. `--frozen-lockfile` refuses to update anything automatically.
- **What I tried first:** Running `pnpm install` in CI without `--frozen-lockfile`. This is not the right fix; it hides the problem.
- **Fix:** Ran `pnpm install --no-frozen-lockfile` locally, then committed the updated `pnpm-lock.yaml` together with `package.json`.
- **Takeaway:** Any change to `package.json` (especially `packageManager`) requires updating the lockfile locally before pushing.

---

### 3. pnpm version mismatch between local and CI

- **Date:** 2026-11-08
- **Lesson:** [04 — CI · Matrix · Cache](04-ci-matrix-cache.md)
- **Symptom:** The local pnpm version (`12.16.1`) might not match the version installed in CI if it isn't specified clearly.
- **Cause:** No single source of truth for the pnpm version.
- **What I tried first:** Setting `version:` in the action, but that duplicates the source of truth.
- **Fix:** Pinned the version in `packageManager` only, and avoided `with: version:` in the action to reduce conflicts.
- **Takeaway:** Make `packageManager` the single source of truth for the pnpm version, and update the lockfile with it.

---
