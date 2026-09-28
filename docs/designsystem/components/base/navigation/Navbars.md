# Component Guideline: Navbars

> NestUI Page Header 仕様。Tailwind CSS。値は `tokens/`（色は `color.primary.*` 等）を正とし、以下の class はその適用例。
> NestUI での実体は「ページ単位の見出し」= Breadcrumbs + Title + Status tag を組み合わせた複合コンポーネント。

## 1. Component Identity
- **Name**: Navbars（Page Header）
- **Variants**: レベル `H1`（ページ）/ `H2`（セクション）/ `H3`（サブセクション）。オプションで BreadCrumb・Status tag を付与
- **Responsibility**:
  - する: 現在地（Breadcrumbs）+ 見出し（Title）でページ文脈を即座に伝える。3レベルの見出し体系を一貫して維持する
  - しない: ページ内コンテンツの切り替え（Tabs を使う）、グローバルナビ（Side nav を使う）
- **Target Platform**: デスクトップ Web アプリ（業務 SaaS）

**原則**: ページ構造を明示（BreadCrumb で現在地 + Title で見出し）/ 3レベル階層（H1/H2/H3）を一貫運用 / BreadCrumb と Status は任意付与、最小構成はタイトル単体 / 見出しレベルに応じて `<h1>` / `<h2>` / `<h3>` を使う。

---

## 2. Variants & States

**レベル（Title）**

| レベル | 用途 | class |
|--------|------|------|
| H1 | ページ見出し | `text-[21px] font-bold text-body leading-[1.5] tracking-[0.63px] whitespace-nowrap` |
| H2 | セクション見出し | `text-[16px] font-bold text-body leading-[1.5] tracking-[0.48px] whitespace-nowrap` |
| H3 | サブセクション見出し | `text-[14px] font-bold text-body leading-[1.5] whitespace-nowrap` |

**オプション**

| オプション | 説明 |
|-----------|------|
| BreadCrumb あり | 上位階層へのリンク + 現在ページ名 |
| BreadCrumb なし | タイトル単体（トップレベルページ等） |
| Status あり | タイトル右に Status tag を表示 |

Status tag は静的表示（状態は対象オブジェクトに依存）。

---

## 3. Parts / 構成

| パーツ | 役割 | 必須 | class |
|--------|------|:----:|------|
| ラッパー | 全体コンテナ | Yes | `flex flex-col gap-[4px] items-start` |
| BreadCrumb | 現在位置の階層パス（→Breadcrumbs） | No | `<nav aria-label="パンくずリスト">` + `<ol class="flex items-center gap-0.5">` |
| タイトル行 | Title + Status | Yes | `flex items-center gap-2` |
| Title | ページ/セクション/サブセクション見出し | Yes | `<h1>` / `<h2>` / `<h3>`（上記レベル class） |
| Status Tag | オブジェクトのステータス表示（→Status tag） | No | `inline-flex items-center gap-1.5 px-1.5 py-0.5 bg-[#d5f6e6] rounded-[4px]`（ドット `w-2 h-2 rounded-full bg-[#5eb62c]` + テキスト `text-[11px] text-[#376f1c]`） |

---

## 4. Composition Rules

- 許可: BreadCrumb（Breadcrumbs）、見出しテキスト、Status tag
- 禁止: `<div>` で見出しを代替（セマンティクス欠如）、H1 をページに複数配置、BreadCrumb の現在ページをリンクにする、Status を色だけで伝える
- BreadCrumb の実装規約は Breadcrumbs に準拠（Chevron-right セパレータ・現在ページは `<span>`）

---

## 5. Layout & Spacing

- ラッパーは縦並び `flex-col gap-[4px] items-start`（BreadCrumb → タイトル行）
- タイトル行は `flex items-center gap-2`（Title と Status を横並び）
- H1 字間 `tracking-[0.63px]`、H2 `tracking-[0.48px]`

---

## 6. Token Mapping

| 用途 | class（Hex） | トークン |
|------|------|---------|
| Title テキスト | `text-body`（#081a27） | `color.content.primary` |
| BreadCrumb リンク | `text-primary-700` | `color.primary.700` |
| BreadCrumb リンク hover | `text-primary-600` | `color.primary.600` |
| BreadCrumb 現在ページ | `text-[#3e5062]` | `color.content.secondary` 近傍 |
| BreadCrumb セパレータ | `text-slate-400` | `color.content.disabled` 近傍 |
| Status 背景 / ドット / テキスト | `bg-[#d5f6e6]` / `bg-[#5eb62c]` / `text-[#376f1c]` | `color.status.success.container` / `.fill` / `.text` |

---

## 7. Accessibility
- 見出しはレベルに応じて `<h1>` / `<h2>` / `<h3>`。`<h1>` はページに1つ
- BreadCrumb 外枠は `<nav aria-label="パンくずリスト">`、現在ページに `aria-current="page"`
- Status は色だけで状態を伝えず、テキストを必ず併用

## 8. Content Guidelines
- Title は対象の正式名称（「案件」「基本情報」「担当者情報」）
- BreadCrumb の最後（現在ページ）はリンクなし。Status ラベルは状態名（「契約中」等）

## 9. Usage Do / Don't

```html
<!-- Do: H1 + BreadCrumb + Status -->
<div class="flex flex-col gap-[4px] items-start">
  <nav aria-label="パンくずリスト">
    <ol class="flex items-center gap-0.5">
      <li class="flex items-center gap-1">
        <a href="#" class="text-[14px] font-medium text-primary-700 underline hover:text-primary-600 leading-[1.3] tracking-[0.28px] transition-colors">ホーム</a>
        <svg class="w-4 h-4 flex-shrink-0 text-slate-400" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><polyline points="9 18 15 12 9 6"/></svg>
      </li>
      <li><span class="text-[14px] font-medium text-[#3e5062] leading-[1.3] tracking-[0.28px]" aria-current="page">案件</span></li>
    </ol>
  </nav>
  <div class="flex items-center gap-2">
    <h1 class="text-[21px] font-bold text-body leading-[1.5] tracking-[0.63px] whitespace-nowrap">案件</h1>
    <div class="inline-flex items-center gap-1.5 px-1.5 py-0.5 bg-[#d5f6e6] rounded-[4px]">
      <span class="w-2 h-2 rounded-full bg-[#5eb62c] flex-shrink-0"></span>
      <span class="text-[11px] font-normal text-[#376f1c] leading-[1.75] whitespace-nowrap">契約中</span>
    </div>
  </div>
</div>
```

**Don't**: `<div>` で見出しを代替（→`<h1/2/3>`）/ H1 をページに複数配置 / BreadCrumb の現在ページをリンクにする（→`aria-current="page"`）/ Status の色だけで状態を伝える（→アイコン or テキスト併用）/ `text-black` `text-slate-900` を見出しに使う（→`text-body`）。

---

## 11. Implementation Notes
- BreadCrumb は Breadcrumbs コンポーネントを流用、Status は Status tag を流用
- ページ最上部に配置し、その下に Tabs や本文を続ける構成が標準
