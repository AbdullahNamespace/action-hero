🌐 [English](README.md) · **العربية**

# 🦸 Action Hero

![GitHub Actions](https://img.shields.io/badge/GitHub%20Actions-Learning-blue?logo=githubactions&logoColor=white)
![Status](https://img.shields.io/badge/status-learning%20in%20progress-yellow)
![License](https://img.shields.io/badge/license-MIT-green)

---

## 🎯 عن المشروع

**Action Hero** ساحة تجاربي الشخصية لإتقان GitHub Actions.
كل مرحلة لها ملفان: workflow قابل للتشغيل في `.github/workflows/NN-*.yml`
ودرس في `docs/NN-*.ar.md` (الإنجليزي: `docs/NN-*.md`).

---

> [!NOTE]
> 🚧 **المشروع قيد البناء.** بعض الروابط في الجداول أدناه (الدروس أو ملفات الـ workflow أو الـ actions) قد لا تعمل حاليًا أو تُظهر خطأ 404، لأن ملفاتها لم تُكتب أو تُرفع بعد. ستعمل كلها تدريجيًا مع اكتمال المراحل، وتُحدَّث خانات قسم [التقدّم](#-التقدّم) عند الانتهاء من كل مرحلة.

## 🧭 مسار التعلّم

سِر بالترتيب — كل مرحلة تبني على السابقة.

| المستوى    | المراحل | ماذا تكسب                                            |
| ---------- | ------- | ---------------------------------------------------- |
| الأساسيات  | 01–03   | قراءة workflow وكتابته وتشغيله واستخدام contexts     |
| CI الأساسي | 04–08   | اختبار، منطق شرطي، أمان، تمرير بيانات، تحكم بالتشغيل |
| التسليم    | 09–13   | إعادة استخدام، نشر، معاينة، موافقات                  |
| الإنتاج    | 14–15   | دخول سحابي بلا مفاتيح (OIDC)، إصدارات، actions مخصصة |

---

## 🧪 المنهج

| #   | المرحلة                         | الـ Workflow                                                      | الدرس                                                            | المفاهيم                                   |
| --- | ------------------------------- | ----------------------------------------------------------------- | ---------------------------------------------------------------- | ------------------------------------------ |
| 01  | التشريح                         | [`01-hello.yml`](../.github/workflows/01-hello.yml)               | [01-anatomy](01-anatomy.ar.md)                                   | `on` · `jobs` · `steps` · `run`            |
| 02  | المشغّلات                       | [`02-triggers.yml`](../.github/workflows/02-triggers.yml)         | [02-triggers](02-triggers.ar.md)                                 | `push` · `schedule` · `dispatch` · `paths` |
| 03  | Contexts والتعبيرات             | [`03-contexts.yml`](../.github/workflows/03-contexts.yml)         | [03-contexts](03-contexts.ar.md)                                 | `github.*` · `${{ }}` · الدوال             |
| 04  | CI · Matrix · Cache             | [`04-ci.yml`](../.github/workflows/04-ci.yml)                     | [04-ci-matrix-cache](04-ci-matrix-cache.ar.md)                   | matrix · `needs` · cache                   |
| 05  | الشروط والمخرجات                | [`05-conditionals.yml`](../.github/workflows/05-conditionals.yml) | [05-conditionals-outputs](05-conditionals-outputs.ar.md)         | `if` · `always()` · `GITHUB_OUTPUT`        |
| 06  | الأسرار · المتغيرات · الصلاحيات | [`06-secrets.yml`](../.github/workflows/06-secrets.yml)           | [06-secrets-vars-permissions](06-secrets-vars-permissions.ar.md) | `secrets` · `vars` · `permissions`         |
| 07  | Artifacts                       | [`07-artifacts.yml`](../.github/workflows/07-artifacts.yml)       | [07-artifacts](07-artifacts.ar.md)                               | رفع/تنزيل · retention                      |
| 08  | Concurrency والمهلات            | [`08-concurrency.yml`](../.github/workflows/08-concurrency.yml)   | [08-concurrency-timeouts](08-concurrency-timeouts.ar.md)         | `concurrency` · `timeout-minutes`          |
| 09  | Composite Actions               | [`09-composite.yml`](../.github/workflows/09-composite.yml)       | [09-composite-actions](09-composite-actions.ar.md)               | `runs.using: composite`                    |
| 10  | النشر (المباشر)                 | [`10-deploy.yml`](../.github/workflows/10-deploy.yml)             | [10-deploy](10-deploy.ar.md)                                     | Firebase Hosting · نشر مباشر               |
| 11  | المعاينات                       | [`11-preview.yml`](../.github/workflows/11-preview.yml)           | [11-previews](11-previews.ar.md)                                 | channels · `expires`                       |
| 12  | Reusable Workflows              | [`12-reusable.yml`](../.github/workflows/12-reusable.yml)         | [12-reusable-workflows](12-reusable-workflows.ar.md)             | `workflow_call` · `secrets: inherit`       |
| 13  | Environments                    | [`13-environments.yml`](../.github/workflows/13-environments.yml) | [13-environments](13-environments.ar.md)                         | required reviewers · env secrets           |
| 14  | OIDC                            | [`14-oidc.yml`](../.github/workflows/14-oidc.yml)                 | [14-oidc](14-oidc.ar.md)                                         | `id-token: write` · WIF                    |
| 15  | Release و Docker                | [`15-release.yml`](../.github/workflows/15-release.yml)           | [15-release-custom-actions](15-release-custom-actions.ar.md)     | tags · GHCR · Docker · actions مخصصة       |

> 📖 مصطلح جديد؟ [`00-glossary.ar.md`](00-glossary.ar.md)
> 🤕 علقت في خطأ؟ [سجل الأخطاء](mistakes-log.ar.md)

---

## 🧩 الـ Actions المحلية

وحدات قابلة لإعادة الاستخدام في `.github/actions/`:

- [`setup-pnpm`](../.github/actions/setup-pnpm/action.yml) — composite: pnpm + Node + تثبيت frozen-lockfile بسطر واحد
- [`deploy-firebase-hosting`](../.github/actions/deploy-firebase-hosting/action.yml) — composite: كتابة بيانات الاعتماد والنشر على Firebase Hosting
- [`hello-custom`](../.github/actions/hello-custom/action.yml) — مخصص (custom): action بـ JavaScript/Docker (المرحلة 15)

> 💡 **Composite action أم reusable workflow؟**
> الـ composite action يجمع **خطوات** (يُستخدم داخل job). والـ reusable workflow يجمع **jobs كاملة** (يُستدعى بـ `uses:` على مستوى الـ job). انظر [09](09-composite-actions.ar.md) و[12](12-reusable-workflows.ar.md).

---

## 🗂️ هيكل المشروع

```
action-hero/
├── .github/
│   ├── actions/                     # composite و actions مخصصة
│   │   ├── setup-pnpm/
│   │   ├── deploy-firebase-hosting/
│   │   └── hello-custom/            # المرحلة 15 (JS action)
│   ├── workflows/                   # workflow مرقّم لكل درس (01 → 15)
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
│   └── dependabot.yml               # يحدّث إصدارات الـ actions
├── docs/                            # الدروس (NN-*.md + NN-*.ar.md) والقاموس وسجل الأخطاء
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
│   ├── README.md / README.ar.md     # ← أنت هنا
│   └── mistakes-log.md / .ar.md
├── src/                             # تطبيق NestJS — "المشروع الحقيقي" الذي يختبره CI
├── test/                            # اختبارات e2e
├── vitest.config.ts
└── vitest.config.e2e.ts
```

---

## 📚 التقدّم

**🌱 الأساسيات**

- [x] **01** — التشريح: `on`، `jobs`، `steps`
- [x] **02** — المشغّلات: push، PR، schedule، التشغيل اليدوي، `paths`
- [ ] **03** — Contexts والتعبيرات

**🛠️ CI الأساسي**

- [ ] **04** — CI: matrix (`include`)، `needs`، الكاش
- [ ] **05** — الشروط ومخرجات الـ jobs
- [ ] **06** — الأسرار والمتغيرات وأقل الصلاحيات
- [ ] **07** — Artifacts بين الـ jobs
- [ ] **08** — Concurrency والمهلات

**🚀 التسليم**

- [ ] **09** — Composite actions
- [ ] **10** — النشر على Firebase Hosting
- [ ] **11** — قنوات معاينة الـ PR
- [ ] **12** — Reusable workflows
- [ ] **13** — Environments بموافقات يدوية

**🏆 الإنتاج**

- [ ] **14** — OIDC: دخول Google Cloud بلا مفاتيح
- [ ] **15** — Release و Docker

---

## 🤝 المساهمة

مستودع تعلّم شخصي، لكن الاقتراحات مرحّب بها — افتح issue متى شئت.

---

## 📄 الترخيص

MIT © [Abdullah Abdulkhaleq](https://github.com/AbdullahNamespace)
