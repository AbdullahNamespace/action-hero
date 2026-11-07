🌐 [English](03-contexts.md) · **العربية**

# 03 — الـ Contexts والتعبيرات

> الملف: [`03-contexts.yml`](../.github/workflows/03-contexts.yml)

**الهدف:** قراءة معلومات التشغيل عبر الـ contexts واستخدام التعبيرات بأمان.

## الصيغة

`${{ <تعبير> }}` يُقيَّم ويُستبدل **نصًا قبل** تشغيل الخطوة.

## الـ Contexts

| الـ Context | يحتوي                                        | مثال                       |
| ----------- | -------------------------------------------- | -------------------------- |
| `github`    | التشغيل والمستودع والحدث والمنفّذ            | `github.ref_name`          |
| `runner`    | الجهاز                                       | `runner.os`، `runner.temp` |
| `env`       | متغيرات `env:`                               | `env.GREETING`             |
| `vars`      | متغيرات المستودع/البيئة                      | `vars.FIREBASE_PROJECT_ID` |
| `secrets`   | الأسرار                                      | `secrets.DEMO_SECRET`      |
| `inputs`    | مدخلات `workflow_dispatch` / `workflow_call` | `inputs.name`              |
| `matrix`    | تركيبة الـ matrix الحالية                    | `matrix.node`              |
| `steps`     | مخرجات/نتائج الخطوات السابقة                 | `steps.ver.outputs.v`      |
| `needs`     | مخرجات/نتائج الـ jobs المطلوبة               | `needs.build.result`       |
| `job`       | حالة الـ job الحالي                          | `job.status`               |

حقول `github.*` الشائعة: `event_name`، `ref`، `ref_name`، `sha`، `actor`، `repository`، `run_id`، `event.pull_request.number`.

## العوامل والدوال

- العوامل: `==` `!=` `&&` `||` `!` `<` `>` (مقارنة النصوص لا تفرّق بين الحروف الكبيرة والصغيرة). النصوص بعلامة **اقتباس مفردة**: `'main'`.
- نمط القيمة الافتراضية: `matrix.experimental || false`.

| الدالة                      | الغرض                      | مثال                                           |
| --------------------------- | -------------------------- | ---------------------------------------------- |
| `contains(a, b)`            | نص فرعي / عضوية في مصفوفة  | `contains(github.ref, 'main')`                 |
| `startsWith` / `endsWith`   | فحص البداية / النهاية      | `startsWith(github.ref, 'refs/tags/')`         |
| `format(fmt, …)`            | بناء نص                    | `format('{0}-{1}', github.job, github.run_id)` |
| `join(array, sep)`          | مصفوفة ← نص                | `join(matrix.os, ',')`                         |
| `toJSON(x)` / `fromJSON(s)` | تحويل إلى JSON / منه       | `toJSON(github)`                               |
| `hashFiles(glob)`           | بصمة ملفات (لمفاتيح الكاش) | `hashFiles('pnpm-lock.yaml')`                  |

دوال الحالة (`success()` و`failure()` و`always()`) ← [05](05-conditionals-outputs.ar.md).

## فحص سياق

```yaml
env:
  GITHUB_CTX: ${{ toJSON(github) }}
run: |
  echo "::group::github context"
  echo "$GITHUB_CTX"
  echo "::endgroup::"
```

التوكن يظهر `***`.

## ⚠️ حقن الأوامر

القيم التي يتحكم بها غيرك (عنوان PR، اسم فرع، نص issue) **لا** توضع مباشرة في `run:`. مرّرها عبر `env:` ([06](06-secrets-vars-permissions.ar.md)):

```yaml
env:
  TITLE: ${{ github.event.pull_request.title }}
run: echo "$TITLE"
```

## فخاخ شائعة

- الـ contexts ليست متاحة في كل مكان: `secrets` لا تُستخدم في `if:` على مستوى الـ job — انسخها إلى `env` أولًا.
- `${{ }}` تُستبدل _قبل_ الـ shell، فقواعد الاقتباس هي قواعد الـ shell لا YAML.
- علامة اقتباس مزدوجة داخل التعبير (`"main"`) خطأ في الصياغة.

## تمرين

افتح PR يعدّل الـ workflow، شغّله، وابحث عن عنوان الـ PR مطبوعًا بأمان من `$TITLE`.

⬅️ [02 — المشغّلات](02-triggers.ar.md) · ➡️ [04 — CI · Matrix · Cache](04-ci-matrix-cache.ar.md)
