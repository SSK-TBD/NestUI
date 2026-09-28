# Component Guideline: Accordion

> NestUI Accordion 仕様。Tailwind CSS。値は `tokens/`（色は `color.surface.*` 等）を正とし、以下の class はその適用例。

## 1. Component Identity
- **Name**: Accordion
- **Variants**: `single`（単一展開）/ `multiple`（複数展開）（+ ボーダーあり / ボーダーなし / デフォルト展開）
- **Responsibility**:
  - する: 情報を折りたたんで段階的に開示し、ページの見通しを保つ。1クリックで開閉する
  - しない: ウィザードのステップ管理（Steps を使う）/ タブ切り替え（Tabs を使う）/ ナビゲーション
- **Target Platform**: デスクトップ Web アプリ（業務 SaaS）

**原則**: 段階的開示（Progressive Disclosure）/ デフォルトは全閉じ（重要情報がある場合のみデフォルト展開）/ `<details>/<summary>` 不使用（アニメーション制御が困難なため `<div>` + `<button>` + JS で制御）/ 開閉状態を色だけで伝えない（アイコン回転で明示）。

---

## 2. Variants & States

**Variants**

| Variant | 用途 |
|---------|------|
| `single`（単一展開） | 1つ開くと他のパネルが自動で閉じる。FAQ・設定パネル |
| `multiple`（複数展開） | 複数のパネルを同時に開ける。詳細情報・フィルター |
| ボーダーあり | アイテム間に区切りを表示。標準的なリスト（FAQ 等） |
| ボーダーなし | アイテム間の区切りを省略。カード内のセクション・軽量な表示 |
| デフォルト展開 | 特定のパネルを初期状態で開いておく。重要情報を最初から表示 |

**States**

| State | 変化 |
|-------|------|
| collapsed | パネル `hidden`、トリガー: 通常スタイル、Triangle アイコン: ▼（0deg） |
| expanded | パネル表示、Triangle アイコン: ▲（`rotate(180deg)` + `duration-200`）。`aria-expanded="true"` |
| hover | トリガー背景 `hover:bg-[#edf0f3]`（`color.surface.sunken`）+ `transition-colors` |
| focus | `focus-visible:ring-2 focus-visible:ring-primary-700 focus-visible:ring-offset-1` |
| reduced-motion | `prefers-reduced-motion` 時はアニメーションを無効化し即時切り替え |

---

## 3. パーツ / アイコン

| パーツ | 役割 | 必須 | class（適用例） |
|--------|------|:----:|------|
| Container | Accordion 全体のラッパー（複数並べる場合は `space-y-2`） | Yes | `space-y-2` |
| Item | 個々の折りたたみ項目（Trigger + Panel のペア） | Yes | `bg-white rounded-[6px] overflow-hidden` |
| Trigger | 展開・折りたたみを操作する `<button>` | Yes | `w-full flex items-center gap-2 p-3 text-left hover:bg-[#edf0f3] transition-colors focus:outline-none focus-visible:ring-2 focus-visible:ring-primary-700 focus-visible:ring-offset-1` |
| Icon | 開閉状態を示す Triangle アイコン（回転で表現） | Yes | `w-7 h-7 flex-shrink-0 inline-flex items-center justify-center bg-[#f7f9fb] rounded-sm`（内部: filled triangle SVG 10×6px） |
| タイトル | 項目見出し | Yes | `flex-1 text-[16px] font-bold text-[#081a27] tracking-[0.48px] leading-[1.5]` |
| サブテキスト | 右端の補足（任意） | No | `text-[12px] font-medium text-[#3e5062] tracking-[0.24px] whitespace-nowrap` |
| Panel | 展開時のコンテンツ領域 | Yes | `hidden px-3 pb-3`（表示時は `hidden` 除去。トリガーの `p-3` が上部間隔を担う） |

**Triangle アイコン SVG**（チェブロン `>` `∨` は禁止 → filled triangle 10×6px を使用）
```html
<!-- 閉じた状態（▼）/ open 時は transform:rotate(180deg) が付く -->
<svg style="width:10px;height:6px;transition:transform 0.2s;flex-shrink:0"
     viewBox="0 0 10 6" fill="currentColor" aria-hidden="true">
  <path d="M0 0L10 0L5 6Z"/>
</svg>
```

---

## 4. Composition Rules

許可: テキストタイトル / 右端サブテキスト（任意）/ パネル内の任意コンテンツ。
禁止: Trigger に `<div>` や `<a>`（必ず `<button>`）/ アコーディオン内にアコーディオンをネスト（最大1階層）/ `single` モードで重要コンテンツを全閉じ初期表示（`defaultOpen` を設定）。
JS: `toggleAccordion(this)` は `btn.nextElementSibling` をパネルとして扱う。`single` モード時は他パネルを閉じてから開く。

