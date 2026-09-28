# Component Guideline: Radio

> NestUI Radio Button 仕様。Tailwind CSS。値は `tokens/`（色は `color.primary.*` 等）を正とし、以下の class / CSS はその適用例。`appearance: none` でネイティブ表示を完全リセットし、ボーダー色変更 + 内側ドットで選択を表す。

## 1. Component Identity
- **Name**: Radio
- **Variants**: 縦並び / 横並び / 説明付き（カードスタイル）
- **Responsibility**:
  - する: 選択肢群から排他的に1つを選ぶ。全選択肢を常時表示して比較させる
  - しない: 複数選択（→ Checkbox）、選択肢が多い（→ Dropdown）、ON/OFF の2値（→ Checkbox / Toggle）
- **Target Platform**: デスクトップ Web アプリ（業務 SaaS）

**原則**: 選択肢は2〜5つ / 全選択肢を同時表示して比較可能に / 切り替えだけでは確定しない（送信で反映）/ 原則1つを初期選択にする / 単一の Radio は使わない（2つ以上必須。ON/OFF は Checkbox か Toggle）。

---

## 2. Variants & States

**Variants**

| Variant | 説明 | 用途 |
|---------|------|------|
| 縦並び | 標準。選択肢を縦に並べる | 支払い方法など |
| 横並び | 2〜3つで短いラベル | 性別など |
| 説明付き（カード） | 各選択肢に補足説明 + `has-[:checked]` で選択強調 | プラン選択など |

**States**

| State | 変化 |
|-------|------|
| Unselected | `border: 2px solid #cbd5e1`（≒ `color.border.default`）/ `bg-white` |
| Selected | `border-color: #2b70ef`（primary-500）+ 内側ドット `::after`（`bg #2b70ef`） |
| Focus | `box-shadow: 0 0 0 2px rgba(59,130,246,0.3)`（`focus-visible:ring-2 ring-primary-700 ring-offset-1` と等価運用） |
| Error | `border-[#e93766]` + `aria-invalid="true"`（選択時は内側ドットも `#ef4444`）+ エラーメッセージ |
| Disabled | `opacity: 0.5; cursor: not-allowed` |

> 選択解除は不可（同グループの別選択肢を選ぶまで Selected を維持）。Checkbox と異なりクリックで解除されない。

---

## 3. サイズ / Anatomy

| 項目 | 値 |
|------|-----|
| Radio Circle | `1.125rem`（18px）`border-radius: 9999px` |
| 内側ドット | `0.5rem`（8px）`border-radius: 9999px` |
| Circle-ラベル gap | `gap-2`（8px） |
| ラベル | `text-sm`（行 + 説明含む div は `leading-normal`） |

**Anatomy**: Radio Circle + Selected Indicator（内側ドット）+ Label + Group Label（`<legend>`）+ Description（任意）+ Helper / Error Text。

---

## 4. Composition Rules

許可: テキストノード（ラベル）/ Description / グループは `<fieldset>` + `<legend>`。
禁止: `<fieldset>` ラッパーの省略（ラジオグループに必須）/ `<label>` の省略 / 単一の Radio。
配置: レイアウトは選択肢数と文字数に合わせる（縦並び推奨、3つ以下で短ければ横並びも可）。同一 `name` が1グループとして動作。

---

## 5. Layout & Spacing

- 縦並び: `flex flex-col gap-4`
- 横並び: `flex flex-wrap gap-6`
- 説明付きカード: `space-y-3`、各カード `p-4 border border-slate-200 rounded-lg`、説明含む div に `leading-normal`
- エラーメッセージ `mt-1`

---

## 6. Token Mapping

