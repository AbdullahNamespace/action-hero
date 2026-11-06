🌐 **English** · [العربية](README.ar.md)

# 🦸 Action Hero

![GitHub Actions](https://img.shields.io/badge/GitHub%20Actions-Learning-blue?logo=githubactions&logoColor=white)
![Status](https://img.shields.io/badge/status-learning%20in%20progress-yellow)
![License](https://img.shields.io/badge/license-MIT-green)

---

## 🎯 About This Project

**Action Hero** is my personal sandbox for mastering GitHub Actions.
Each stage lives twice: a runnable workflow in `.github/workflows/NN-*.yml`
and a lesson in `docs/NN-*.md` (Arabic: `docs/NN-*.ar.md`).

---

> [!NOTE]
> 🚧 **This project is a work in progress.** Some links in the tables below (lessons, workflow files, or actions) may not work yet or may show a 404, because those files haven't been written or pushed yet. They will start working as each stage is completed, and the [Progress](#-progress) checklist is ticked off stage by stage.

## 🧭 Learning Path

Go in order — every stage builds on the previous one.

| Level       | Stages | What you get                                         |
| ----------- | ------ | ---------------------------------------------------- |
| Foundations | 01–03  | Read and write a workflow, trigger it, use contexts  |
| Core CI     | 04–08  | Test, branch logic, secure, share data, control runs |
| Delivery    | 09–13  | Reuse code, deploy, preview, gate with approvals     |
| Production  | 14–15  | Keyless cloud auth (OIDC), releases, custom actions  |

---

## 🧪 The Curriculum

| #   | Stage                  | Workflow                                                          | Lesson                                                        | Key Concepts                               |
| --- | ---------------------- | ----------------------------------------------------------------- | ------------------------------------------------------------- | ------------------------------------------ |
| 01  | Anatomy                | [`01-hello.yml`](../.github/workflows/01-hello.yml)               | [01-anatomy](01-anatomy.md)                                   | `on` · `jobs` · `steps` · `run`            |
| 02  | Triggers               | [`02-triggers.yml`](../.github/workflows/02-triggers.yml)         | [02-triggers](02-triggers.md)                                 | `push` · `schedule` · `dispatch` · `paths` |
| 03  | Contexts & Expressions | [`03-contexts.yml`](../.github/workflows/03-contexts.yml)         | [03-contexts](03-contexts.md)                                 | `github.*` · `${{ }}` · functions          |
| 04  | CI · Matrix · Cache    | [`04-ci.yml`](../.github/workflows/04-ci.yml)                     | [04-ci-matrix-cache](04-ci-matrix-cache.md)                   | matrix · `needs` · cache                   |
| 05  | Conditionals & Outputs | [`05-conditionals.yml`](../.github/workflows/05-conditionals.yml) | [05-conditionals-outputs](05-conditionals-outputs.md)         | `if` · `always()` · `GITHUB_OUTPUT`        |
| 06  | Secrets · Vars · Perms | [`06-secrets.yml`](../.github/workflows/06-secrets.yml)           | [06-secrets-vars-permissions](06-secrets-vars-permissions.md) | `secrets` · `vars` · `permissions`         |
| 07  | Artifacts              | [`07-artifacts.yml`](../.github/workflows/07-artifacts.yml)       | [07-artifacts](07-artifacts.md)                               | upload/download · retention                |
| 08  | Concurrency & Timeouts | [`08-concurrency.yml`](../.github/workflows/08-concurrency.yml)   | [08-concurrency-timeouts](08-concurrency-timeouts.md)         | `concurrency` · `timeout-minutes`          |
| 09  | Composite Actions      | [`09-composite.yml`](../.github/workflows/09-composite.yml)       | [09-composite-actions](09-composite-actions.md)               | `runs.using: composite`                    |
| 10  | Deploy (live)          | [`10-deploy.yml`](../.github/workflows/10-deploy.yml)             | [10-deploy](10-deploy.md)                                     | Firebase Hosting · live deploy             |
| 11  | Previews               | [`11-preview.yml`](../.github/workflows/11-preview.yml)           | [11-previews](11-previews.md)                                 | channels · `expires`                       |
| 12  | Reusable Workflows     | [`12-reusable.yml`](../.github/workflows/12-reusable.yml)         | [12-reusable-workflows](12-reusable-workflows.md)             | `workflow_call` · `secrets: inherit`       |
| 13  | Environments           | [`13-environments.yml`](../.github/workflows/13-environments.yml) | [13-environments](13-environments.md)                         | required reviewers · env secrets           |
| 14  | OIDC                   | [`14-oidc.yml`](../.github/workflows/14-oidc.yml)                 | [14-oidc](14-oidc.md)                                         | `id-token: write` · WIF                    |
| 15  | Release & Docker       | [`15-release.yml`](../.github/workflows/15-release.yml)           | [15-release-custom-actions](15-release-custom-actions.md)     | tags · GHCR · Docker · custom actions      |

> 📖 New term? [`00-glossary.md`](00-glossary.md)
> 🤕 Stuck on an error? [mistakes log](mistakes-log.md)

---

## 🧩 Local Actions

Reusable building blocks under `.github/actions/`:

- [`setup-pnpm`](../.github/actions/setup-pnpm/action.yml) — composite: pnpm + Node + frozen-lockfile install in one line
- [`deploy-firebase-hosting`](../.github/actions/deploy-firebase-hosting/action.yml) — composite: write SA credentials & deploy to Firebase Hosting
- [`hello-custom`](../.github/actions/hello-custom/action.yml) — custom: a JavaScript/Docker action (stage 15)

> 💡 **Composite action vs reusable workflow?**
> A composite action bundles _steps_ (used inside a job). A reusable workflow bundles _whole jobs_ (called with `uses:` at the job level). See [09](09-composite-actions.md) and [12](12-reusable-workflows.md).

---

## 🗂️ Project Structure

```
action-hero/
├── .github/
│   ├── actions/                     # composite & custom actions
│   │   ├── setup-pnpm/
│   │   ├── deploy-firebase-hosting/
│   │   └── hello-custom/            # stage 15 (JS action)
│   ├── workflows/                   # one numbered workflow per lesson (01 → 15)
│   │   ├── 01-hello.yml
│   │   ├── 02-triggers.yml
│   │   ├── 03-contexts.yml
│   │   ├── 04-ci.yml
│   │   ├── 05-conditionals.yml
│   │   ├── 06-secrets.yml
│   │   ├── 07-artifacts.yml
│   │   ├── 08-concurrency.yml
│   │   ├── 09-composite.yml
│   │   ├── 10-deploy.yml
│   │   ├── 11-preview.yml
│   │   ├── 12-reusable.yml
│   │   ├── 12-reusable-called.yml
│   │   ├── 13-environments.yml
│   │   ├── 14-oidc.yml
│   │   └── 15-release.yml
│   └── dependabot.yml               # keeps action versions up to date
├── docs/                            # lessons (NN-*.md + NN-*.ar.md), glossary, mistakes log
│   ├── 00-glossary.md / .ar.md
│   ├── 01-anatomy.md / .ar.md
│   ├── 02-triggers.md / .ar.md
│   ├── 03-contexts.md / .ar.md
│   ├── 04-ci-matrix-cache.md / .ar.md
│   ├── 05-conditionals-outputs.md / .ar.md
│   ├── 06-secrets-vars-permissions.md / .ar.md
│   ├── 07-artifacts.md / .ar.md
│   ├── 08-concurrency-timeouts.md / .ar.md
│   ├── 09-composite-actions.md / .ar.md
│   ├── 10-deploy.md / .ar.md
│   ├── 11-previews.md / .ar.md
│   ├── 12-reusable-workflows.md / .ar.md
│   ├── 13-environments.md / .ar.md
│   ├── 14-oidc.md / .ar.md
│   ├── 15-release-custom-actions.md / .ar.md
│   ├── README.md / README.ar.md     # ← you are here
│   └── mistakes-log.md / .ar.md
├── src/                             # NestJS app — the "real project" CI runs against
├── test/                            # e2e specs
├── vitest.config.ts
└── vitest.config.e2e.ts
```

---

## 📚 Progress

**🌱 Foundations**

- [x] **01** — Anatomy: `on`, `jobs`, `steps`
- [x] **02** — Triggers: push, PR, schedule, manual dispatch, `paths`
- [ ] **03** — Contexts & expressions

**🛠️ Core CI**

- [ ] **04** — CI: matrix (`include`), `needs`, caching
- [ ] **05** — Conditionals & job outputs
- [ ] **06** — Secrets, variables & least-privilege permissions
- [ ] **07** — Artifacts between jobs
- [ ] **08** — Concurrency & timeouts

**🚀 Delivery**

- [ ] **09** — Composite actions
- [ ] **10** — Deploying to Firebase Hosting
- [ ] **11** — PR preview channels
- [ ] **12** — Reusable workflows
- [ ] **13** — Environments with manual approvals

**🏆 Production**

- [ ] **14** — OIDC: keyless authentication to Google Cloud
- [ ] **15** — Release & Docker

---

## 🤝 Contributing

This is a personal learning repo, but suggestions are welcome — feel free to open an issue.

---

## 📄 License

MIT © [Abdullah Abdulkhaleq](https://github.com/AbdullahNamespace)
