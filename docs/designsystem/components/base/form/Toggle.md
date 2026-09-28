# Component Guideline: Toggle

> NestUI Toggle Switch 仕様。Tailwind CSS。値は `tokens/`（色は `color.primary.*` / slate スケール等）を正とし、以下の class はその適用例。`role="switch"` の `<button>` で実装する（`<input type="checkbox">` のスタイリングで代替しない）。

## 1. Component Identity
- **Name**: Toggle（Switch）
- **Variants**: 基本 / 説明付き / ステータス表示付き
- **Responsibility**:
  - する: ON/OFF の2値設定を即時反映する（確定ボタン不要）
  - しない: フォーム送信で確定する設定（→ Checkbox）、3状態以上の選択（→ Radio）、破壊的操作（確認ダイアログを併用）
- **Target Platform**: デスクトップ Web アプリ（業務 SaaS）

**原則**: 操作と同時に反映（楽観的更新→失敗時はロールバック + エラートースト）/ Checkbox との使い分け（送信確定=Checkbox、即時反映=Toggle）/ 副作用をラベルと補足で明示 / 色だけで ON/OFF を伝えない（Thumb の位置変化を必ず併用）。

---

## 2. Variants & States

**Variants**

| Variant | 説明 | 用途 |
|---------|------|------|
| 基本 | ラベル + トグル | 設定画面の各項目 |
| 説明付き | ラベル + 補足テキスト + トグル | 影響範囲を補足 |
| ステータス表示付き | トグル横に「ON/OFF」テキスト | 状態を明示したい場合 |

**States**（Track / Thumb）

| State | Track | Thumb |
|-------|-------|-------|
| OFF | `bg-slate-200` | 左寄せ・白（`translate-x-0.5`） |
| ON | `bg-primary-500` | 右寄せ・白（`translate-x-[22px]`、Small `translate-x-[14px]`）+ `aria-checked="true"` |
| OFF + Hover | `hover:bg-slate-300` | 左寄せ |
| ON + Hover | `hover:bg-primary-700` | 右寄せ |
| Focus | `focus-visible:ring-2 focus-visible:ring-primary-700 focus-visible:ring-offset-1` | — |
| Disabled OFF | `bg-slate-100 opacity-50 cursor-not-allowed` | 左寄せ + `aria-disabled="true"` |
| Disabled ON | `bg-primary-300 opacity-50 cursor-not-allowed` | 右寄せ + `aria-disabled="true"` |

---

## 3. サイズ / Anatomy

| サイズ | Track | Thumb | Thumb 移動量 | 用途 |
|--------|-------|-------|-------------|------|
| Medium（既定） | `w-11 h-6` | `w-5 h-5` | `translate-x-[22px]` | 標準設定画面 |
| Small | `w-8 h-5` | `w-3.5 h-3.5` | `translate-x-[14px]` | テーブル内・密なレイアウト |

共通: Track `relative inline-flex items-center rounded-full transition-colors`、Thumb `inline-block rounded-full bg-white shadow-sm transition-transform`。

**Anatomy**: Track + Thumb + Label + Description（任意）+ Status Text（任意）。

> **行間の注意**: `body { line-height: 2.0 }` 環境では、ラベル + 説明を含む `<div>` に `leading-normal` を付与する。

---

## 4. Composition Rules

許可: ラベル（`<label>` or `aria-labelledby`）/ Description / Status Text。
禁止: `<label>` の省略 / `<input type="checkbox">` をスタイリングでトグルに見せること（`role="switch"` の `<button>` を使う）/ 確定ボタンが必要な操作への使用（→ Checkbox）/ 確認なしでの破壊的操作。
配置: ラベル左・トグル右の `flex items-center justify-between` が標準。設定リストは `divide-y divide-slate-100` で各行 `py-4`。

---

## 5. Layout & Spacing

- 行レイアウト: `flex items-center justify-between`（説明付きは `items-start gap-4`、トグルに `flex-shrink-0`）
- 設定リスト: `divide-y divide-slate-100`、各項目 `py-4`
- Small + Status Text は `flex items-center gap-3`

---

## 6. Token Mapping

