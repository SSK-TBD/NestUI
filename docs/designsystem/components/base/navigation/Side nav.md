# Component Guideline: Side nav

> NestUI Sidebar 仕様。Tailwind CSS。値は `tokens/`（色は `color.surface.*` / `color.primary.*` 等）を正とし、以下の class はその適用例。

## 1. Component Identity
- **Name**: Side nav
- **Variants**: `expanded`（標準 ~188px）/ `collapsed`（コンパクト ~56px・アイコンのみ）
- **Responsibility**:
  - する: アプリ全ページ共通のメインナビゲーション。現在地の明示と最短到達の構造を保つ
  - しない: ページ内コンテキストの切り替え（Tabs を使う）、一時的メニュー（Popover を使う）、アクション配置（Navbars / Button を使う）
- **Target Platform**: デスクトップ Web アプリ（業務 SaaS）

**原則**: 3ゾーン構成（Header=ロゴ / Navigation=メインナビ / Footer=アカウント）/ 現在地は背景色 + テキスト色の2手がかりで示す（色のみ禁止）/ 標準↔コンパクトのトグル対応。

---

## 2. Variants & States

**Variants**

| Variant | 幅 | 説明 |
|---------|-----|------|
| `expanded`（標準・既定） | `~188px` | アイコン + テキスト表示 |
| `collapsed`（コンパクト） | `~56px` | アイコンのみ。通知はドットで表示・各アイテムに `aria-label` + `title` 必須 |

Container 共通: `<aside>` に `bg-[#edf0f3] flex-shrink-0 flex flex-col h-screen`。`bg-white` 系背景・右ボーダー（`border-r`）は禁止。

**States**（Nav Item）

| State | class |
|-------|-------|
| Default | `text-[#081a27] font-medium text-[14px] hover:bg-[#dde3eb] rounded-[4px] transition-colors` |
| Selected / Active | `bg-[#dde3eb] text-[#081a27] font-medium rounded-[4px]` + `aria-current="page"` |
| Hover | `bg-[#dde3eb]`（Selected と同色） |
| Press | `bg-[#c4cdd9]` |
| Focus | `outline-none ring-2 ring-[#2661cf] ring-offset-1 rounded-[4px]` |
| Disabled | `text-[#a1afc0] cursor-not-allowed` + `aria-disabled="true"` + `tabindex="-1"` |

---

## 3. Parts / 構成

3ゾーン（Header / Navigation / Footer）が必須。

| パーツ | 要素 | 必須 | class |
|--------|------|:----:|------|
| Container | `<aside>` | Yes | `bg-[#edf0f3] flex flex-col h-screen`（幅 188px / 56px） |
| Header | `<div>` | Yes | `h-[68px] pl-[12px] py-[20px]`（ロゴ表示） |
| Navigation | `<nav>` | Yes | `flex-1 overflow-y-auto flex flex-col gap-[6px] pl-[12px]` + `aria-label="メインナビゲーション"` |
| Collapse Button | `<button>` | No | 折りたたみトグル（Chevrons-left/right アイコン + "折りたたむ"） |
| Section Label | `<p>` | No | `text-[12px] font-medium text-[#5a6c7f] px-[8px] pt-[4px]`（コンパクト時は `bg-[#c4cdd9] h-px` の水平線に置換） |
| Nav Item | `<a>` / `<button>` | Yes | `flex items-center gap-[6px] h-[32px] px-[8px] rounded-[4px]` |
| Nav Icon | `<svg>` | Yes | `w-[18px] h-[18px] flex-shrink-0` |
| Badge | `<span>` | No | `rounded-[2px] bg-[#2661cf]`（コンパクト時はドット） |
| Footer | `<div>` | Yes | `pb-[8px] pl-[8px]` |
| Account Card | `<button>` | Yes | `bg-[#f7f9fb] rounded-[6px] shadow-sm`（幅 172px） |

---

## 4. Badge / Account Card

**Badge**

| バリエーション | class |
|----------------|-------|
| 標準テキストバッジ | `bg-[#2661cf] text-white rounded-[2px] min-w-[20px] h-[20px] px-[4px] text-[11px] font-medium inline-flex items-center justify-center` |
| コンパクト ドット | `absolute w-[8px] h-[8px] rounded-full bg-[#2661cf] top-[4px] left-[20px]` |

**Account Card**

| 状態 | class |
|------|-------|
| Default | `bg-[#f7f9fb] shadow-sm rounded-[6px] flex items-center gap-[8px] p-[8px]` |
| Hover | `bg-[#edf0f3]` |
| Press | `bg-[#ddedfc]` |
| Focus | `ring-2 ring-[#2661cf] ring-offset-1 rounded-[6px]` |

