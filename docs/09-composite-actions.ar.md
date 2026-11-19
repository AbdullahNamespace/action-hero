🌐 [English](09-composite-actions.md) · **العربية**

# 09 — Composite Actions

> الملف: [`09-composite.yml`](../.github/workflows/09-composite.yml)
> الـ Action: [`setup-pnpm`](../.github/actions/setup-pnpm/action.yml)

**الهدف:** تجميع **خطوات** متكررة في action واحد قابل لإعادة الاستخدام.

## التركيب

```yaml
# .github/actions/setup-pnpm/action.yml
name: Setup pnpm
description: Install pnpm and Node.js with caching, then optionally install dependencies.
inputs:
  node-version:
    description: Node.js version to install.
    required: false
    default: '22'
  install:
    description: Run `pnpm install --frozen-lockfile` when set to "true".
    required: false
    default: 'true'
outputs:
  pnpm-version:
    description: The installed pnpm version.
    value: ${{ steps.ver.outputs.version }}
runs:
  using: composite
  steps:
    - uses: pnpm/action-setup@v4
    - uses: actions/setup-node@v7
      with: { node-version: '${{ inputs.node-version }}', cache: pnpm }
    - if: inputs.install == 'true'
      shell: bash
      run: pnpm install --frozen-lockfile
    - id: ver
      shell: bash
      run: echo "version=$(pnpm --version)" >> "$GITHUB_OUTPUT"
```

## الاستخدام

```yaml
- uses: actions/checkout@v7 # أولًا — الـ action داخل المستودع
- id: setup
  uses: ./.github/actions/setup-pnpm
  with:
    node-version: '24'
- run: echo "${{ steps.setup.outputs.pnpm-version }}"
```

## القواعد

- `runs.using: composite` مع `steps`.
- `name` و`description` مطلوبان للـ action، و`description` مطلوب لكل input وكل output.
- **`shell:` إلزامي** في كل خطوة `run`.
- المدخلات نصوص دائمًا (`"true"` لا `true`).
- المخرجات تُربط بخطوة: `value: ${{ steps.<id>.outputs.x }}`.
- **لا `secrets` تلقائيًا** — مرّرها كمدخلات.
- الملفات المجاورة للـ action: `${{ github.action_path }}`.
- المسار المحلي (`./.github/actions/x`) يحتاج `checkout` قبله؛ والـ action المنشور يُستدعى بـ `owner/repo/path@ref`.

## Composite مقابل Reusable workflow

|            | Composite action     | Reusable workflow ([12](12-reusable-workflows.ar.md)) |
| ---------- | -------------------- | ----------------------------------------------------- |
| يجمع       | **خطوات**            | **jobs**                                              |
| يُستدعى بـ | `uses:` داخل `steps` | `uses:` على **job**                                   |
| يعمل على   | runner المستدعي      | runner(s) خاصة به                                     |
| الأسرار    | عبر inputs           | `secrets:` / `inherit`                                |
| متى        | تكرار بضع خطوات      | مشاركة pipeline كامل                                  |

للمنطق بـ JavaScript أو Docker ← [15](15-release-custom-actions.ar.md).

## فخاخ شائعة

- غياب `shell` ← `Required property is missing: shell` ([الخطأ 16](mistakes-log.ar.md)).
- استدعاء action محلي قبل `checkout` ← `Can't find 'action.yml'` ([الخطأ 15](mistakes-log.ar.md)).
- الترتيب مهم داخل `setup-pnpm`: `pnpm/action-setup` قبل `setup-node` مع `cache: pnpm`.

## تمرين

أضف مدخلًا `run-build` (الافتراضي `"false"`) إلى `setup-pnpm` ينفّذ `pnpm build` عندما يكون `"true"`.

⬅️ [08 — Concurrency والمهلات](08-concurrency-timeouts.ar.md) · ➡️ [10 — النشر](10-deploy.ar.md)
