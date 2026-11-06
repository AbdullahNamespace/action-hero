🌐 [English](01-anatomy.md) · **العربية**

# 01 — تشريح الـ Workflow

> الملف: [`01-hello.yml`](../.github/workflows/01-hello.yml)

**الهدف:** فهم الأجزاء الأربعة لأي workflow.

## البنية

```yaml
name: 01 · Hello # 1) الاسم في تبويب Actions

on: # 2) متى يعمل؟
  push:
    branches: [main]
  workflow_dispatch:

jobs: # 3) ماذا يعمل؟
  hello:
    runs-on: ubuntu-latest
    steps: # 4) الخطوات بالترتيب
      - uses: actions/checkout@v7
      - run: echo "Hello"
```

## المفاهيم

- **`on`** — الأحداث المشغِّلة ← [02](02-triggers.ar.md).
- **`jobs`** — كل job على runner مستقل ونظيف. تعمل بالتوازي ما لم تربطها بـ `needs`. المعرّف (`hello`) هو ما تشير إليه `needs`، و`name:` للعرض فقط.
- **`steps`** — كل step أحد نوعين:

| النوع  | الاستخدام           | مثال                        |
| ------ | ------------------- | --------------------------- |
| `run`  | تشغيل أمر shell     | `run: ls -la`               |
| `uses` | استدعاء action جاهز | `uses: actions/checkout@v7` |

الأوامر متعددة الأسطر تُكتب بالرمز `|`.

- **الـ Contexts** — تعبيرات `${{ ... }}` (التفاصيل في [03](03-contexts.ar.md)):

| التعبير             | القيمة               |
| ------------------- | -------------------- |
| `github.repository` | اسم المستودع         |
| `github.ref_name`   | اسم الفرع/الوسم      |
| `github.sha`        | معرّف الـ commit     |
| `github.actor`      | من شغّل الـ workflow |
| `github.event_name` | نوع الحدث            |

## فخاخ شائعة

- نسيان `checkout` ← الـ runner يبدأ فارغًا.
- مسافتان للمحاذاة، ولا Tab أبدًا.
- القيمة التي فيها `:` أو `#` ضعها بين علامتي اقتباس.

➡️ التالي: [02 — المشغّلات](02-triggers.ar.md)
