🌐 [English](10-deploy.md) · **العربية**

# 10 — النشر على Firebase Hosting

> الملف: [`10-deploy.yml`](../.github/workflows/10-deploy.yml)
> الـ Action: [`deploy-firebase-hosting`](../.github/actions/deploy-firebase-hosting/action.yml)

**الهدف:** كل push إلى `main` يحدّث الموقع الحي. (معاينات الـ PR ← [11](11-previews.ar.md)؛ الدخول بلا مفاتيح ← [14](14-oidc.ar.md).)

## الإعداد المسبق (مرة واحدة)

1. أنشئ مشروعًا في [Firebase Console](https://console.firebase.google.com) وفعّل **Hosting**.
2. **Project settings → Service accounts** ← أنشئ مفتاح JSON وامنح دور **Firebase Hosting Admin**.
3. GitHub ← **Settings → Secrets and variables → Actions**:
   - Secret باسم `FIREBASE_SERVICE_ACCOUNT` = **محتوى** ملف JSON
   - Variable باسم `FIREBASE_PROJECT_ID` = معرّف المشروع
4. احذف ملف JSON من جهازك (أو خزّنه بأمان). **لا ترفعه للمستودع أبدًا.**

## التدفق

```
push إلى main → checkout → setup-pnpm → build → تجهيز site/ → deploy → الرابط في الملخص
```

## الـ Composite Action

| الخطوة               | ما تفعله                                                      | لماذا                          |
| -------------------- | ------------------------------------------------------------- | ------------------------------ |
| Write credentials    | JSON ← `$RUNNER_TEMP` وتعيين `GOOGLE_APPLICATION_CREDENTIALS` | Firebase CLI يقرأ ملفًا        |
| Ensure firebase.json | ينشئ إعدادًا بسيطًا إن لم يوجد                                | لا حاجة لملف إضافي في المستودع |
| Deploy               | `deploy --only hosting`، أو `hosting:channel:deploy` مع قناة  | حسب `channel-id`               |
| Cleanup              | يحذف ملف الاعتماد (`if: always()`)                            | لا يبقى سر على الـ runner      |

| المدخل            | مطلوب | الافتراضي | الوصف                                                            |
| ----------------- | ----- | --------- | ---------------------------------------------------------------- |
| `service-account` | ❌    | فارغ      | محتوى JSON؛ فارغ = استخدم اعتمادًا موجودًا ([14](14-oidc.ar.md)) |
| `project-id`      | ✅    | —         | معرّف مشروع Firebase                                             |
| `public-dir`      | ❌    | `site`    | المجلد المنشور                                                   |
| `channel-id`      | ❌    | فارغ      | فارغ = نشر حي                                                    |
| `expires`         | ❌    | `7d`      | عمر قناة المعاينة                                                |

المخرج: `url` — عنوان النشر.

## عن المشروع

تطبيق NestJS **خادم** وليس موقعًا ثابتًا، فلا يصلح نشر `dist/` على Hosting. الـ workflow ينشر صفحة `site/index.html` بسيطة (الـ commit والفرع) لتعلّم آلية النشر. لنشر API حقيقي استخدم Cloud Run أو Cloud Functions.

## الأمان

- السر يُمرَّر عبر `env` وليس مباشرة في `run`.
- `cancel-in-progress: false` يمنع قطع نشر جارٍ ([08](08-concurrency-timeouts.ar.md)).
- مفتاح JSON طويل الأمد هو نقطة الضعف ← استبدله بـ **OIDC** ([14](14-oidc.ar.md)).
- احمِ الإنتاج بـ **Environment** وموافقة ([13](13-environments.ar.md)).

## فخاخ شائعة

- لصق **مسار** الملف بدل **محتواه** في الـ Secret.
- Hosting غير مفعّل أو دور ناقص ← 403.
- الـ composite action لا يرى `secrets` — مرّرها عبر `inputs`.

## تمرين

ادفع تعديلًا إلى `main`، افتح ملخص التشغيل واضغط رابط النشر.

⬅️ [09 — Composite Actions](09-composite-actions.ar.md) · ➡️ [11 — المعاينات](11-previews.ar.md)
