# Component Guideline: Pagination

> NestUI Pagination 仕様。Tailwind CSS。値は `tokens/`（色は `color.primary.*` 等）を正とし、以下の class はその適用例。

## 1. Component Identity
- **Name**: Pagination
- **Variants**: `基本`（連続ページ番号 + prev/next）/ `Ellipsis 付き`（`1 … 4 [5] 6 … 20`）/ `件数付き` / `ページ数セレクター付き`
- **Responsibility**:
  - する: コンテンツを複数ページに分割しページ間を移動する。現在位置を明示する
  - しない: データフェッチ・ページコンテンツの管理（親の責務）、フィルター（Dropdown を使う）
- **Target Platform**: デスクトップ Web アプリ（業務 SaaS）

**原則**: ボタンは `w-8 h-8`（32px）でデスクトップ業務UIに最適化 / アクティブは `bg-primary-100 text-primary-700`（白抜きは使わない）/ ページ数が多い場合は `…` で中間省略 / 一覧画面では件数表示と「N件/ページ」セレクターを組み合わせる。

---

## 2. Variants & States

**Parts / NumItem（ページ番号ボタン）** — `w-8 h-8 inline-flex items-center justify-center relative rounded text-[14px] font-medium tracking-[0.28px]`

| 状態 | class |
|------|-------|
| Selected（アクティブ） | `bg-primary-100 text-primary-700`（`#ddedfc` / `#2661cf`）+ `aria-current="page"` |
| Enable | `text-[#3e5062] hover:bg-[#edf0f3] active:bg-primary-100 cursor-pointer` |
| Disable | `text-[#a1afc0] cursor-not-allowed`（背景なし） |
| Focus | `focus-visible:outline-none` + 絶対配置フォーカスリング（`border-2 border-primary-700 rounded inset-[-3px]`） |

**Parts / Pager（Prev/Next 矢印）** — `min-h-[32px] px-1 inline-flex items-center justify-center relative rounded`（アイコン `w-6 h-6`）

| 状態 | class |
|------|-------|
| Enable | `hover:bg-[#edf0f3] active:bg-primary-100 cursor-pointer`（アイコン `text-[#3e5062]`） |
| Disable | `cursor-not-allowed`（アイコン `text-[#a1afc0]`） + `disabled` |

**Ellipsis**: `w-8 h-8 inline-flex items-center justify-center text-[#5a6c7f] text-[14px]` + `aria-hidden="true"`

---

## 3. Parts / 構成

| パーツ | 役割 | 必須 |
|--------|------|:----:|
| Nav Container | `<nav aria-label="ページネーション">` | Yes |
| Count Label | 総件数テキスト `"N件"`（`text-[14px] font-medium text-[#5a6c7f] tracking-[0.28px] shrink-0`） | No（一覧画面では推奨） |
| Prev Button（Pager） | 前のページへ | Yes |
| Page Button（NumItem） | ページ番号ボタン | Yes |
| Next Button（Pager） | 次のページへ | Yes |
| Ellipsis | 省略記号 `…` | No |
| Per-Page Selector | 「N件 / ページ」表示数切替（Dropdown トリガー） | No（一覧画面では推奨） |

レイアウト: `flex items-center gap-1`。

---

## 4. Composition Rules

- 許可: NumItem / Pager / Ellipsis / Count Label / Per-Page Selector
- Per-Page Selector は Dropdown のフィルタートリガー（`h-8`）を流用し、ラベルは「50件 / ページ」等の固定テキスト
- 先頭ページでは Prev を `disabled`、末尾ページでは Next を `disabled`
- `totalPages` が 1 のときは Pagination を表示しない

---

## 5. Layout & Spacing

- ボタンサイズ `w-8 h-8`（32px）固定。`w-10 h-10` は使わない
- アイテム間 `gap-1`、Pager アイコン `w-6 h-6`、角丸は `rounded`（`rounded-lg` 禁止）

---

## 6. Token Mapping

| 用途 | class（Hex） | トークン |
|------|------|---------|
| アクティブ背景 / テキスト | `bg-primary-100` / `text-primary-700` | `color.primary.100` / `.700` |
| Enable テキスト | `text-[#3e5062]` | `color.content.secondary` 近傍 |
| hover 背景 / press 背景 | `bg-[#edf0f3]` / `bg-primary-100` | `color.surface.sunken` / `color.primary.100` |
| Disable テキスト | `text-[#a1afc0]` | `color.content.disabled` |
| 件数 / Ellipsis テキスト | `text-[#5a6c7f]` | `color.content.secondary` |
| Per-Page トリガー枠 | `border-[#c4cdd9] bg-[#f7f9fb]` | `color.border.default` / `color.surface.base` |
| focus ring | `border-primary-700` | `color.border.focus` |

---

## 7. Accessibility
- `<nav aria-label="ページネーション">`、アクティブページに `aria-current="page"`
- 各ボタンに `aria-label="ページ N"` / `"前のページ"` / `"次のページ"`
- 先頭/末尾で prev/next を `disabled`、Ellipsis の `<span>` に `aria-hidden="true"`
- タップ領域 `w-8 h-8`（デスクトップ業務UI基準）。ページ番号はアラビア数字のみ

## 8. Content Guidelines
- 件数表示は桁区切り付き（「14,874件」）
- Per-Page ラベルは「50件 / ページ」等の固定文言

## 9. Usage Do / Don't

```html
<!-- Do: 件数 + prev/next + アクティブページ -->
<div class="flex items-center gap-1">
  <span class="text-[14px] font-medium text-[#5a6c7f] tracking-[0.28px] shrink-0 mr-1">14,874件</span>
  <nav aria-label="ページネーション">
    <ul class="flex items-center gap-1">
      <li><button disabled class="min-h-[32px] px-1 inline-flex items-center justify-center rounded cursor-not-allowed" aria-label="前のページ"><svg class="w-6 h-6 text-[#a1afc0]">…</svg></button></li>
      <li><button class="w-8 h-8 inline-flex items-center justify-center rounded text-[14px] font-medium tracking-[0.28px] bg-primary-100 text-primary-700" aria-current="page" aria-label="ページ 1">1</button></li>
      <li><button class="w-8 h-8 inline-flex items-center justify-center rounded text-[14px] font-medium tracking-[0.28px] text-[#3e5062] hover:bg-[#edf0f3] active:bg-primary-100 cursor-pointer" aria-label="ページ 2">2</button></li>
    </ul>
  </nav>
</div>
```

**Don't**: `w-10 h-10` のボタンサイズ（→`w-8 h-8`）/ `bg-primary-500` のアクティブ色（→`bg-primary-100 text-primary-700`）/ `rounded-lg`（→`rounded`）/ `aria-label` の省略（各ページボタンに必須）/ `aria-current="page"` の省略 / `totalPages=1` で表示。

---

## 10. Implementation Notes
- Per-Page Selector は Dropdown コンポーネントのトリガー（`.filter-dropdown-wrapper`）を流用
- フォーカスは絶対配置のフォーカスリング要素で実装（NumItem / Pager 共通）
- Table の下部に配置し、ページ変更時はリスト先頭へスクロール。一覧画面では件数 + Per-Page を組で配置するのが標準
