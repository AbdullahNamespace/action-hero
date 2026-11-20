🌐 [English](11-previews.md) · **العربية**

# 11 — المعاينات (Previews)

> الملف: [`11-preview.yml`](../.github/workflows/11-preview.yml)

**الهدف:** كل PR يحصل على رابط مؤقت خاص به، يُنشر كتعليق على الـ PR.

## التدفق

```
فتح/تحديث PR → build → نشر على قناة pr-<رقم> → تعليق بالرابط
                                  │
                          تنتهي بعد 7 أيام
```

## المفهوم العام: Preview Environments

فكرة "نسخة مؤقتة برابط مستقل لكل PR" ليست خاصة بـ Firebase. تُسمى عمومًا **Preview Environments** (أو Ephemeral Environments)، وكل منصة تسميها باسم مختلف:

| المنصة                      | الاسم عندها                      | ملاحظة                                                  |
| --------------------------- | -------------------------------- | ------------------------------------------------------- |
| Firebase Hosting            | Preview Channels                 | نشرها بأنفسنا في هذا الدرس عبر `hosting:channel:deploy` |
| Vercel                      | Preview Deployments              | تلقائية بمجرد ربط المستودع                              |
| Netlify                     | Deploy Previews                  | تلقائية، وتدعم أيضًا Branch Deploys                     |
| Cloudflare Pages            | Preview Deployments              | تلقائية لكل فرع وPR                                     |
| AWS Amplify                 | Pull Request Previews            | تُفعَّل من إعدادات التطبيق                              |
| Azure Static Web Apps       | Staging Environments             | تُنشأ لكل PR تلقائيًا عبر الـ workflow                  |
| Kubernetes (ArgoCD, وغيرها) | Ephemeral / Preview Environments | بيئة كاملة (تطبيق + قاعدة بيانات) لكل PR                |

> الاسم يختلف، لكن الفكرة واحدة: **رابط مستقل + عمر محدود + لا يمس الموقع الحي.**
> بعض المنصات (Vercel، Netlify) تفعل هذا دون أي workflow، وبعضها (Firebase) يحتاج أن نبنيه بأنفسنا كما في هذا الدرس.

## الأجزاء الأساسية

**القناة (Channel)** — هذا هو مصطلح Firebase للمعاينة: نسخة من الموقع برابط مستقل:

```yaml
- uses: ./.github/actions/deploy-firebase-hosting
  with:
    channel-id: pr-${{ github.event.pull_request.number }}
    expires: 7d
```

إعادة النشر على نفس القناة تحدّثها. القنوات المنتهية تختفي؛ ولحذف قناة مبكرًا: `firebase hosting:channel:delete pr-12 --project <id>`.

**التعليق** — يحتاج `pull-requests: write`، ويحتاج توكنًا يمرَّر إلى `gh` عبر `GH_TOKEN`. الخيار `--edit-last` يحدّث **آخر تعليق كتبه نفس المستخدم** (هنا البوت) بدل إضافة تعليق جديد مع كل push:

```bash
gh pr comment "$PR" --edit-last --body "$BODY" || gh pr comment "$PR" --body "$BODY"
```

**Concurrency** — مجموعة لكل PR؛ والـ push الجديد يلغي المعاينة الأقدم (`cancel-in-progress: true`)، وهذا آمن لأنها ليست الموقع الحي. الفرق بين الإلغاء والانتظار ولماذا لا نلغي النشر الحي: [08 — Concurrency والمهلات](08-concurrency-timeouts.ar.md).

## الـ Forks والأسرار

| المشغّل               | أسرار لـ PR من fork | الحكم                                      |
| --------------------- | ------------------- | ------------------------------------------ |
| `pull_request`        | ❌ لا               | آمن — **استخدمه** وتخطَّ الـ forks بـ `if` |
| `pull_request_target` | ✅ نعم              | خطِر مع كود غير موثوق — تجنّبه             |

```yaml
if: github.event.pull_request.head.repo.full_name == github.repository
```

## فخاخ شائعة

- PR من fork ← السر فارغ ← فشل النشر ([الخطأ 17](mistakes-log.ar.md)).
- غياب `pull-requests: write` ← `Resource not accessible by integration`.
- نسيان `GH_TOKEN` ← الأمر `gh` يفشل لأنه لا يجد توكنًا.
- تراكم التعليقات: استخدم `--edit-last`.

## تمرين

افتح PR وابحث عن رابط المعاينة في التعليق؛ ثم ادفع مرة أخرى وراقب تحديث نفس التعليق.

⬅️ [10 — النشر](10-deploy.ar.md) · ➡️ [12 — Reusable Workflows](12-reusable-workflows.ar.md)
