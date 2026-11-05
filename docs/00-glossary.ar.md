🌐 [English](00-glossary.md) · **العربية**

# 📖 00 — القاموس

مصطلحات GitHub Actions الأساسية، مرتّبة حسب ترتيب الدروس.
عمود **الدرس** يوضح أين يُشرح المصطلح بالتفصيل.

> 🤕 تعثرت في مصطلح؟ راجع [سجل الأخطاء](mistakes-log.ar.md).

**الفهرس**

| القسم                                                           |
| --------------------------------------------------------------- |
| [🌱 الأساسيات](#-الأساسيات)                                     |
| [🛠️ CI والتحكم في التدفق](#️-ci-والتحكم-في-التدفق)               |
| [🔐 الأمان](#-الأمان)                                           |
| [📦 البيانات والتحكم في التشغيل](#-البيانات-والتحكم-في-التشغيل) |
| [🚀 إعادة الاستخدام والنشر](#-إعادة-الاستخدام-والنشر)           |
| [🏆 الإنتاج والمتقدم](#-الإنتاج-والمتقدم)                       |

---

## 🌱 الأساسيات

| المصطلح                  | المعنى                                                                                                         | مثال                                                                     | الدرس                                 |
| ------------------------ | -------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------ | ------------------------------------- |
| **Workflow**             | ملف YAML يصف عملية آلية كاملة. مكانه `.github/workflows/`                                                      | `04-ci.yml`                                                              | [01](01-anatomy.ar.md)                |
| **YAML**                 | صيغة نصية تُكتب بها ملفات الـ workflows، وتعتمد على المسافات البادئة (مسافتان، لا Tab)                         | `name: CI`                                                               | [01](01-anatomy.ar.md)                |
| **Workflow run**         | تشغيل واحد فعلي لـ workflow (له رقم وسجل ونتيجة)                                                               | Run #42                                                                  | [01](01-anatomy.ar.md)                |
| **Event / Trigger**      | الحدث الذي يشغّل الـ workflow                                                                                  | `push`, `pull_request`                                                   | [02](02-triggers.ar.md)               |
| **Job**                  | مجموعة خطوات تعمل على جهاز واحد. الـ jobs تعمل بالتوازي افتراضيًا                                              | `lint`, `build`                                                          | [01](01-anatomy.ar.md)                |
| **Step**                 | خطوة واحدة داخل الـ job: إمّا `run` أو `uses`                                                                  | `run: pnpm test`                                                         | [01](01-anatomy.ar.md)                |
| **Runner**               | الجهاز (VM) الذي ينفّذ الـ job                                                                                 | `ubuntu-latest`                                                          | [01](01-anatomy.ar.md)                |
| **`runs-on`**            | حقل يحدد نوع الـ runner الذي سينفّذ الـ job                                                                    | `runs-on: ubuntu-latest`                                                 | [01](01-anatomy.ar.md)                |
| **GitHub-hosted runner** | runner تديره GitHub وتُنشئه نظيفًا لكل job ثم تحذفه                                                            | `ubuntu-latest`                                                          | [01](01-anatomy.ar.md)                |
| **Self-hosted runner**   | runner على جهازك أنت. تتحمل صيانته وأمانه                                                                      | `runs-on: self-hosted`                                                   | [15](15-release-custom-actions.ar.md) |
| **Action**               | وحدة جاهزة قابلة لإعادة الاستخدام تُستدعى بـ `uses`                                                            | `actions/checkout@v7`                                                    | [01](01-anatomy.ar.md)                |
| **`workflow_dispatch`**  | مشغّل يدوي (زر Run workflow) ويقبل `inputs`                                                                    | —                                                                        | [02](02-triggers.ar.md)               |
| **Default branch**       | الفرع الأساسي للمستودع (غالبًا `main`)؛ بعض المشغّلات مثل `workflow_dispatch` و`schedule` تقرأ إعدادها منه فقط | `main`                                                                   | [02](02-triggers.ar.md)               |
| **Filters**              | شروط تحدد متى يعمل المشغّل: `branches` / `tags` / `paths`                                                      | `paths: ['src/**']`                                                      | [02](02-triggers.ar.md)               |
| **cron**                 | صيغة جدولة زمنية من 5 حقول (بتوقيت UTC)                                                                        | `0 6 * * 1`                                                              | [02](02-triggers.ar.md)               |
| **Context**              | كائنات بيانات متاحة داخل `${{ }}`                                                                              | `github`, `env`, `matrix`, `needs`, `steps`, `runner`, `vars`, `secrets` | [03](03-contexts.ar.md)               |
| **Expression**           | تعبير يُقيَّم داخل `${{ ... }}`                                                                                | `${{ github.sha }}`                                                      | [03](03-contexts.ar.md)               |
| **Function**             | دوال جاهزة داخل التعبيرات                                                                                      | `contains()`, `startsWith()`, `format()`, `toJSON()`, `hashFiles()`      | [03](03-contexts.ar.md)               |
| **Script injection**     | ثغرة تحدث عند وضع قيمة يتحكم بها غيرك (مثل عنوان PR) مباشرة داخل `run:`                                        | الحل: مرّرها عبر `env:`                                                  | [03](03-contexts.ar.md)               |

---

## 🛠️ CI والتحكم في التدفق

| المصطلح                   | المعنى                                                              | مثال                                                | الدرس                               |
| ------------------------- | ------------------------------------------------------------------- | --------------------------------------------------- | ----------------------------------- |
| **Matrix**                | تشغيل نفس الـ job بعدة تركيبات (إصدارات Node مثلًا)                 | `node: [22, 24]`                                    | [04](04-ci-matrix-cache.ar.md)      |
| **`fail-fast`**           | إلغاء بقية تركيبات الـ matrix عند فشل واحدة منها                    | `fail-fast: false`                                  | [04](04-ci-matrix-cache.ar.md)      |
| **`include` / `exclude`** | إضافة تركيبة خاصة أو استبعاد تركيبة من الـ matrix                   | —                                                   | [04](04-ci-matrix-cache.ar.md)      |
| **`needs`**               | يجعل job ينتظر نجاح jobs أخرى                                       | `needs: [lint, test]`                               | [04](04-ci-matrix-cache.ar.md)      |
| **Cache**                 | تخزين مؤقت للاعتماديات لتسريع التشغيلات                             | pnpm store                                          | [04](04-ci-matrix-cache.ar.md)      |
| **Cache key**             | المفتاح الذي يحدد هل يُستعاد الكاش أم يُبنى من جديد                 | `hashFiles('pnpm-lock.yaml')`                       | [04](04-ci-matrix-cache.ar.md)      |
| **Frozen lockfile**       | تثبيت يرفض تعديل ملف القفل                                          | `pnpm install --frozen-lockfile`                    | [04](04-ci-matrix-cache.ar.md)      |
| **`if`**                  | شرط يحدد هل يعمل الـ step أو الـ job                                | `if: github.ref == 'refs/heads/main'`               | [05](05-conditionals-outputs.ar.md) |
| **Status functions**      | دوال تفحص حالة ما سبق                                               | `success()`, `failure()`, `always()`, `cancelled()` | [05](05-conditionals-outputs.ar.md) |
| **`continue-on-error`**   | يسمح باستمرار الـ workflow حتى لو فشلت هذه الخطوة                   | —                                                   | [05](05-conditionals-outputs.ar.md) |
| **`GITHUB_OUTPUT`**       | ملف تكتب فيه قيمة لتصبح output للخطوة                               | `echo "v=1.2" >> "$GITHUB_OUTPUT"`                  | [05](05-conditionals-outputs.ar.md) |
| **`GITHUB_ENV`**          | ملف تكتب فيه متغير بيئة ليصبح متاحًا للخطوات اللاحقة في نفس الـ job | `echo "NODE_ENV=test" >> "$GITHUB_ENV"`             | [05](05-conditionals-outputs.ar.md) |
| **Step output**           | قيمة تنتجها خطوة وتقرؤها خطوة لاحقة في نفس الـ job                  | `steps.ver.outputs.v`                               | [05](05-conditionals-outputs.ar.md) |
| **Job output**            | قيمة يعلنها job (`outputs:`) ليقرأها job آخر عبر `needs`            | `needs.build.outputs.version`                       | [05](05-conditionals-outputs.ar.md) |
| **`GITHUB_STEP_SUMMARY`** | ملف Markdown يظهر كملخص منسَّق في صفحة التشغيل                      | —                                                   | [05](05-conditionals-outputs.ar.md) |
| **Workflow command**      | سطر خاص يطبعه الـ step ليتفاعل معه GitHub                           | `::error::رسالة`                                    | [05](05-conditionals-outputs.ar.md) |

---

## 🔐 الأمان

| المصطلح                         | المعنى                                                                                                                                              | مثال                               | الدرس                                   |
| ------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------- | --------------------------------------- |
| **Secret**                      | قيمة مشفّرة حساسة تُخزَّن في الإعدادات وتُخفى من السجلات                                                                                            | `secrets.FIREBASE_SERVICE_ACCOUNT` | [06](06-secrets-vars-permissions.ar.md) |
| **Variable**                    | قيمة غير حساسة تُخزَّن في الإعدادات (تظهر في السجلات)                                                                                               | `vars.FIREBASE_PROJECT_ID`         | [06](06-secrets-vars-permissions.ar.md) |
| **`env`**                       | متغيرات بيئة تُعرَّف داخل الملف نفسه (workflow أو job أو step)                                                                                      | `env: NODE_ENV: test`              | [06](06-secrets-vars-permissions.ar.md) |
| **Masking**                     | استبدال قيمة السر في السجلات بـ `***`                                                                                                               | —                                  | [06](06-secrets-vars-permissions.ar.md) |
| **`GITHUB_TOKEN`**              | توكن مؤقت يُنشأ تلقائيًا لكل job وينتهي بانتهاء الـ job                                                                                             | `secrets.GITHUB_TOKEN`             | [06](06-secrets-vars-permissions.ar.md) |
| **Personal Access Token (PAT)** | توكن شخصي بصلاحيات أوسع من `GITHUB_TOKEN`؛ يلزم مثلًا للوصول إلى مستودع آخر                                                                         | `secrets.MY_PAT`                   | [06](06-secrets-vars-permissions.ar.md) |
| **SSO / SAML SSO**              | تسجيل دخول موحّد تفرضه المنظمة عبر مزوّد هوية (Okta مثلًا). عند تفعيله يجب تفويض (Authorize) الـ PAT للمنظمة قبل استخدامه، ولا يتأثر `GITHUB_TOKEN` | —                                  | [06](06-secrets-vars-permissions.ar.md) |
| **Permissions**                 | صلاحيات `GITHUB_TOKEN` الممنوحة للـ workflow أو الـ job                                                                                             | `contents: read`                   | [06](06-secrets-vars-permissions.ar.md) |
| **Least privilege**             | مبدأ: امنح أقل صلاحيات كافية فقط                                                                                                                    | `permissions: {}` ثم أضف المطلوب   | [06](06-secrets-vars-permissions.ar.md) |
| **Fork**                        | نسخة من المستودع عند شخص آخر. تشغيلات الـ PR القادمة منها **لا تحصل على الأسرار** وصلاحياتها للقراءة فقط                                            | —                                  | [11](11-previews.ar.md)                 |

---

## 📦 البيانات والتحكم في التشغيل

| المصطلح               | المعنى                                                                  | مثال                       | الدرس                               |
| --------------------- | ----------------------------------------------------------------------- | -------------------------- | ----------------------------------- |
| **Artifact**          | ملفات ناتجة يتم حفظها لتُنزَّل أو تُمرَّر لـ job آخر                    | `dist/`                    | [07](07-artifacts.ar.md)            |
| **Retention**         | عدد الأيام التي يبقى فيها الـ artifact قبل حذفه تلقائيًا                | `retention-days: 7`        | [07](07-artifacts.ar.md)            |
| **Artifact vs Cache** | الـ artifact **ناتج** تريد تسليمه، والـ cache **اعتمادية** تريد تسريعها | —                          | [07](07-artifacts.ar.md)            |
| **Concurrency**       | منع/إلغاء التشغيلات المتزامنة المتشابهة                                 | `cancel-in-progress`       | [08](08-concurrency-timeouts.ar.md) |
| **Concurrency group** | الاسم الذي تُعتبر التشغيلات المتشاركة فيه "متشابهة"                     | `group: ${{ github.ref }}` | [08](08-concurrency-timeouts.ar.md) |
| **`timeout-minutes`** | أقصى مدة للـ job أو الـ step قبل إيقافه                                 | `timeout-minutes: 10`      | [08](08-concurrency-timeouts.ar.md) |

---

## 🚀 إعادة الاستخدام والنشر

| المصطلح                   | المعنى                                                                      | مثال                                               | الدرس                             |
| ------------------------- | --------------------------------------------------------------------------- | -------------------------------------------------- | --------------------------------- |
| **Composite Action**      | action تكتبه أنت من عدة **خطوات** (تُستدعى داخل job)                        | `.github/actions/setup-pnpm`                       | [09](09-composite-actions.ar.md)  |
| **Action inputs/outputs** | مدخلات ومخرجات الـ action (`inputs:` / `outputs:` في `action.yml`)          | `${{ inputs.node-version }}`                       | [09](09-composite-actions.ar.md)  |
| **`shell`**               | إلزامي لكل خطوة `run` داخل composite action                                 | `shell: bash`                                      | [09](09-composite-actions.ar.md)  |
| **Deploy**                | نشر نسخة التطبيق على بيئة تشغيل حقيقية                                      | —                                                  | [10](10-deploy.ar.md)             |
| **Firebase Hosting**      | خدمة استضافة من Google تُنشر عليها الواجهات                                 | —                                                  | [10](10-deploy.ar.md)             |
| **Service Account**       | حساب خدمة في Google Cloud يُستخدم للتوثيق الآلي (مفتاح JSON طويل الأمد)     | `FIREBASE_SERVICE_ACCOUNT`                         | [10](10-deploy.ar.md)             |
| **Preview channel**       | نسخة نشر مؤقتة برابط مستقل لكل PR                                           | `expires: 7d`                                      | [11](11-previews.ar.md)           |
| **`pull_request_target`** | مشغّل يعمل بسياق المستودع الأساسي (له أسرار) — خطِر مع كود الـ forks        | —                                                  | [11](11-previews.ar.md)           |
| **Reusable Workflow**     | workflow كامل يُستدعى من workflow آخر على مستوى الـ **job**                 | `uses: ./.github/workflows/12-reusable-called.yml` | [12](12-reusable-workflows.ar.md) |
| **`workflow_call`**       | المشغّل الذي يجعل الـ workflow قابلًا للاستدعاء                             | `on: workflow_call`                                | [12](12-reusable-workflows.ar.md) |
| **Caller / Called**       | الـ workflow المستدعي / الـ workflow المستدعى                               | `12-reusable.yml` / `12-reusable-called.yml`       | [12](12-reusable-workflows.ar.md) |
| **`secrets: inherit`**    | تمرير كل أسرار المستدعي للـ workflow المستدعى                               | —                                                  | [12](12-reusable-workflows.ar.md) |
| **Environment**           | بيئة مسمّاة (production) يمكن حمايتها بموافقات                              | `environment: production`                          | [13](13-environments.ar.md)       |
| **Required reviewers**    | أشخاص يجب أن يوافقوا قبل أن يبدأ الـ job المرتبط ببيئة                      | —                                                  | [13](13-environments.ar.md)       |
| **Review deployments**    | زر في صفحة التشغيل للموافقة على job ينتظر مراجِعًا أو رفضه                  | —                                                  | [13](13-environments.ar.md)       |
| **Prevent self-review**   | خيار في الـ Environment يمنع من شغّل الـ workflow من الموافقة على تشغيله هو | —                                                  | [13](13-environments.ar.md)       |
| **Environment secret**    | سر مرتبط ببيئة واحدة فقط ولا يراه غيرها                                     | —                                                  | [13](13-environments.ar.md)       |

---

## 🏆 الإنتاج والمتقدم

| المصطلح                          | المعنى                                                                          | مثال                            | الدرس                                 |
| -------------------------------- | ------------------------------------------------------------------------------- | ------------------------------- | ------------------------------------- |
| **OIDC**                         | OpenID Connect: يثبت الـ workflow هويته للسحابة بتوكن قصير العمر بدل مفتاح ثابت | —                               | [14](14-oidc.ar.md)                   |
| **`id-token: write`**            | صلاحية تسمح للـ job بطلب توكن OIDC                                              | `permissions: id-token: write`  | [14](14-oidc.ar.md)                   |
| **Workload Identity Federation** | ربط هوية GitHub بحساب خدمة في Google Cloud دون مفاتيح                           | WIF                             | [14](14-oidc.ar.md)                   |
| **Tag**                          | علامة ثابتة على commit معيّن، تُستخدم للإصدارات                                 | `v1.0.0`                        | [15](15-release-custom-actions.ar.md) |
| **Release**                      | إصدار منشور على GitHub مرتبط بـ tag وملفات ووصف                                 | —                               | [15](15-release-custom-actions.ar.md) |
| **GHCR**                         | GitHub Container Registry: مستودع صور Docker من GitHub                          | `ghcr.io/<user>/action-hero`    | [15](15-release-custom-actions.ar.md) |
| **Marketplace**                  | متجر GitHub للـ actions الجاهزة التي ينشرها الآخرون                             | —                               | [15](15-release-custom-actions.ar.md) |
| **Third-party action**           | action من خارج `actions/*`؛ كود غير موثوق فيُفضَّل تثبيته بـ SHA                | `uses: owner/repo@<sha>`        | [15](15-release-custom-actions.ar.md) |
| **Custom Action**                | action بـ JavaScript أو Docker (أقوى وأعقد من composite)                        | `.github/actions/hello-custom`  | [15](15-release-custom-actions.ar.md) |
| **Dependabot**                   | أداة تفتح PR تلقائيًا لتحديث الاعتماديات والـ actions                           | `.github/dependabot.yml`        | [15](15-release-custom-actions.ar.md) |
| **Pinning by SHA**               | تثبيت الـ action بمعرّف commit كامل بدل tag قابل للتغيّر                        | `uses: actions/checkout@<sha>`  | [15](15-release-custom-actions.ar.md) |
| **Container job**                | job كامل يعمل داخل صورة Docker (`container:`)                                   | `container: { image: node:24 }` | [15](15-release-custom-actions.ar.md) |
| **`docker://` step**             | step واحد يعمل داخل صورة Docker جاهزة                                           | `uses: docker://alpine:3`       | [15](15-release-custom-actions.ar.md) |
