🌐 [English](07-artifacts.md) · **العربية**

# 07 — الـ Artifacts

> الملف: [`07-artifacts.yml`](../.github/workflows/07-artifacts.yml)

**الهدف:** نقل الملفات الناتجة بين الـ jobs، أو حفظها للتنزيل.

**المشكلة:** كل job على جهاز منفصل؛ ما يبنيه `build` غير موجود في `verify`. الحل: رفعه كـ artifact ثم تنزيله.

```
 build job                     verify job
 pnpm build                    download-artifact
 upload-artifact ──► تخزين ──► فحص الملفات
```

## الأوامر

```yaml
# الرفع
- uses: actions/upload-artifact@v7
  with:
    name: dist-${{ github.sha }}
    path: dist/
    retention-days: 3
    if-no-files-found: error

# التنزيل
- uses: actions/download-artifact@v8
  with:
    name: dist-${{ github.sha }}
    path: downloaded-dist
```

## ملاحظات

- اسم الـ artifact **فريد** داخل التشغيل الواحد — أضف `github.sha` أو `matrix.node`.
- `retention-days` يحدد مدة الحفظ (الافتراضي 90 يومًا، ويستهلك مساحة التخزين).
- `if-no-files-found: error` يمنع النجاح الصامت.
- يمكن تنزيل الـ artifact يدويًا من صفحة التشغيل.
- لا ترفع ملفات فيها أسرار (`.env`).

## Artifact مقابل Cache

|            | Artifact                  | Cache                        |
| ---------- | ------------------------- | ---------------------------- |
| الغرض      | **ناتج** تريد تمريره/حفظه | **اعتماديات** لتسريع التشغيل |
| تنزيل يدوي | نعم                       | لا                           |
| مثال       | `dist/`                   | pnpm store                   |

## فخاخ شائعة

- نسيان `needs` فيبدأ `verify` قبل انتهاء `build`.
- رفع مجلد ضخم (`node_modules`) بلا داعٍ.
- نفس الاسم من jobs متوازية ← خطأ تعارض.

## تمرين

شغّله ثم نزّل artifact المسمى `dist-...` من صفحة التشغيل وافتحه محليًا.

⬅️ [06 — الأسرار · المتغيرات · الصلاحيات](06-secrets-vars-permissions.ar.md) · ➡️ [08 — Concurrency و Timeouts](08-concurrency-timeouts.ar.md)
