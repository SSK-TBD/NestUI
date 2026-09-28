# Component Guideline: Tabs

> NestUI Tabs 仕様。Tailwind CSS。値は `tokens/`（色は `color.primary.*` 等）を正とし、以下の class はその適用例。

## 1. Component Identity
- **Name**: Tabs
- **Variants**: `underline`（下線インジケーター・既定）
- **Responsibility**:
  - する: 同一ページ内で関連するコンテンツ群を切り替え表示する。常に1つだけアクティブ
  - しない: ページ遷移（`<a>` / Text link を使う）、ウィザードのステップ管理（Stepper を使う）、垂直方向のグローバルナビ（Side nav を使う）
- **Target Platform**: デスクトップ Web アプリ（業務 SaaS）

**原則**: 1つだけアクティブ / アクティブタブは下線インジケーターで明示 / 矢印キーでタブ間移動（disabled はスキップ）。

---

## 2. Variants & States

**Tab List**: `flex border-b border-slate-200`

**Tab Item 状態**

| 状態 | class |
|------|-------|
| **Selected** | `h-[40px] px-4 inline-flex items-center justify-center relative border-b-2 border-primary-700 rounded-tl-[2px] rounded-tr-[2px] text-[14px] font-medium text-primary-700 tracking-[0.28px] hover:bg-primary-50 active:bg-primary-100 cursor-default outline-none` |
| **Unselected** | `h-[40px] px-4 inline-flex items-center justify-center relative rounded-[2px] text-[14px] font-medium text-body tracking-[0.28px] hover:bg-primary-50 active:bg-primary-100 cursor-pointer outline-none` |
| **Disabled** | `h-[40px] px-4 inline-flex items-center justify-center relative text-[14px] font-medium text-slate-300 tracking-[0.28px] cursor-not-allowed` |
| **Focus** | 上記に `focus-visible:ring-2 focus-visible:ring-primary-700 focus-visible:ring-offset-1` を付与 |

**Tab Panel**: `py-4`

---

## 3. プロパティ早見

| プロパティ | Selected | Unselected | Disabled |
|-----------|----------|------------|----------|
| 高さ / 横 padding | `h-[40px]` / `px-4` | `h-[40px]` / `px-4` | `h-[40px]` / `px-4` |
| テキスト色 | `text-primary-700` | `text-body` | `text-slate-300` |
| 下線 | `border-b-2 border-primary-700` | なし | なし |
| 角丸 | `rounded-tl-[2px] rounded-tr-[2px]` | `rounded-[2px]` | — |
| hover / active 背景 | `hover:bg-primary-50` / `active:bg-primary-100` | 同左 | — |
| カーソル | `cursor-default` | `cursor-pointer` | `cursor-not-allowed` |

---

## 4. Composition Rules

- 許可: `<Tab>` 内にテキスト、アイコン（`<svg>`）+ テキスト
- 禁止: `<Tab>` 内のフォーム要素 / `<TabList>` 内に `<Tab>` 以外の要素
- タブ数は6個以内を目安。7個以上は認知負荷が上がるため Dropdown や Side nav を検討
- Tabs をページ間遷移に使わない（ルーター連携時は `<a>` で実装）

---

## 5. Layout & Spacing

- タブ高さ `h-[40px]` 固定 + `inline-flex items-center`（`py-2.5` 等の可変パディング禁止）
- 横 padding `px-4`、下線 `border-b-2`（2px）、Tab Panel `py-4`
- letter-spacing は `tracking-[0.28px]`（フォントの2%）

---

## 6. Token Mapping

| 用途 | class | トークン |
|------|-------|---------|
| アクティブテキスト・下線 | `text-primary-700` / `border-primary-700` | `color.primary.700` |
| 非アクティブテキスト | `text-body` | `color.content.primary` |
| Disabled テキスト | `text-slate-300` | `color.content.disabled` |
| hover / active 背景 | `bg-primary-50` / `bg-primary-100` | `color.primary.50` / `.100` |
| Tab List 区切り線 | `border-slate-200` | `color.border.subtle` |
| focus ring | `ring-primary-700` | `color.border.focus` |

---

## 7. Accessibility
- `role="tablist"`（コンテナ）/ `role="tab"`（各タブ）/ `role="tabpanel"`（各パネル）
- `aria-selected="true/false"`、`aria-controls`（タブ→パネル id）、`aria-labelledby`（パネル→タブ id）
- `tabindex`: Selected `"0"` / Unselected `"-1"`
- キーボード: `←`/`→` で前後移動（disabled スキップ）、`Home`/`End` で先頭/末尾

## 8. Content Guidelines
- タブラベルは名詞（ビュー名）。「概要」「設定」「ログ」「メンバー」。動詞は使わない
- 最大3単語以内。アイコンのみのタブは `aria-label` 必須

## 9. Usage Do / Don't

```html
<!-- Do -->
<div role="tablist" aria-label="設定タブ" class="flex border-b border-slate-200">
  <button role="tab" aria-selected="true" aria-controls="panel-1" id="tab-1" tabindex="0"
    class="h-[40px] px-4 inline-flex items-center justify-center relative border-b-2 border-primary-700 rounded-tl-[2px] rounded-tr-[2px] text-[14px] font-medium text-primary-700 tracking-[0.28px] hover:bg-primary-50 active:bg-primary-100 cursor-default outline-none focus-visible:ring-2 focus-visible:ring-primary-700 focus-visible:ring-offset-1">一般</button>
  <button role="tab" aria-selected="false" aria-controls="panel-2" id="tab-2" tabindex="-1"
    class="h-[40px] px-4 inline-flex items-center justify-center relative rounded-[2px] text-[14px] font-medium text-body tracking-[0.28px] hover:bg-primary-50 active:bg-primary-100 cursor-pointer outline-none focus-visible:ring-2 focus-visible:ring-primary-700 focus-visible:ring-offset-1">通知</button>
</div>
<div id="panel-1" role="tabpanel" aria-labelledby="tab-1" tabindex="0" class="py-4">…</div>
```

**Don't**: `tabindex` 管理の省略（Selected `0` / 非選択 `-1`）/ `aria-selected` の省略 / `aria-controls` の省略 / `text-primary-500` のアクティブ色（→`text-primary-700 border-primary-700`）/ `py-2.5` のパディング（→`h-[40px]` 固定 + `inline-flex items-center`）/ ページ遷移にタブを使う。

---

## 10. Implementation Notes
- `switchTab` / `handleTabKeydown` を JS で実装。Selected 切替時に `aria-selected` `tabindex` とクラスを同期、対応パネルの `hidden` を切替
- 非アクティブパネルは `hidden`。重いコンテンツ（チャート・テーブル）は遅延表示を推奨
- アイコンは Lucide
