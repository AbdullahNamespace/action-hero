🌐 [English](13-environments.md) · **العربية**

# 13 — Environments والموافقات

> الملف: [`13-environments.yml`](../.github/workflows/13-environments.yml)

**الهدف:** حماية الإنتاج: staging يعمل تلقائيًا، وproduction ينتظر موافقة إنسان.

```
 staging (تلقائي) ──► production (⏸️ ينتظر الموافقة)
```

## الإعداد (Settings → Environments)

| البيئة       | الحماية                                                                      |
| ------------ | ---------------------------------------------------------------------------- |
| `staging`    | بلا حماية                                                                    |
| `production` | **Required reviewers** (أنت)، مؤقّت انتظار اختياري، الفروع المسموحة = `main` |

البيئة المذكورة في workflow ولم تُنشأ بعد تُنشأ **بلا** حماية.
قواعد الحماية في المستودعات الخاصة قد تتطلب خطة مدفوعة.

## في الـ workflow

```yaml
jobs:
  production:
    needs: staging
    environment:
      name: production
      url: https://example.com # يظهر في التشغيل وتبويب Deployments
```

الـ job **يتوقف قبل أن يبدأ**؛ ويوافق المراجعون أو يرفضون من صفحة التشغيل.

## أسرار ومتغيرات البيئة

نفس الأسماء بقيم مختلفة لكل بيئة — ولا يقرؤها إلا الـ jobs التي تستهدف تلك البيئة:

```yaml
env:
  ENV_SECRET: ${{ secrets.ENV_SECRET }} # قيمة staging أو production
  ENV_NAME: ${{ vars.ENV_NAME }}
```

ضع اعتمادات الإنتاج **فقط** في بيئة production.

## قواعد الحماية

| القاعدة             | الأثر                                   |
| ------------------- | --------------------------------------- |
| Required reviewers  | حتى 6 أشخاص/فرق؛ تكفي موافقة واحدة      |
| Wait timer          | تأخير قبل بدء الـ job                   |
| Deployment branches | فقط الفروع/الوسوم المذكورة تنشر         |
| Prevent self-review | من شغّل التشغيل لا يستطيع الموافقة عليه |

## فخاخ شائعة

- تفعيل المراجعين دون تعيين أحد ← انتظار للأبد ([الخطأ 19](mistakes-log.ar.md)).
- أسرار على مستوى المستودع يراها كل job — انقل أسرار الإنتاج إلى البيئة.
- الـ job الذي لا يعلن `environment:` لا يقرأ أسرار تلك البيئة.

## تمرين

أنشئ البيئتين، أضف مراجعًا لـ `production`، شغّل الـ workflow ووافق من صفحة التشغيل.

⬅️ [12 — Reusable Workflows](12-reusable-workflows.ar.md) · ➡️ [14 — OIDC](14-oidc.ar.md)
