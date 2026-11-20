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

## الأجزاء الأساسية

**القناة (Channel)** — نسخة معاينة من الموقع برابط مستقل:

```yaml
- uses: ./.github/actions/deploy-firebase-hosting
  with:
    channel-id: pr-${{ github.event.pull_request.number }}
    expires: 7d
```

إعادة النشر على نفس القناة تحدّثها. القنوات المنتهية تختفي؛ ولحذف قناة مبكرًا: `firebase hosting:channel:delete pr-12 --project <id>`.

**التعليق** — يحتاج `pull-requests: write`. الخيار `--edit-last` يحدّث تعليقًا واحدًا بدل إضافة تعليق جديد مع كل push:

```bash
gh pr comment "$PR" --edit-last --body "$BODY" || gh pr comment "$PR" --body "$BODY"
```

**Concurrency** — مجموعة لكل PR؛ والـ push الجديد يلغي المعاينة الأقدم (آمن: ليست الموقع الحي).

## 🔒 الـ Forks والأسرار

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
- تراكم التعليقات: استخدم `--edit-last`.

## تمرين

افتح PR وابحث عن رابط المعاينة في التعليق؛ ثم ادفع مرة أخرى وراقب تحديث نفس التعليق.

⬅️ [10 — النشر](10-deploy.ar.md) · ➡️ [12 — Reusable Workflows](12-reusable-workflows.ar.md)
