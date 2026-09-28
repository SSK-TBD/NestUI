# Component Guideline: Checkbox

> NestUI Checkbox 仕様。Tailwind CSS。値は `tokens/`（色は `color.primary.*` / `color.border.*` 等）を正とし、以下の class はその適用例。ネイティブ `<input type="checkbox">` に `appearance-none` を当て、SVG オーバーレイで描画する。

## 1. Component Identity
- **Name**: Checkbox
- **Variants**: 単一（`default`）/ グループ（`<fieldset>`）/ 不確定（Indeterminate）
- **Responsibility**:
  - する: 選択肢群から0個以上を選ぶ。単一の ON/OFF 切り替え（送信で確定）
  - しない: 排他的単一選択（→ Radio）、即時反映の ON/OFF（→ Toggle）、選択肢が多数・動的増減（→ Dropdown）
- **Target Platform**: デスクトップ Web アプリ（業務 SaaS）

**原則**: チェックの切り替えだけでは確定しない（送信/確認ボタンで反映）/ 即時反映は Toggle / ラベルは能動的な肯定文。

---

## 2. Variants & States

**Variants**

| Variant | 説明 |
|---------|------|
| 単一 | 利用規約同意・メール購読など ON/OFF の2値 |
| グループ | `<fieldset>` + `<legend>` で複数選択肢をまとめる（縦並び推奨） |
| Indeterminate | 親子関係で子の一部のみ選択時に親が不確定（ダッシュ表示） |

**States**

| State | 変化 |
|-------|------|
| Unchecked | `border border-[#c4cdd9] bg-white` |
| Hover | `hover:bg-primary-50`（チェック時 `checked:hover:bg-primary-600`） |
| Checked | `checked:bg-primary-700 checked:border-primary-700` + SVG チェックマーク（`peer-checked:opacity-100`） |
| Indeterminate | `indeterminate:bg-primary-700 indeterminate:border-primary-700` + ダッシュ SVG（`peer-indeterminate:opacity-100`）。JS: `el.indeterminate = true` |
| Focus | `focus-visible:ring-2 focus-visible:ring-primary-700 focus-visible:ring-offset-1` |
| Error | `border-[#e93766] checked:bg-[#e93766] checked:border-[#e93766]` + `aria-invalid="true"` + エラーメッセージ |
| Disabled（未チェック） | `bg-white border-2 border-[#dde3eb] cursor-not-allowed`（`opacity-50` 禁止） |
| Disabled（チェック済み） | `bg-[#c4cdd9] border-[#c4cdd9] cursor-not-allowed` |

---

## 3. サイズ / Anatomy

| サイズ | 外側コンテナ | ボックス | チェック SVG | ラベル |
|--------|-------------|---------|-------------|--------|
| Medium（既定） | `w-6 h-6`（24px） | `w-5 h-5`（20px） | `w-3.5 h-3.5` | `text-[14px] tracking-[0.28px]` |
| Small | `w-6 h-6`（24px） | `w-3.5 h-3.5`（14px） | `w-2.5 h-2.5` | `text-[12px] tracking-[0.24px]` |

共通: ボックス `appearance-none rounded-[2px]`、ラベル `font-medium leading-[1.3] text-body`。`gap-1`（M）/ `gap-0.5`（S）。

**Anatomy**: Box + Checkmark（選択時）+ Indeterminate Mark（条件付き）+ Label + Group Label（`<legend>`）+ Helper / Error Text。

> **行間の注意**: `body { line-height: 2.0 }` 環境では、チェックボックスグループの包含 `<div>` に `leading-normal` を付与して行間をリセットする。

---

## 4. Composition Rules

許可: テキストノード（ラベル）/ グループは `<fieldset>` + `<legend>` / Helper・Error テキスト。
禁止: `<label>` の省略 / Checkbox 内に Checkbox を入れること（中間状態は `indeterminate` で表現）/ カード直下に `<fieldset>` を見出し代わりに使うこと。
Anti-pattern: 確認が必要なアクションに単体 Checkbox（「削除に同意」はチェックボックス + ボタンのセット）/ 排他的単一選択に Checkbox（→ Radio）。

---

## 5. Layout & Spacing

- ボックス-ラベル gap: `gap-1`（M, 4px）/ `gap-0.5`（S, 2px）
- グループ内の行間: `space-y-2`（包含 div に `leading-normal`）
- エラーメッセージはボックス幅分インデント（`ml-7`）+ `mt-1`
- 角丸 `rounded-[2px]`

---

## 6. Token Mapping

