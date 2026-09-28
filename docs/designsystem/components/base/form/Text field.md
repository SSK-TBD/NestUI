# Component Guideline: Text field

> NestUI TextField / TextBox 仕様。Tailwind CSS。値は `tokens/`（色は `color.border.*` / `color.primary.*` 等）を正とし、以下の class はその適用例。

## 1. Component Identity
- **Name**: Text field（TextBox）
- **Variants**: TextField / CopyTextField / InputBox（textarea）/ SelectBox / NumberInput
- **Responsibility**:
  - する: 単一行/複数行のテキスト入力、読み取り専用値のコピー、数値入力、固定リスト選択
  - しない: 5つ以上の固定選択（→ Dropdown）、ON/OFF（→ Toggle/Checkbox）、日付（→ Date Picker）
- **Target Platform**: デスクトップ Web アプリ（業務 SaaS）

**原則**: ラベルを常に表示（プレースホルダーで代替しない）/ エラーは即座に・具体的に（フィールド直下）/ フォーカスはボーダー変化のみ（`focus:ring-*` 禁止、`#c4cdd9`→`#2661cf`）/ 十分なタッチターゲット（M `h-9` / S `h-7`）/ カーソルカラー `caret-[#2661cf]`。

---

## 2. Variants & States

**Variants**

| Variant | 説明 | 用途 |
|---------|------|------|
| TextField | 単一行テキスト入力 | 名前・URL・検索 |
| CopyTextField | テキスト + コピーボタン付き（`readonly`） | 共有URL・APIキー |
| InputBox | 複数行テキストエリア（`<textarea>`） | 説明文・コメント |
| SelectBox | ドロップダウン選択（カスタム chevron 必須） | 固定リスト選択（→ Dropdown 参照） |
| NumberInput | 数値入力 + スピナーボタン | 数量・金額 |

**States**

| 状態 | ボーダー | 背景 | テキスト |
|------|----------|------|----------|
| Enable | `border-[#c4cdd9]` | `bg-white` | `text-[#3e5062]` |
| Hover | `border-[#c4cdd9]` | `bg-[#f7f9fb]` | `text-[#3e5062]` |
| Focus / Typing | `focus:border-[#2661cf]` | `bg-white` | `text-[#3e5062]`（ring なし） |
| Fill | `border-[#c4cdd9]` | `bg-white` | `text-[#3e5062]` |
| Error | `border-[#e93766]` | `bg-white` | `text-[#3e5062]` + `aria-invalid="true"` |
| Warning | `border-[#ee8c29]` | `bg-white` | `text-[#3e5062]` |
| Disable | `border-[#c4cdd9]` | `bg-[#f7f9fb]` | `text-[#a1afc0]` + `disabled` |

> プレースホルダー色: `placeholder:text-[#a1afc0]`。

---

## 3. サイズ / Anatomy

| サイズ | 高さ | テキスト | 左パディング | 用途 |
|--------|------|----------|--------------|------|
| Middle（既定） | `h-9`（36px） | `text-[12px]` | `px-2`（8px） | 標準フォーム |
| Small | `h-7`（28px） | `text-[12px]` | `px-2`（8px） | テーブル内・密なレイアウト |

共通: `font-medium tracking-[0.24px] border rounded-sm outline-none transition-colors caret-[#2661cf]`（font-family: Noto Sans JP）。横並びフォーム時も `h-9 leading-normal` を維持（`py-2` を外す）。

**Anatomy**: Label（`<label for>`）+ Required マーク（`*` + `aria-required`）+ Input Container + Leading Icon（任意）+ Input/`<textarea>` + Clear Button（任意 `w-4 h-4`）+ Trailing Icon/Button（コピー・スピナー等）+ Helper Text + Error/Warning Text。

---

## 4. Composition Rules

許可: 上部 `<label>` / Leading・Trailing アイコン / Clear ボタン / Helper・Error テキスト。
禁止: `<label>` の省略 / プレースホルダーのみでラベルを代替 / SelectBox でネイティブ矢印（`appearance-none` + カスタム chevron 必須）。
固有実装の注意:
- **NumberInput**: スピナーコンテナに `w-7 shrink-0`、input に `min-w-0`（幅指定がないと input が全幅を占有しボタンが不可視）。Up/Down 間は `border-b border-[#c4cdd9]`、コンテナ左は `border-l border-[#c4cdd9]`
- **CopyTextField**: `<input readonly>` 右端にコピーボタン（`w-7 h-7 m-0.5`）。成功後アイコンを checkmark に切替→1.5秒で復帰（→ Copy button 参照）
- **SelectBox の Clear**: Fill/Error/Warning で×アイコン表示、Clear 後は Enable に戻す、`aria-label="クリア"` 必須

---

## 5. Layout & Spacing

- 高さ: M `h-9` / S `h-7`。左右 `px-2`（textarea は `px-2 py-2`）
- Label と Input の間 `mb-1`（包含 div に `leading-normal`）
- Error / Warning / Helper は Input 直下 `mt-1`
- 角丸 `rounded-sm`（`rounded-lg` 禁止）

---

## 6. Token Mapping