| 用途 | トークン |
|------|---------|
| ON Track / hover | `color.primary.500` / `color.primary.700` |
| OFF Track / hover | slate-200 / slate-300（`color.surface.sunken` 近傍のグレースケール） |
| Thumb | `color.content.inverse`（white） |
| Disabled ON Track | `color.primary.300` |
| Disabled OFF Track | slate-100 |
| focus ring | `color.border.focus`(= primary-700) |
| ラベル / 説明 | `color.content.primary` 系 / `color.content.secondary`(#5a6c7f) |

---

## 7. Accessibility
- `role="switch"` を `<button>` に付与。`aria-checked`: `"true"` / `"false"`（省略禁止）
- ラベル: `aria-labelledby` でラベルの `id` を参照、または `aria-label` を直接付与
- Description は `aria-describedby` で紐付け
- キーボード: Tab でフォーカス、Space / Enter で切り替え
- Disabled: `aria-disabled="true"` + 操作無効化
- モーション: `prefers-reduced-motion` 時は Thumb のスライドアニメーションを無効化
- コントラスト: Track の ON/OFF 状態の色差 3:1 以上

## 8. Content Guidelines
- ラベルは設定対象を端的に（「メール通知」「二段階認証」）
- Description で副作用・影響範囲を補足（「ログイン時に認証コードが必要になります」）

## 9. Usage Do / Don't

```html
<!-- Do: 基本（OFF） -->
<div class="flex items-center justify-between">
  <label id="toggle-label" class="text-sm font-medium text-slate-700">メール通知</label>
  <button type="button" role="switch" aria-checked="false" aria-labelledby="toggle-label"
    class="relative inline-flex h-6 w-11 items-center rounded-full bg-slate-200 hover:bg-slate-300
           focus:outline-none focus-visible:ring-2 focus-visible:ring-primary-700 focus-visible:ring-offset-1 transition-colors"
    onclick="this.setAttribute('aria-checked', this.getAttribute('aria-checked') === 'true' ? 'false' : 'true')">
    <span class="inline-block h-5 w-5 rounded-full bg-white shadow-sm translate-x-0.5 transition-transform
                 [[aria-checked=true]_&]:translate-x-[22px]"></span>
  </button>
</div>

<!-- Do: 説明付き（ON） -->
<div class="flex items-start justify-between gap-4">
  <div class="leading-normal">
    <label id="t-desc-label" class="text-sm font-medium text-slate-700">二段階認証</label>
    <p id="t-desc" class="text-sm text-slate-500 mt-1">ログイン時に認証コードの入力が必要になります</p>
  </div>
  <button type="button" role="switch" aria-checked="true" aria-labelledby="t-desc-label" aria-describedby="t-desc"
    class="relative inline-flex h-6 w-11 items-center rounded-full bg-primary-500 flex-shrink-0 hover:bg-primary-700
           focus:outline-none focus-visible:ring-2 focus-visible:ring-primary-700 focus-visible:ring-offset-1 transition-colors">
    <span class="inline-block h-5 w-5 rounded-full bg-white shadow-sm translate-x-[22px] transition-transform"></span>
  </button>
</div>

<!-- Do: Small -->
<button type="button" role="switch" aria-checked="true" aria-label="有効"
  class="relative inline-flex h-5 w-8 items-center rounded-full bg-primary-500 hover:bg-primary-700
         focus:outline-none focus-visible:ring-2 focus-visible:ring-primary-700 focus-visible:ring-offset-1 transition-colors">
  <span class="inline-block h-3.5 w-3.5 rounded-full bg-white shadow-sm translate-x-[14px] transition-transform"></span>
</button>
```

**Don't**: `aria-checked` 省略 / Space キー操作未実装 / `<label>` 省略 / 破壊的操作に確認なしで使用 / OFF Track を `bg-slate-200` 以外に変える（Figma 準拠の slate を維持）/ `<input type="checkbox">` で代替。

---

## 10. Implementation Notes
- 反映処理: ①操作 → ②UI 即時更新（楽観的更新）→ ③バックエンドへ送信 → ④成功は何もしない / 失敗は UI を戻しエラートースト
- トークン参照は `guidelines/TOKEN_GUIDE.md`。設定画面のレイアウトは Form / secondary-nav パターンを参照