| 用途 | トークン |
|------|---------|
| 未選択ボーダー | `color.border.default`（#c4cdd9 ≒ サンプルの #cbd5e1） |
| 選択ボーダー・内側ドット | `color.primary.500`（#2b70ef） |
| 背景 | `color.surface.input`（white） |
| エラーボーダー・ドット | `color.semantic.error`(#e93766 / サンプル #ef4444) |
| focus ring | `color.border.focus`(= primary-700) |
| ラベル | `color.content.primary` / Description は `color.content.secondary` |
| Required マーク | `color.semantic.error`(#e93766) |

---

## 7. Accessibility
- `<fieldset>` + `<legend>` でグループを構成（必須）
- `<label>` を各ラジオに関連付ける（`for` or ラッピング）
- キーボード: Tab でグループにフォーカス、ArrowUp/Left で前・ArrowDown/Right で次へ移動して選択、Space で選択
- エラー: 色だけでなくテキスト + アイコン併用。`aria-describedby` で `<fieldset>` に関連付け、`aria-invalid="true"`
- コントラスト: ラベル 4.5:1 以上、Circle（ボーダー/背景）3:1 以上。Disabled はキーボードからも操作不可

## 8. Content Guidelines
- グループ見出し（`<legend>`）で何を選ぶか明示
- ラベルは選択肢を端的に。Description はプラン差分など比較情報を1〜2行で

## 9. Usage Do / Don't

```html
<!-- Do: 基本（縦並び） -->
<fieldset>
  <legend class="text-sm font-medium text-slate-700 mb-3">お支払い方法</legend>
  <div class="flex flex-col gap-4">
    <label class="flex items-center gap-2 cursor-pointer">
      <input type="radio" name="payment" value="credit" checked
        class="text-primary-500 border-slate-300 focus-visible:ring-2 focus-visible:ring-primary-700 focus-visible:ring-offset-1">
      <span class="text-sm text-slate-700">クレジットカード</span>
    </label>
    <label class="flex items-center gap-2 cursor-pointer">
      <input type="radio" name="payment" value="bank"
        class="text-primary-500 border-slate-300 focus-visible:ring-2 focus-visible:ring-primary-700 focus-visible:ring-offset-1">
      <span class="text-sm text-slate-700">銀行振込</span>
    </label>
  </div>
</fieldset>

<!-- Do: 説明付き（カードスタイル・has-[:checked] で選択強調） -->
<label class="flex items-start gap-3 cursor-pointer p-4 border border-slate-200 rounded-lg hover:bg-gray-50 has-[:checked]:border-primary-500 has-[:checked]:bg-primary-50 transition-colors">
  <input type="radio" name="plan" value="pro"
    class="mt-[3px] text-primary-500 border-slate-300 focus-visible:ring-2 focus-visible:ring-primary-700 focus-visible:ring-offset-1 flex-shrink-0">
  <div class="leading-normal">
    <span class="text-sm font-medium text-slate-900">プロ</span>
    <p class="text-sm text-body mt-0.5">全機能が利用可能。月額 &yen;980</p>
  </div>
</label>
```

**必須カスタム CSS**（ネイティブ表示の完全リセット）:

```css
input[type="radio"]{ -webkit-appearance:none; appearance:none; width:1.125rem; height:1.125rem;
  border:2px solid #cbd5e1; border-radius:9999px; background:#fff; outline:none!important; box-shadow:none!important;
  cursor:pointer; flex-shrink:0; position:relative; }
input[type="radio"]:checked{ border-color:#2b70ef; }
input[type="radio"]:checked::after{ content:''; position:absolute; top:50%; left:50%; transform:translate(-50%,-50%);
  width:.5rem; height:.5rem; border-radius:9999px; background:#2b70ef; }
input[type="radio"]:focus{ box-shadow:0 0 0 2px rgba(59,130,246,.3)!important; }
input[type="radio"]:disabled{ opacity:.5; cursor:not-allowed; }
input[type="radio"][aria-invalid="true"]{ border-color:#ef4444; }
input[type="radio"][aria-invalid="true"]:checked::after{ background:#ef4444; }
```

**Don't**: `<fieldset>` ラッパー省略 / `<label>` 省略 / `border-red-500`・`text-red-500`（→ `#e93766`）/ `focus:ring-primary-500/50`（→ `focus-visible:ring-primary-700 ring-offset-1`）/ 単一の Radio 使用。

---

## 10. Implementation Notes
- `appearance: none` で完全リセットし、選択表示（ボーダー色 + 内側ドット）はすべて CSS で制御
- エラーアイコンは circle-alert（Lucide `w-3.5 h-3.5`）
- トークン参照は `guidelines/TOKEN_GUIDE.md`。フォーム全体は `Form.md` を参照
