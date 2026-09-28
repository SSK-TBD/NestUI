# Component Guideline: Segmented button

> NestUI Segmented Button 仕様。Tailwind CSS。値は `tokens/`（色は `color.primary.*` 等）を正とし、以下の class はその適用例。

## 1. Component Identity
- **Name**: Segmented button
- **Variants**: 状態のみ（`Default` / `Selected` / `Focus` / `Disabled`）。サイズは現状 `Small`（28px）固定
- **Responsibility**:
  - する: 複数の選択肢から1つを排他選択する。フィルター・表示切替・ビュー切替に使う
  - しない: コンテンツ切替（Tabs を使う）、フォーム入力（Radio / Dropdown を使う）、ページ遷移
- **Target Platform**: デスクトップ Web アプリ（業務 SaaS）

**原則**: 排他選択（同時選択は必ず1つ・ラジオのセグメント版）/ 2〜6項目（それ以上は Tabs または Dropdown）/ 全項目を視覚化（折り返し・スクロールなし）/ ラベルは名詞・短文（動詞禁止）/ ヘッダー・ツールバー等の省スペース箇所に配置。

---

## 2. Variants & States

Container: `inline-flex h-7 border border-[#c4cdd9] rounded-sm overflow-hidden`。
Item 共通: `h-full px-2 text-[12px] font-medium tracking-[0.24px] whitespace-nowrap cursor-pointer relative focus:outline-none focus-visible:ring-2 focus-visible:ring-inset focus-visible:ring-primary-700 transition-colors`（最終アイテム以外は `border-r border-[#c4cdd9]`）。

**Item 状態**

| 状態 | 背景 | テキスト | 備考 |
|------|------|----------|------|
| Default | `bg-white` | `text-[#5a6c7f]` | 未選択の通常状態 |
| Selected | `bg-primary-50` | `text-primary-700` | 選択中。`aria-pressed="true"`（または `aria-checked="true"`） |
| Focus | Default と同じ | Default と同じ | フォーカスリング（`focus-visible:ring-2 focus-visible:ring-inset focus-visible:ring-primary-700`） |
| Disabled | `bg-white` | `text-[#a1afc0]` | 操作不可。Container 全体に適用（`aria-disabled="true" opacity-50` + 各 button に `disabled`） |

---

## 3. サイズ

現バージョンは Small 固定（ヘッダー/ツールバー文脈に合わせた寸法）。

| サイズ | 高さ | 横 padding | フォント | tracking |
|--------|------|------------|----------|----------|
| Small | `h-7`（28px） | `px-2`（8px） | `text-[12px] font-medium` | `tracking-[0.24px]` |

---

## 4. Composition Rules

| パーツ | 役割 | 必須 |
|--------|------|:----:|
| Container | 全アイテムをまとめる外枠（境界線・角丸・overflow を定義） | Yes |
| Item | 選択肢の単位（`<button type="button">`） | Yes（2個以上） |
| Label | アイテムの選択肢名 | Yes |

- 既に選択中のアイテムをクリックしても何も起きない（選択解除不可・常に1つ以上が Selected）
- ページ内に複数配置する場合は各々に明確なコンテキストを持たせる
- Tabs と同一文脈に混在させない（Tabs=コンテンツ切替、Segmented=表示モード切替）
- フォーム内では使わない（選択肢は Radio / Dropdown）

---

## 5. Layout & Spacing

- Container 高さ `h-7`、Item 横 padding `px-2`
- アイテム間の区切りは `border-r border-[#c4cdd9]`（最終アイテムは付けない）
- 角丸は Container 側の `rounded-sm` + `overflow-hidden` で表現

---

## 6. Token Mapping

| 用途 | class（Hex） | トークン |
|------|------|---------|
| Container ボーダー / 区切り | `border-[#c4cdd9]` | `color.border.default` |
| Default 背景 / テキスト | `bg-white` / `text-[#5a6c7f]` | `color.surface.raised` / `color.content.secondary` |
| Selected 背景 / テキスト | `bg-primary-50` / `text-primary-700` | `color.primary.50` / `.700` |
| Disabled テキスト | `text-[#a1afc0]` | `color.content.disabled` |
| focus ring | `ring-primary-700` | `color.border.focus` |

---

## 7. Accessibility
- Container に `role="group"` + `aria-label`（または `aria-labelledby`）
- 各 Item は `<button type="button">`、選択状態を `aria-pressed="true/false"` で伝える
- 選択状態はテキスト・背景色・テキスト色の3点で同時表現（色のみに依存しない）
- フォーカス時は必ず視覚インジケーター（`focus-visible:ring-2`）。Disabled は `disabled` 属性でキーボードからも除外
- コントラスト: Default `#5a6c7f`/白 = 4.6:1、Selected `#2661cf`/`#f0f7fe` = 3.1:1（UI Component 基準 3:1 以上）

## 8. Content Guidelines
- ラベルは端的な名詞・副詞（「月次」「週次」「全員」「今日」「今週」「今月」）。動詞は使わない
- 項目数は2〜6個

## 9. Usage Do / Don't

```html
<!-- Do: 3項目（Selected + Default ×2） -->
<div role="group" aria-label="表示切替" class="inline-flex h-7 border border-[#c4cdd9] rounded-sm overflow-hidden">
  <button type="button" aria-pressed="true"
    class="h-full px-2 text-[12px] font-medium tracking-[0.24px] text-primary-700 bg-primary-50 border-r border-[#c4cdd9] whitespace-nowrap cursor-pointer relative focus:outline-none focus-visible:ring-2 focus-visible:ring-inset focus-visible:ring-primary-700 transition-colors">週次</button>
  <button type="button" aria-pressed="false"
    class="h-full px-2 text-[12px] font-medium tracking-[0.24px] text-[#5a6c7f] bg-white border-r border-[#c4cdd9] whitespace-nowrap cursor-pointer relative focus:outline-none focus-visible:ring-2 focus-visible:ring-inset focus-visible:ring-primary-700 transition-colors">月次</button>
  <button type="button" aria-pressed="false"
    class="h-full px-2 text-[12px] font-medium tracking-[0.24px] text-[#5a6c7f] bg-white whitespace-nowrap cursor-pointer relative focus:outline-none focus-visible:ring-2 focus-visible:ring-inset focus-visible:ring-primary-700 transition-colors">年次</button>
</div>
```

**Don't**: 7項目以上の選択肢（最大6項目）/ 選択状態の解除（常に1つ以上が Selected）/ フォーム入力としての使用（→フィルター・表示切替専用）/ Tabs との混在使用 / `aria-pressed` の省略。

---

## 10. Implementation Notes
- クリック時に全 button の `aria-pressed` と背景/テキストクラス（`bg-primary-50` / `text-primary-700` ⇔ `bg-white` / `text-[#5a6c7f]`）をトグルする JS で実装
- キーボードは Tab で Container にフォーカス、`role="radiogroup"` 実装時は `←` `→` で項目間移動
