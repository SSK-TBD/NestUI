# Component Guideline: Breadcrumbs

> NestUI Breadcrumb 仕様。Tailwind CSS。値は `tokens/`（色は `color.primary.*` 等）を正とし、以下の class はその適用例。

## 1. Component Identity
- **Name**: Breadcrumbs
- **Variants**: `default`（1階層 / 2〜3階層）
- **Responsibility**:
  - する: 現在ページがサイト構造のどこに位置するかを示す。上位階層へ素早く移動できる
  - しない: フラットな1階層のみのナビ、タブ切り替え（Tabs を使う）
- **Target Platform**: デスクトップ Web アプリ（業務 SaaS）

**原則**: 階層構造を可視化 / `<nav>` + `<ol>` でセマンティックに構造化 / セパレータは Chevron-right SVG のみ（`/` スラッシュ禁止）。

---

## 2. Variants & States

**Variants**

| 階層数 | 説明 |
|--------|------|
| 1階層 | リンクのみ（Chevron なし） |
| 2〜3階層 | Text link × N + Chevron-right セパレータ + 現在ページ（リンクなし）。3階層を推奨、4以上は省略表示を検討 |

**States**

| State | 変化 |
|-------|------|
| Default（リンク） | `text-primary-700 underline` |
| Hover（リンク） | `hover:text-primary-600 transition-colors` |
| 現在ページ | `text-[#3e5062]`（リンクにしない・`<span>` + `aria-current="page"`） |

---

## 3. Parts / 構成

| パーツ | 役割 | 必須 | class |
|--------|------|:----:|------|
| Nav Container | `<nav aria-label="パンくずリスト">` | Yes | — |
| Ordered List | `<ol>` でリスト構造を明示 | Yes | `flex items-center gap-0.5` |
| Link Item | 上位階層へのリンク `<li>` | Yes | `flex items-center gap-1` |
| Link Text | リンクテキスト | Yes | `text-[14px] font-medium text-primary-700 underline hover:text-primary-600 leading-[1.3] tracking-[0.28px] transition-colors` |
| Chevron-right | Lucide SVG セパレータ | Yes | `w-4 h-4 flex-shrink-0 text-slate-400` |
| Current Page | 現在ページ名（リンクなし） | Yes | `text-[14px] font-medium text-[#3e5062] leading-[1.3] tracking-[0.28px]` + `aria-current="page"` |

---

## 4. Composition Rules

- 許可: テキストノード、リンク（`<a>`）、Chevron-right SVG
- 禁止: `<button>`（リンク以外のアクションを置かない）/ フォーム要素 / `/` スラッシュ区切り
- 現在ページ（最後のアイテム）は `<span>` でリンクにしない

---

## 5. Layout & Spacing

- `<ol>` は `gap-0.5`、リンク項目内（テキスト + Chevron）は `gap-1`
- Chevron は `w-4 h-4`、リンク行高 `leading-[1.3]`、字間 `tracking-[0.28px]`（2%）
- 長いラベルは省略（`text-overflow: ellipsis`）

---

## 6. Token Mapping

| 用途 | class | トークン |
|------|-------|---------|
| リンクテキスト | `text-primary-700` | `color.primary.700` |
| リンク hover | `text-primary-600` | `color.primary.600` |
| 現在ページテキスト | `text-[#3e5062]` | `color.content.secondary` 近傍 |
| セパレータ | `text-slate-400` | `color.content.disabled` 近傍 |

---

## 7. Accessibility
- `<nav aria-label="パンくずリスト">` + `<ol>` で構造を明示
- 現在ページに `aria-current="page"`（リンクにしない・`<span>` 使用）
- キーボード: `Tab` でリンク間移動、`Enter` で遷移

## 8. Content Guidelines
- ラベルはページ/セクションの正式名称（略称は避ける）
- 最初は「ホーム」またはルートセクション名、最後（現在ページ）はリンクなし

## 9. Usage Do / Don't

```html
<!-- Do: 3階層（Chevron-right セパレータ + 現在ページは span） -->
<nav aria-label="パンくずリスト">
  <ol class="flex items-center gap-0.5">
    <li class="flex items-center gap-1">
      <a href="#" class="text-[14px] font-medium text-primary-700 underline hover:text-primary-600 leading-[1.3] tracking-[0.28px] transition-colors">ホーム</a>
      <svg class="w-4 h-4 flex-shrink-0 text-slate-400" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><polyline points="9 18 15 12 9 6"/></svg>
    </li>
    <li class="flex items-center gap-1">
      <a href="#" class="text-[14px] font-medium text-primary-700 underline hover:text-primary-600 leading-[1.3] tracking-[0.28px] transition-colors">設定</a>
      <svg class="w-4 h-4 flex-shrink-0 text-slate-400" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><polyline points="9 18 15 12 9 6"/></svg>
    </li>
    <li><span class="text-[14px] font-medium text-[#3e5062] leading-[1.3] tracking-[0.28px]" aria-current="page">プロフィール</span></li>
  </ol>
</nav>
```

**Don't**: `/` スラッシュをセパレータに使う（→Chevron-right SVG のみ）/ 現在ページをリンクにする（→`<span>` + `aria-current="page"`）/ `aria-current="page"` の省略 / 4階層以上の表示（→3階層推奨・以上は省略）/ `text-slate-500` 等の汎用カラーをリンク色にする（→`text-primary-700`）。

---

## 10. Implementation Notes
- セパレータは Lucide chevron-right を `<svg>` で直書き（`text-slate-400`）
- SEO 用途では `<ol>` + JSON-LD（BreadcrumbList）の併用を推奨
- Page Header（Navbars 参照）と組み合わせて使うことが多い