| 用途 | トークン |
|------|---------|
| 未チェック ボーダー / 背景 | `color.border.default`(#c4cdd9) / `color.surface.input`(#fff) |
| チェック済み 背景・ボーダー | `color.primary.700`（hover `.600`） |
| チェックマーク | `color.content.inverse`（white） |
| hover 背景（未チェック） | `color.primary.50` |
| エラー ボーダー・背景 | `color.semantic.error`(#e93766) |
| focus ring | `color.border.focus`(= primary-700) |
| disabled ボーダー（未チェック）/ 背景（チェック済み） | `color.border.subtle`(#dde3eb) / `color.border.default`(#c4cdd9) |
| disabled ラベル | `color.content.disabled`(#a1afc0) |
| 角丸 | `radius.sm`（→ `rounded-[2px]`） |

---

## 7. Accessibility
- `<label>` を各チェックボックスに関連付ける（`for` 属性 or ラッピング）
- グループは `<fieldset>` + `<legend>`
- `role="checkbox"`（ネイティブ input なら不要）。`aria-checked`: `true`/`false`/`mixed`（Indeterminate）
- キーボード: Tab フォーカス・Space で切り替え。Disabled はキーボードからも操作不可
- エラー: 色だけでなくテキスト + アイコン併用。`aria-describedby` で関連付け
- コントラスト: ラベル 4.5:1 以上、Box（ボーダー/背景）3:1 以上。テキスト200%拡大でクリッピングしない

## 8. Content Guidelines
- ラベルは能動的な肯定文（「メール通知を受け取る」「自動保存を有効にする」）
- 否定文ラベルは避ける（「通知を無効にする」→ Toggle を検討）
- Helper は1行以内

## 9. Usage Do / Don't

```html
<!-- Do: 単一チェックボックス（Medium） -->
<label class="inline-flex items-center gap-1 cursor-pointer">
  <span class="relative flex-shrink-0 w-6 h-6 flex items-center justify-center">
    <input type="checkbox"
      class="peer appearance-none w-5 h-5 rounded-[2px] border border-[#c4cdd9] bg-white
             checked:bg-primary-700 checked:border-primary-700 hover:bg-primary-50 checked:hover:bg-primary-600
             focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-primary-700 focus-visible:ring-offset-1
             cursor-pointer transition-colors duration-150">
    <svg class="absolute pointer-events-none opacity-0 peer-checked:opacity-100 w-3.5 h-3.5 text-white"
         viewBox="0 0 14 11" fill="none" stroke="currentColor" stroke-width="2.2" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true">
      <polyline points="1.5,5.5 5,9 12.5,1.5"/>
    </svg>
  </span>
  <span class="text-[14px] font-medium tracking-[0.28px] leading-[1.3] text-body">メール通知</span>
</label>

<!-- Do: Indeterminate（ダッシュ SVG を重ねる） -->
<label class="inline-flex items-center gap-1 cursor-pointer">
  <span class="relative flex-shrink-0 w-6 h-6 flex items-center justify-center">
    <input type="checkbox" id="cb-all"
      class="peer appearance-none w-5 h-5 rounded-[2px] border border-[#c4cdd9] bg-white
             checked:bg-primary-700 checked:border-primary-700 indeterminate:bg-primary-700 indeterminate:border-primary-700
             focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-primary-700 focus-visible:ring-offset-1 cursor-pointer transition-colors">
    <svg class="absolute pointer-events-none opacity-0 peer-indeterminate:opacity-100 w-2.5 h-0.5 text-white"
         viewBox="0 0 10 2" fill="none" stroke="currentColor" stroke-width="2.2" stroke-linecap="round" aria-hidden="true"><line x1="1" y1="1" x2="9" y2="1"/></svg>
  </span>
  <span class="text-[14px] font-medium tracking-[0.28px] leading-[1.3] text-body">すべて選択</span>
</label>
<script>document.getElementById('cb-all').indeterminate = true;</script>
```

**Don't**: `accent-primary-500`（→ `appearance-none` + カスタム SVG）/ `<label>` 省略 / `focus:ring-primary-500/50`（→ `focus-visible:ring-primary-700 ring-offset-1`）/ エラーを色だけで伝える / Disabled に `opacity-50`（→ 専用配色 `border-[#dde3eb]` / `bg-[#c4cdd9]`）/ 単一選択に Checkbox（→ Radio）。

---

## 10. Implementation Notes
- チェックマークは `peer-checked:opacity-100`、ダッシュは `peer-indeterminate:opacity-100` の SVG オーバーレイで制御
- `CheckboxGroup` 相当は `value` 配列を管理し、各 `checked` を計算して渡す
- アイコンは Lucide。エラーアイコンは circle-alert（`w-3.5 h-3.5`）
- トークン参照は `guidelines/TOKEN_GUIDE.md`
