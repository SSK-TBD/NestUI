# Component Guideline: Dropdown

> NestUI Select / Dropdown 仕様。Tailwind CSS。値は `tokens/`（色は `color.border.*` / `color.primary.*` 等）を正とし、以下の class はその適用例。

## 1. Component Identity
- **Name**: Dropdown（= Select。固定リストからの単一選択）
- **Variants**: ネイティブ `<select>` / プレフィックスアイコン付き / グループ付き（`<optgroup>`）
- **Responsibility**:
  - する: 5つ以上の固定選択肢から1つを選ぶ。送信で確定する
  - しない: 4つ以下の選択肢（→ Radio）、フリーテキスト入力（→ Text field）、即時フィルタ（→ Filter Group。確定ボタンが要る場合は明示）
- **Target Platform**: デスクトップ Web アプリ（業務 SaaS）

**原則**: 選択肢が多いときの第一選択（5つ以上はDropdown / 4つ以下はRadio）/ 特別なUIが不要ならネイティブ `<select>` 要素を優先しOS操作性を活かす（**ただし矢印だけは `appearance: none` で消してカスタムシェブロンを重ねる** → §3）/ プレースホルダーで何を選ぶか明示 / 選択変更だけで遷移・送信しない（確定操作が必要）。

---

## 2. Variants & States

**Variants**

| Variant | 説明 | 用途 |
|---------|------|------|
| ネイティブセレクト | `<select>` 要素を使用（**矢印は `appearance: none` で消し、カスタムシェブロンを重ねる**。素の `<select>` のままにしない） | 標準フォーム（推奨） |
| プレフィックスアイコン付き | 左端にアイコンを配置 | カテゴリ選択・国選択 |
| グループ付き | `<optgroup>` で選択肢をグループ化 | 大量の選択肢 |

> 選択肢が20を超える場合は検索機能付きのカスタムセレクトを検討する。ネイティブでも `<optgroup>` でグループ化して探しやすくする。

**States**

| State | ボーダー | 背景 | テキスト |
|-------|----------|------|----------|
| Enable | `border-[#c4cdd9]` | `bg-white` | `text-[#3e5062]` |
| Hover | `border-[#c4cdd9]` | `bg-[#f7f9fb]` | `text-[#3e5062]` |
| Focus | `focus:border-[#2661cf]` | `bg-white` | `text-[#3e5062]`（ring なし・ボーダー変化のみ） |
| Error | `border-[#e93766]` | `bg-white` | `text-[#3e5062]` + `aria-invalid="true"` |
| Warning | `border-[#ee8c29]` | `bg-white` | `text-[#3e5062]` |
| Disable | `border-[#c4cdd9]` | `bg-[#f7f9fb]` | `text-[#a1afc0]` + `disabled` |

---

## 3. サイズ / Anatomy

| サイズ | 高さ | class | 用途 |
|--------|------|-------|------|
| Middle（既定） | 36px | `h-[36px] pl-[8px] pr-8 text-[12px]` | 標準フォーム |
| Small | 28px | `h-[28px] pl-[8px] pr-8 text-[12px]` | テーブル内・密なレイアウト |

共通: `w-full appearance-none font-medium tracking-[0.24px] border rounded-[2px] outline-none transition-colors cursor-pointer`。

**Anatomy**: Label（`<label>`）+ Required マーク（`*`）+ Select Container + Leading Icon（任意）+ Selected Value + Chevron + Option / Option Group + Helper Text / Error Text。

> **必須**: `appearance-none` + `pr-8` + カスタム SVG シェブロン（`absolute right-2 top-1/2 -translate-y-1/2 w-5 h-5`）は必須。ネイティブ矢印はブラウザ間で位置・余白が不安定。

---

## 4. Composition Rules

許可: `<option>` / `<optgroup>`（グループ見出し）/ 左端 Leading アイコン（`<svg>`）/ 上部 `<label>` / 下部 Helper・Error テキスト。
禁止: `<option value="">` をプレースホルダーとして送信可能にすること / `<label>` の省略 / 選択変更だけでのページ遷移・フォーム送信。
Anti-pattern: 選択肢が4個以下で Dropdown を使う（→ Radio。可視性が高い）/ 50個以上を検索なしで出す（探せない → 検索付きカスタムセレクト）。

---

## 5. Layout & Spacing

- 高さ: Middle 36px / Small 28px。左 `pl-[8px]`、右はシェブロン用に `pr-8`
- Label と Container の間: `gap-[4px]`（`flex flex-col`）
- Helper / Error テキストは Container 直下
- 角丸 `rounded-[2px]`（`rounded-lg` 禁止）

**シェブロンの寸法（Tailwind を使わない実装はこの実値を使う）**

| 項目 | Tailwind | 実値 | トークン |
|---|---|---|---|
| シェブロン右余白（枠線内側 → アイコン右端） | `right-2` | 8px | `spacing.2` |
| シェブロンの一辺 | `w-5 h-5` | 20px | `spacing.5` |
| `<select>` の右パディング | `pr-8` | 32px | `spacing.8` |
| 縦位置 | `top-1/2 -translate-y-1/2` | 中央 | — |