| 用途 | トークン |
|------|---------|
| ボーダー（標準） | `color.border.default`(#c4cdd9) |
| ボーダー（フォーカス）/ caret | `color.border.focus`(#2661cf = primary-700) |
| 背景 / hover | `color.surface.input`(#fff) / `color.surface.base`(#f7f9fb) |
| テキスト / プレースホルダー | `#3e5062`（content 補助）/ `color.content.disabled`(#a1afc0) |
| エラーボーダー・テキスト | `color.semantic.error`(#e93766) |
| 警告ボーダー・テキスト | `color.semantic.warning`(#ee8c29) |
| disabled テキスト / 背景 | `color.content.disabled`(#a1afc0) / `color.surface.base` |
| 角丸 | `radius.sm`（→ `rounded-sm`） |

---

## 7. Accessibility
- `<label for="id">` と `<input id="id">` を一致させる
- 必須: `aria-required="true"` + 視覚的 `*`
- エラー: テキストに `id` + `role="alert"`、入力に `aria-describedby` + `aria-invalid="true"`
- Disabled: `disabled` 属性（HTML ネイティブ推奨）
- バリデーションタイミング: 送信時（全検証 + 最初のエラーへフォーカス）/ フォーカスアウト時（入力済みのみ）/ エラー表示中は入力中に再検証
- コントラスト: ラベル・入力・エラーテキストすべて 4.5:1 以上

## 8. Content Guidelines
- ラベルは入力内容を端的に。プレースホルダーは入力例（「サンプル 太郎」）
- エラーは何が間違っているか・どう直すかを明示（「有効なメールアドレスを入力してください」）

## 9. Usage Do / Don't

```html
<!-- Do: TextField（基本） -->
<div class="leading-normal">
  <label for="name" class="block text-sm font-medium text-slate-900 mb-1">名前</label>
  <input type="text" id="name" placeholder="サンプル 太郎"
    class="w-full h-9 px-2 text-[12px] font-medium tracking-[0.24px] border border-[#c4cdd9] rounded-sm bg-white text-[#3e5062]
           outline-none focus:border-[#2661cf] hover:bg-[#f7f9fb] caret-[#2661cf] transition-colors placeholder:text-[#a1afc0]" />
</div>

<!-- Do: Error -->
<div class="leading-normal">
  <label for="email" class="block text-sm font-medium text-slate-900 mb-1">メールアドレス <span class="text-[#e93766] ml-0.5">*</span></label>
  <input type="email" id="email" aria-required="true" aria-invalid="true" aria-describedby="email-error" value="invalid@"
    class="w-full h-9 px-2 text-[12px] font-medium tracking-[0.24px] border border-[#e93766] rounded-sm bg-white text-[#3e5062] outline-none caret-[#2661cf] transition-colors" />
  <p id="email-error" role="alert" class="mt-1 text-xs text-[#e93766] flex items-center gap-1">
    <svg class="w-3.5 h-3.5 shrink-0" fill="currentColor" viewBox="0 0 24 24"><path d="M12 2C6.48 2 2 6.48 2 12s4.48 10 10 10 10-4.48 10-10S17.52 2 12 2zm1 15h-2v-2h2v2zm0-4h-2V7h2v6z"/></svg>
    有効なメールアドレスを入力してください
  </p>
</div>

<!-- Do: NumberInput（スピナーコンテナ w-7 shrink-0 / input min-w-0） -->
<div class="relative flex h-9 w-40 border border-[#c4cdd9] rounded-sm overflow-hidden bg-white focus-within:border-[#2661cf] transition-colors">
  <input type="number" value="1" class="flex-1 min-w-0 px-2 text-[12px] font-medium tracking-[0.24px] text-[#3e5062] bg-transparent outline-none caret-[#2661cf] [appearance:textfield] [&::-webkit-inner-spin-button]:appearance-none" />
  <div class="flex flex-col w-7 shrink-0 border-l border-[#c4cdd9]">
    <button type="button" aria-label="増やす" class="flex-1 flex items-center justify-center bg-white hover:bg-[#edf0f3] active:bg-[#c4cdd9] transition-colors border-b border-[#c4cdd9]">
      <svg class="w-3 h-3 text-[#3e5062]" fill="none" stroke="currentColor" viewBox="0 0 24 24" stroke-width="2.5"><path stroke-linecap="round" stroke-linejoin="round" d="M5 15l7-7 7 7"/></svg>
    </button>
    <button type="button" aria-label="減らす" class="flex-1 flex items-center justify-center bg-white hover:bg-[#edf0f3] active:bg-[#c4cdd9] transition-colors">
      <svg class="w-3 h-3 text-[#3e5062]" fill="none" stroke="currentColor" viewBox="0 0 24 24" stroke-width="2.5"><path stroke-linecap="round" stroke-linejoin="round" d="M19 9l-7 7-7-7"/></svg>
    </button>
  </div>
</div>
```

**Don't**: `<label>` 省略 / プレースホルダーのみでラベル代替 / `focus:ring-*`（→ `focus:border-[#2661cf]` のみ）/ `rounded-lg`（→ `rounded-sm`）/ `py-2 text-base`（→ `h-9 text-[12px]`）/ SelectBox でネイティブ矢印。

---

## 10. Implementation Notes
- SelectBox は単一選択の固定リスト。詳細は `Dropdown.md` を参照
- CopyTextField のコピー挙動は `Copy button.md` を参照
- エラー/警告アイコンは Lucide。エラー `w-3.5 h-3.5`
- トークン参照は `guidelines/TOKEN_GUIDE.md`。フォーム全体は `Form.md` を参照
