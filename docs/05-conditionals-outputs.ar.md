🌐 [English](05-conditionals-outputs.md) · **العربية**

# 05 — الشروط والمخرجات

> الملف: [`05-conditionals.yml`](../.github/workflows/05-conditionals.yml)

**الهدف:** التحكم فيما يعمل من خطوات/jobs، وتمرير القيم بين الخطوات والـ jobs.

```
 version ──► report          conditions (7 خطوات: if / failure() / always())
 (output)    (يقرأ الـ output)
```

## `if`

```yaml
- if: github.ref == 'refs/heads/main'
  run: echo "on main"
```

اكتبه **تعبيرًا واحدًا عاديًا** (لا حاجة لـ `${{ }}`). كل step/job فيه ضمنيًا `success() &&`.

| الدالة        | تكون true عندما                              |
| ------------- | -------------------------------------------- |
| `success()`   | نجحت كل الخطوات/الـ jobs السابقة (الافتراضي) |
| `failure()`   | فشلت أي خطوة/job سابق                        |
| `always()`    | دائمًا — حتى عند الفشل أو الإلغاء            |
| `cancelled()` | أُلغي التشغيل                                |

`always()` للتنظيف، و`failure()` للتنبيهات.

## `continue-on-error`

```yaml
- continue-on-error: true
  run: exit 1 # الـ job يكمل ويُحتسب نجاحًا
```

على matrix job يمنع فشل تركيبة تجريبية من إفشال التشغيل ([04](04-ci-matrix-cache.ar.md)).

## مخرجات الـ Step

```yaml
- id: pkg
  run: echo "version=1.2.3" >> "$GITHUB_OUTPUT"
- run: echo "${{ steps.pkg.outputs.version }}"
```

القيم متعددة الأسطر تحتاج فاصلًا:

```bash
{ echo "notes<<EOF"; cat notes.txt; echo "EOF"; } >> "$GITHUB_OUTPUT"
```

## مخرجات الـ Job

مخرجات الـ step لا تعبر بين الـ jobs وحدها — أعلنها على الـ job واقرأها عبر `needs`:

```yaml
jobs:
  version:
    outputs:
      version: ${{ steps.pkg.outputs.version }}
  report:
    needs: version
    env:
      VERSION: ${{ needs.version.outputs.version }}
```

المخرجات **نصوص** فقط. للملفات استخدم [artifacts](07-artifacts.ar.md).

## الملخص والتنبيهات

```bash
echo "### Result ✅" >> "$GITHUB_STEP_SUMMARY"   # Markdown في صفحة التشغيل
echo "::notice::done"  ·  "::warning::careful"  ·  "::error::broken"
echo "::group::title" … "::endgroup::"           # قسم قابل للطي في اللوج
echo "::add-mask::$VALUE"                         # إخفاء قيمة
```

## فخاخ شائعة

- `if: ${{ x }} == 'y'` تكون true دائمًا (نص غير فارغ) — انظر [الخطأ 9](mistakes-log.ar.md).
- الـ output فارغ؟ نسيت `outputs:` على الـ job أو `needs:` ([الخطأ 10](mistakes-log.ar.md)).
- الـ job المتخطّى تكون مخرجاته نصوصًا فارغة.
- `failure()` لا تعمل بسبب خطوة عليها `continue-on-error: true`.

## تمرين

شغّله يدويًا بـ `fail=true`؛ راقب الخطوة 5 تفشل وتعمل الخطوتان 6 (`failure()`) و7 (`always()`).

⬅️ [04 — CI · Matrix · Cache](04-ci-matrix-cache.ar.md) · ➡️ [06 — الأسرار · المتغيرات · الصلاحيات](06-secrets-vars-permissions.ar.md)