> 右パディング 32px = 右余白 8px + アイコン 20px + テキストとの逃げ 4px。**この 3 つは連動する**ので、片方だけ変えない。右余白が 0 に見える実装は、ほぼ `appearance: none` の付け忘れ（ネイティブ矢印が枠に張り付いている）。

---

## 6. Token Mapping

| 用途 | トークン |
|------|---------|
| ボーダー（標準） | `color.border.default`（#c4cdd9） |
| ボーダー（フォーカス） | `color.border.focus`（#2661cf = primary-700） |
| 背景 / hover | `color.surface.input`(#fff) / `color.surface.base`(#f7f9fb) |
| テキスト | `#3e5062`（`color.content.secondary` 近傍） |
| エラーボーダー | `color.semantic.error`(#e93766) |
| 警告ボーダー | `color.semantic.warning`(#ee8c29) |
| disabled テキスト / 背景 | `color.content.disabled`(#a1afc0) / `color.surface.base` |
| Required マーク | `color.semantic.error`(#e93766) |
| 角丸 | `radius.sm`（→ `rounded-[2px]` で適用） |
| シェブロン色 | `color.content.secondary`(#5a6c7f) / disabled 時は `color.content.disabled`(#a1afc0) |
| シェブロン右余白 / 一辺 / 右パディング | `spacing.2`(8px) / `spacing.5`(20px) / `spacing.8`(32px) |

---

## 7. Accessibility
- `<label for="id">` と `<select id="id">` を一致させる（ラベルなし禁止）
- 必須: `required` + `aria-required="true"` + 視覚的 `*`
- エラー: テキストに `id` + `role="alert"`、`<select>` に `aria-describedby` と `aria-invalid="true"`
- Disabled: `disabled` 属性
- キーボード: Tab フォーカス・Space/Enter で開閉・矢印キーで選択肢移動・Home/End・先頭一致ジャンプ（ネイティブ標準）
- コントラスト: ラベル・選択テキスト 4.5:1 以上

## 8. Content Guidelines
- プレースホルダーは「選択してください」または「[項目名]を選択」
- ラベルは何を選ぶかを端的に。Helper は1行で制約や用途を補足

## 9. Usage Do / Don't

```html
<!-- Do: 必須 + ヘルパー（appearance-none + カスタム chevron） -->
<div class="flex flex-col gap-[4px] items-start">
  <div class="leading-normal text-[12px] font-medium tracking-[0.24px]" style="color:#3e5062;">
    役割<span class="text-[#e93766] ml-0.5">*</span>
  </div>
  <div class="relative w-full">
    <select id="select-role" name="role" required aria-required="true" aria-describedby="select-role-helper"
      class="w-full h-[36px] appearance-none pl-[8px] pr-8 text-[12px] font-medium tracking-[0.24px] border border-[#c4cdd9] rounded-[2px] bg-white outline-none focus:border-[#2661cf] hover:bg-[#f7f9fb] transition-colors cursor-pointer" style="color:#3e5062;">
      <option value="" disabled selected>選択してください</option>
      <option value="admin">管理者</option>
      <option value="editor">編集者</option>
    </select>
    <svg class="pointer-events-none absolute right-2 top-1/2 -translate-y-1/2 w-5 h-5" style="color:#3e5062;" fill="none" stroke="currentColor" viewBox="0 0 24 24" stroke-width="1.5">
      <path stroke-linecap="round" stroke-linejoin="round" d="M19 9l-7 7-7-7"/>
    </svg>
  </div>
  <p id="select-role-helper" class="text-[11px] text-[#5a6c7f]">プロジェクト内での権限を決定します</p>
</div>

<!-- Do: グループ付き（optgroup） -->
<select class="w-full h-[36px] appearance-none pl-[8px] pr-8 text-[12px] font-medium tracking-[0.24px] border border-[#c4cdd9] rounded-[2px] bg-white outline-none focus:border-[#2661cf] hover:bg-[#f7f9fb] cursor-pointer" style="color:#3e5062;">
  <option value="" disabled selected>選択してください</option>
  <optgroup label="関東"><option>東京</option><option>神奈川</option></optgroup>
  <optgroup label="関西"><option>大阪</option><option>京都</option></optgroup>
</select>
```

**Don't**: ネイティブ矢印表示（→ `appearance-none` + カスタム SVG）/ `rounded-lg`（→ `rounded-[2px]`）/ `focus:ring-*`（→ `focus:border-[#2661cf]` のみ）/ `<label>` 省略 / `py-2 text-base`（→ `h-[36px] text-[12px]`）/ 選択変更だけで遷移・送信。

---

## 10. Implementation Notes
- アイコンは Lucide（chevron-down）。エラーアイコンは circle-alert（`w-3.5 h-3.5`）
- カスタムドロップダウン（検索付き）にする場合は Combobox 相当のキーボード・ARIA を満たす（Trigger `role="combobox"` / List `role="listbox"` / 各項目 `role="option"` `aria-selected`）
- トークン参照は `guidelines/TOKEN_GUIDE.md`。フォーム全体のレイアウトは `Form.md` を参照
