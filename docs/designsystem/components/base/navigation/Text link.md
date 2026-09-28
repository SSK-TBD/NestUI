# Component Guideline: Text link

> NestUI TextLink 仕様。Tailwind CSS。値は `tokens/`（色は `color.primary.*` 等）を正とし、以下の class はその適用例。

## 1. Component Identity
- **Name**: Text link
- **Variants**: タイプ `inline`（同一タブ遷移・アンカー）/ `window`（別タブ・ExternalLink アイコン）/ `download`（DL・Download アイコン）。サイズ `md` / `sm` / `xs`
- **Responsibility**:
  - する: テキスト内・テーブル行・インラインヘルプから別ページ/外部/ファイルへ遷移する
  - しない: 破壊的操作（削除・解除は Buttons の danger を使う）、アクションのトリガー（Buttons を使う）
- **Target Platform**: デスクトップ Web アプリ（業務 SaaS）

**原則**: 遷移先を明示するラベル（「ここをクリック」禁止）/ 破壊的アクションには使わない / アイコン単独リンクはツールチップを併用 / 文字量の多い業務画面でも視認性を確保。

---

## 2. Variants & States

**サイズ**

| サイズ | font-size | letter-spacing | 用途 |
|--------|-----------|----------------|------|
| md | `text-[14px]` | `tracking-[0.28px]`（2%） | 標準テキスト・本文中 |
| sm | `text-[12px]` | `tracking-[0.24px]`（2%） | テーブル行・コンパクト UI（標準） |
| xs | `text-[11px]` | `tracking-[0.22px]`（2%） | 補足・メタ情報 |

**タイプ**

| タイプ | アイコン | 用途 |
|--------|----------|------|
| inline | なし | 同一タブ内ページ遷移・アンカー |
| window | ExternalLink（`w-3 h-3`） | 別タブで開くページ |
| download | Download（`w-3 h-3`） | ファイルダウンロード（`download` 属性） |

**States**

| 状態 | class | トークン |
|------|-------|---------|
| Default | `text-primary-700` | `color.primary.700` |
| Hover | `hover:text-primary-800` | `color.primary.800` |
| Press / Active | `active:text-primary-900` | `color.primary.900` |
| Disabled | `text-[#a1afc0]`（`<span aria-disabled="true">` で代替・underline 保持） | `color.content.disabled` |

ベースクラス共通: `inline-flex items-center gap-1 font-medium leading-[1.3] underline [text-decoration-skip-ink:none] transition-colors cursor-pointer rounded-sm focus:outline-none focus-visible:ring-2 focus-visible:ring-primary-700 focus-visible:ring-offset-1`。

---

## 3. Parts / 構成

| パーツ | 役割 | 必須 | class |
|--------|------|:----:|------|
| Label | リンク先・アクションを示すテキスト。underline 必須 | Yes | `underline [text-decoration-skip-ink:none]` |
| Icon | ExternalLink（別タブ）/ Download（DL）。12×12px | No（window / download 時） | `w-3 h-3 flex-shrink-0` + `aria-hidden="true"` |

---

## 4. Composition Rules

- 許可: テキストラベル + 末尾アイコン（ExternalLink / Download）
- 禁止: underline なし（リンクと判別不能）、アイコンのみのリンク（`aria-label` なし）、`<button>` でのページ遷移（href なし）、破壊的アクション
- window タイプはラベル末尾に `<span class="sr-only">（新しいタブで開く）</span>` を追加

---

## 5. Layout & Spacing

- `inline-flex items-center gap-1`（テキストとアイコンの間 4px）
- 行高 `leading-[1.3]`、字間はサイズ別（2%）
- アイコンは 12×12px（`w-3 h-3`）

---

## 6. Token Mapping

| 用途 | class | トークン |
|------|-------|---------|
| Default テキスト | `text-primary-700` | `color.primary.700` |
| Hover テキスト | `text-primary-800` | `color.primary.800` |
| Press テキスト | `text-primary-900` | `color.primary.900` |
| Disabled テキスト | `text-[#a1afc0]` | `color.content.disabled` |
| focus ring | `ring-primary-700` | `color.border.focus` |

---

## 7. Accessibility
- `<a href="...">` を使用（`<button>` は代替ページ遷移がない場合のみ）
- 別タブは `target="_blank" rel="noopener noreferrer"` 必須 + ラベル末尾に `<span class="sr-only">（新しいタブで開く）</span>`
- アイコンは `aria-hidden="true"`、Disabled は `<span aria-disabled="true">` で代替（`<a>` の href を除去しない）
- フォーカスリング `focus-visible:ring-2 focus-visible:ring-primary-700 focus-visible:ring-offset-1`

## 8. Content Guidelines
- 遷移先・アクションが一目でわかるラベル（「取引先責任者の詳細を見る」）。「ここをクリック」等の曖昧表現は禁止
- 使用シーン例: テーブルのレコード名（sm/inline）/ 本文中（md/window）/ ヘルプ（sm/window）/ DL（sm/download）/ 補足（xs/inline）

## 9. Usage Do / Don't

```html
<!-- Do: md / window（別タブ・告知付き） -->
<a href="https://example.com" target="_blank" rel="noopener noreferrer"
  class="inline-flex items-center gap-1 text-[14px] font-medium tracking-[0.28px] leading-[1.3] text-primary-700 underline [text-decoration-skip-ink:none] hover:text-primary-800 active:text-primary-900 transition-colors cursor-pointer rounded-sm focus:outline-none focus-visible:ring-2 focus-visible:ring-primary-700 focus-visible:ring-offset-1">
  取引先責任者の詳細を見る
  <svg class="w-3 h-3 flex-shrink-0" viewBox="0 0 16 16" fill="none" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true"><path d="M6 2H2v12h12v-4M9 2h5v5M14 2L8 8"/></svg>
  <span class="sr-only">（新しいタブで開く）</span>
</a>

<!-- Do: sm / inline（テーブル行） -->
<a href="/records/123" class="inline-flex items-center gap-1 text-[12px] font-medium tracking-[0.24px] leading-[1.3] text-primary-700 underline [text-decoration-skip-ink:none] hover:text-primary-800 active:text-primary-900 transition-colors cursor-pointer rounded-sm focus:outline-none focus-visible:ring-2 focus-visible:ring-primary-700 focus-visible:ring-offset-1">サンプル 太郎</a>
```

**Don't**: 「ここをクリック」等の曖昧ラベル（→具体的表現）/ 削除・解除等の破壊的アクション（→danger Button）/ アイコンのみのリンクで `aria-label` なし / `text-blue-500` 等のハードコードカラー（→`text-primary-700`）/ underline なし / `<button>` でのページ遷移（→`<a href>`）。

---

## 10. Implementation Notes
- アイコンは Lucide（ExternalLink / Download を 12×12px に縮小）
- 他コンポーネント内での使用: Breadcrumbs のリンク、Navbars の BreadCrumb、Pagination の件数近傍などで流用