アバター: `w-[32px] h-[32px] rounded-full bg-[#ee8c29] text-white font-medium text-[16px]`（オレンジ = ブランドアクセント）。

---

## 5. Layout & Spacing

- 幅は `~188px`（標準）/ `~56px`（コンパクト）。`w-64` / `w-60` 等の固定幅クラスは使わない
- Header 高さ `h-[68px]`、Nav アイテム高さ `h-[32px]`、アイテム間 `gap-[6px]`
- コンパクト時: Nav Item は `size-[32px] justify-center px-0`、Account Card はアバターのみ

---

## 6. Token Mapping

| 用途 | class（Hex） | トークン |
|------|------|---------|
| サイドバー背景 | `bg-[#edf0f3]` | `color.surface.sunken` |
| Active/Hover 背景 | `bg-[#dde3eb]` | `color.border.subtle` 近傍（操作面） |
| Press 背景 | `bg-[#c4cdd9]` | `color.border.default` |
| バッジ背景 | `bg-[#2661cf]` | `color.primary.700` |
| アカウントカード背景 | `bg-[#f7f9fb]` | `color.surface.base` |
| メインテキスト | `text-[#081a27]` | `color.content.primary` |
| セクションラベル | `text-[#5a6c7f]` | `color.content.secondary` |
| Disabled テキスト | `text-[#a1afc0]` | `color.content.disabled` |
| アバター（オレンジ） | `bg-[#ee8c29]` | `color.brand`（アクセント） |
| フォーカスリング / 仕切り線 | `ring-[#2661cf]` / `bg-[#c4cdd9]` | `color.border.focus` / `color.border.default` |

---

## 7. Accessibility
- `<nav aria-label="メインナビゲーション">` 必須。ランドマークは `<aside>` を使用しメインは `<main>` で囲む
- Active ナビアイテムに `aria-current="page"` 付与（色のみで現在地を伝えない）
- コンパクト時は各アイコンに `aria-label` + `title` 必須
- Disabled は `aria-disabled="true"` + `tabindex="-1"`（キーボードからも操作不可）

## 8. Content Guidelines
- ラベルは名詞（画面名・セクション名）。動詞は使わない（「設定する」→「設定」）
- セクションラベルは短く（「案件」「メッセージ」「データベース」）

## 9. Usage Do / Don't

```html
<!-- Do: 3ゾーン + Active に aria-current -->
<aside class="bg-[#edf0f3] flex-shrink-0 flex flex-col h-screen" style="width:188px;">
  <div class="flex items-center pl-[12px] py-[20px] h-[68px]"><!-- ロゴ --></div>
  <nav class="flex-1 overflow-y-auto flex flex-col gap-[6px] pl-[12px] pb-[8px]" aria-label="メインナビゲーション">
    <p class="text-[12px] font-medium text-[#5a6c7f] px-[8px] pt-[4px]">案件</p>
    <a href="#" aria-current="page" class="flex items-center gap-[6px] h-[32px] px-[8px] rounded-[4px] text-[14px] font-medium text-[#081a27] bg-[#dde3eb]">
      <svg class="w-[18px] h-[18px] flex-shrink-0">...</svg>案件・工事修繕
    </a>
  </nav>
  <div class="pb-[8px] pl-[8px]"><!-- Account Card --></div>
</aside>
```

**Don't**: `bg-white` 背景（→`bg-[#edf0f3]`）/ `border-r border-slate-200` の右ボーダー（不要）/ `rounded-lg` のアイテム角丸（→`rounded-[4px]`）/ Active に `bg-primary-50` `text-primary-500`（→`bg-[#dde3eb]` + `text-[#081a27]`）/ 左端の色付き縦アクセントバー（`border-l-4` 等）で現在地を示す（→ 背景色 + テキスト色 + `aria-current="page"` で示す）/ バッジに `rounded-full`（→`rounded-[2px]`）/ `w-64` 等の固定幅 / `<nav>` の `aria-label` 省略 / コンパクト時アイコンの `aria-label` 省略 / Side nav 内へのアクションボタン配置（→Navbars）。

---

## 10. Implementation Notes
- 標準↔コンパクト切替: `transition: width 300ms ease-in-out` + テキスト `opacity 200ms ease`。折りたたみ状態はローカルストレージに保存
- コンパクト時は各アイテムに Tooltip でラベルを補完
- アイコンは Lucide（`w-[18px] h-[18px]`）。Section Label はコンパクト時に水平線へ置換