---

## 5. Layout & Spacing

| プロパティ | トークン | 値 |
|-----------|---------|-----|
| トリガー padding | `spacing.3` | 12px（`p-3`） |
| パネル padding | `spacing.3` | 左右・下 12px（`px-3 pb-3`） |
| トリガー内 gap | `spacing.2` | 8px（`gap-2`） |
| Item 間隔 | `spacing.2` | 8px（`space-y-2`） |
| Item radius | `radius.*`（6px） | `rounded-[6px]` |
| アイコンボックス | — | 28px（`w-7 h-7`、`rounded-sm`） |
| Triangle サイズ | — | 10×6px |

コンポーネント自体に外側 `margin` を持たせない。外側余白は呼び出し側で指定する。

---

## 6. Token Mapping

| 用途 | トークン |
|------|---------|
| Item 背景 | `#ffffff`（`color.surface.raised`） |
| トリガー hover 背景 | `#edf0f3`（`color.surface.sunken`） |
| アイコンボックス背景 | `#f7f9fb`（`color.surface.base`） |
| タイトルテキスト | `#081a27`（`color.content.primary`） |
| サブテキスト | `#3e5062`（`color.content.secondary` 近傍） |
| focus ring | `color.border.focus`（= primary-700） |
| Item radius | `radius.*`（6px） |
| アニメーション | `motion.duration.normal`（200ms、Triangle 回転） |

---

## 7. Accessibility

- Trigger は必ず `<button>` を使用（`<div>` / `<a>` 不可）
- `aria-expanded`: Trigger に付与（展開 `true` / 折りたたみ `false`）。省略禁止
- `aria-controls`: Trigger に対応する Panel の `id` を指定
- Panel: ユニークな `id` を付与（`role="region"` + `aria-labelledby` で見出し参照も可）
- キーボード: Enter / Space で開閉（`<button>` の既定動作）。Tab でヘッダー間移動
- フォーカス: Trigger にフォーカスインジケーターを表示
- パネル内の重要情報をキーボードで到達不能にしない。展開状態を色だけで伝えない

---

## 8. Content Guidelines
- タイトルは簡潔に（「詳細設定」「よくある質問」「アカウント設定について」）
- サブテキストは補足のみ。タイトルの内容を重複させない

---

## 9. Usage Do / Don't

```html
<!-- Do: 標準（複数アイテム、1件目展開） -->
<div class="space-y-2">
  <div class="bg-white rounded-[6px] overflow-hidden">
    <button onclick="toggleAccordion(this)" aria-expanded="true" aria-controls="panel-1"
      class="w-full flex items-center gap-2 p-3 text-left hover:bg-[#edf0f3] transition-colors focus:outline-none focus-visible:ring-2 focus-visible:ring-primary-700 focus-visible:ring-offset-1">
      <div class="w-7 h-7 flex-shrink-0 inline-flex items-center justify-center bg-[#f7f9fb] rounded-sm" aria-hidden="true">
        <svg style="width:10px;height:6px;transition:transform 0.2s;transform:rotate(180deg)" viewBox="0 0 10 6" fill="currentColor"><path d="M0 0L10 0L5 6Z"/></svg>
      </div>
      <span class="flex-1 text-[16px] font-bold text-[#081a27] tracking-[0.48px] leading-[1.5]">アカウント設定について</span>
      <span class="text-[12px] font-medium text-[#3e5062] tracking-[0.24px] whitespace-nowrap">サブテキスト</span>
    </button>
    <div id="panel-1" class="px-3 pb-3">
      <p class="text-base text-body">アカウント設定はプロフィールページから変更できます。</p>
    </div>
  </div>
</div>
```

**Don't**: `border border-slate-200 rounded-lg divide-y` でラッピング（→ `bg-white rounded-[6px]` + `space-y-2`）/ チェブロン（`>` `∨`）をインジケーターに（→ filled triangle 10×6px）/ `aria-expanded` の省略 / アコーディオンのネスト（最大1階層）/ 展開アニメーションの省略。

---

## 10. Implementation Notes
- パネルは `hidden` クラスのトグルで即時切り替え。Triangle のみ `transition: transform 0.2s` で回転
- 高さアニメーションを使う場合は `max-height` を `0` → `scrollHeight` に CSS transition で変化させる
- 親子展開テーブルの展開トグルは本コンポーネントと同じ Triangle SVG を共有（`base/table/Table.md` 参照）
- 思想は `FOUNDATIONS.md`、使い分けは `COMPONENT_GUIDE.md`
