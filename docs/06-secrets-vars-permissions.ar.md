🌐 [English](06-secrets-vars-permissions.md) · **العربية**

# 06 — الأسرار · المتغيرات · الصلاحيات

> الملف: [`06-secrets.yml`](../.github/workflows/06-secrets.yml)

**الهدف:** حماية الأسرار وتقليل الضرر لو حدث تسريب.

## ثلاثة مصادر للقيم

| المصدر      | يُعرَّف في                     | حساس؟   | في اللوج | الوصول             |
| ----------- | ------------------------------ | ------- | -------- | ------------------ |
| `env`       | ملف الـ workflow نفسه          | لا      | ظاهر     | `$APP_ENV`         |
| `vars.*`    | Settings → Variables → Actions | لا      | ظاهر     | `${{ vars.X }}`    |
| `secrets.*` | Settings → Secrets → Actions   | **نعم** | `***`    | `${{ secrets.X }}` |

مرّرها عبر `env` واقرأها كمتغيرات shell:

```yaml
env:
  DEMO_SECRET: ${{ secrets.DEMO_SECRET }}
  DEMO_VAR: ${{ vars.DEMO_VAR }}
```

## الإخفاء (Masking)

GitHub يستبدل قيمة السر بـ `***` في اللوج، لكنه:

- ❌ لا يكتشف النسخ المحوّلة (base64، مقسّمة، معكوسة)
- ❌ لا يمنعك من إرسال السر لخدمة خارجية
- ✅ يمكنك إخفاء قيمة مولّدة: `echo "::add-mask::$VALUE"`

## أقل صلاحيات ممكنة

`GITHUB_TOKEN` يُنشأ لكل تشغيل. قيّده:

```yaml
permissions: {} # على مستوى الملف: لا شيء

jobs:
  my-job:
    permissions:
      contents: read # على مستوى الـ job: ما يلزم فقط
```

| الصلاحية               | تسمح بـ                      |
| ---------------------- | ---------------------------- |
| `contents: read`       | قراءة الكود (checkout)       |
| `contents: write`      | الدفع وإنشاء الإصدارات       |
| `pull-requests: write` | التعليق على الـ PRs          |
| `packages: write`      | الرفع إلى GHCR               |
| `id-token: write`      | OIDC (دخول سحابي بلا مفاتيح) |

الخطأ `Resource not accessible by integration` = صلاحية ناقصة.

## حقن الأوامر (Script Injection)

`${{ }}` تُستبدل **نصيًا قبل** تشغيل الـ shell، فعنوان PR خبيث قد ينفّذ أوامر.

```yaml
# ❌ خطر
run: echo "${{ github.event.pull_request.title }}"

# ✅ آمن
env:
  TITLE: ${{ github.event.pull_request.title }}
run: echo "$TITLE"
```

## الـ Forks

- `pull_request` من fork **لا يستلم الأسرار** و`GITHUB_TOKEN` فيه للقراءة فقط.
- تجنّب `pull_request_target` إلا لو تفهم مخاطره تمامًا.

## تثبيت الـ Actions

`@v7` مقبول للـ actions الرسمية. للخارجية ثبّتها على **commit SHA كامل**.

## قائمة تحقق

- [ ] `permissions` معرّفة صراحة في كل workflow
- [ ] لا مدخلات خارجية مباشرة داخل `run`
- [ ] الأسرار تُمرَّر عبر `env`
- [ ] أسرار الإنتاج خلف Environment وموافقة ([13](13-environments.ar.md))
- [ ] لا طباعة للأسرار حتى لو كانت مخفية

## تمرين

أنشئ `DEMO_SECRET` و`DEMO_VAR`، شغّل الـ workflow، وقارن ظهور كل منهما في اللوج.

⬅️ [05 — Conditionals و Outputs](05-conditionals-outputs.ar.md) · ➡️ [07 — Artifacts](07-artifacts.ar.md)
