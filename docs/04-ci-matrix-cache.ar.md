🌐 [English](04-ci-matrix-cache.md) · **العربية**

# 04 — CI · Matrix · Cache

> الملف: [`04-ci.yml`](../.github/workflows/04-ci.yml)

**الهدف:** خط CI حقيقي لتطبيق NestJS: فحص، اختبار، ثم بناء.

```
 lint ──┐
 test ──┼──► build        test على Node 22 + 24
 e2e  ──┘                 (+ 26 تجريبي)
```

الثلاثة الأولى **بالتوازي**، و`build` ينتظرها جميعًا.

## المفاهيم

**`needs`** — `needs: [lint, test, e2e]`. لو فشل أي منها يُتخطى `build`.

**`strategy.matrix`**

```yaml
strategy:
  fail-fast: false # اترك الباقي يكمل
  matrix:
    node: ['22', '24']
    include: # تركيبة إضافية منفردة
      - node: '26'
        experimental: true
    # exclude: [{ os: windows-latest, node: "22" }]   # استبعاد تركيبة
```

استخدم `${{ matrix.node }}` في الخطوات. اكتب الإصدارات بين علامتي اقتباس (`"22"`) وإلا صار `20.10` رقمًا عشريًا.
`continue-on-error: ${{ matrix.experimental || false }}` يمنع فشل التجريبي من كسر التشغيل.

**الكاش** — `setup-pnpm` يستخدم `cache: pnpm` من `actions/setup-node`، ومفتاحه من `pnpm-lock.yaml`. الصيغة اليدوية:

```yaml
- uses: actions/cache@v6
  with:
    path: ~/.local/share/pnpm/store
    key: pnpm-${{ runner.os }}-${{ hashFiles('pnpm-lock.yaml') }}
```

**`concurrency`** — دفع جديد يلغي التشغيل القديم لنفس المرجع:

```yaml
concurrency:
  group: ci-${{ github.workflow }}-${{ github.ref }}
  cancel-in-progress: true
```

**Composite action محلي** — `uses: ./.github/actions/setup-pnpm` يستبدل 3 خطوات مكررة. نفّذ `checkout` **قبله**.

## فخاخ شائعة

- `pnpm/action-setup` يجب أن يأتي **قبل** `setup-node` مع `cache: pnpm`.
- `--frozen-lockfile` يفشل لو تغيّر `package.json` دون تحديث `pnpm-lock.yaml`.
- كل job يحتاج `checkout` وتثبيتًا خاصًا به (أجهزة منفصلة).
- Node 20 انتهى دعمه في أبريل 2026 — استخدم إصدارات مدعومة.

## تمرين

- أضف `"26"` إلى القائمة الأساسية وراقب ظهور job جديد.
- اكسر اختبارًا عمدًا وتأكد أن `build` يُتخطى.

⬅️ [03 — Contexts](03-contexts.ar.md) · ➡️ [05 — Conditionals و Outputs](05-conditionals-outputs.ar.md)
