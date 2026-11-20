🌐 [English](12-reusable-workflows.md) · **العربية**

# 12 — Reusable Workflows

> المستدعي: [`12-reusable.yml`](../.github/workflows/12-reusable.yml)
> المُستدعى: [`12-reusable-called.yml`](../.github/workflows/12-reusable-called.yml)

**الهدف:** اكتب pipeline مرة واحدة واستدعِه من workflows كثيرة.

## الـ workflow المُستدعى

```yaml
on:
  workflow_call:
    inputs:
      script: { type: string, required: true }
      node-version: { type: string, default: '22' }
    secrets: # اختياري: أعلن أسرارًا صريحة
      DEMO_SECRET: { required: false }
    outputs:
      pnpm-version:
        value: ${{ jobs.run.outputs.pnpm-version }}
```

## المستدعي — `uses` على **job**

```yaml
jobs:
  lint:
    uses: ./.github/workflows/12-reusable-called.yml
    with:
      script: lint
    secrets: inherit
  report:
    needs: lint
    runs-on: ubuntu-latest
    steps:
      - run: echo "${{ needs.lint.outputs.pnpm-version }}"
```

## القواعد

- الـ job المستدعي فيه `uses` + `with` + `secrets` — **بلا `steps` وبلا `runs-on`**.
- نفس المستودع: `./.github/workflows/x.yml`. مستودع آخر: `owner/repo/.github/workflows/x.yml@ref`.
- الملف يجب أن يكون **مباشرة** في `.github/workflows/` (لا مجلدات فرعية).
- الأسرار **لا** تُمرَّر تلقائيًا: اذكرها تحت `secrets:` أو استخدم `secrets: inherit`.
- صلاحيات الـ workflow المُستدعى تساوي أو تضيق عن المستدعي فقط.
- مسار المخرجات: step ← `jobs.<id>.outputs` ← `on.workflow_call.outputs` ← `needs.<job المستدعي>.outputs`.

## Reusable workflow مقابل Composite action

|             | Reusable workflow       | Composite action ([09](09-composite-actions.ar.md)) |
| ----------- | ----------------------- | --------------------------------------------------- |
| يجمع        | **jobs** (runners خاصة) | **خطوات** (runner المستدعي)                         |
| يُستدعى على | مستوى الـ job           | مستوى الـ step                                      |
| الأنسب لـ   | pipelines كاملة         | بضع خطوات متكررة                                    |

## فخاخ شائعة

- نسيان `on: workflow_call` أو وضع `steps` في الـ job المستدعي ([الخطأ 18](mistakes-log.ar.md)).
- السر فارغ في المُستدعى ← أضف `secrets: inherit`.
- ملف داخل مجلد فرعي مثل `workflows/reuseble/` ← لا يُعثر عليه.

## تمرين

استدعِ الـ workflow مرة ثالثة بـ `script: build`، واطبع الـ output في `report`.

⬅️ [11 — المعاينات](11-previews.ar.md) · ➡️ [13 — Environments](13-environments.ar.md)
