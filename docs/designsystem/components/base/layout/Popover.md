# Component Guideline: Popover

> NestUI Popover 仕様。Tailwind CSS。値は `tokens/`（色は `color.surface.*` 等）を正とし、以下の class はその適用例。
> インラインヘルプ・補足情報の表示に使うフローティングパネル。Tooltip と異なりインタラクティブ要素（閉じるリンク）を内包できる。

## 1. Component Identity
- **Name**: Popover
- **Variants**: `default`（タイトル + 閉じる + コンテンツ）
- **Responsibility**:
  - する: トリガーに紐付いた非モーダルなフローティングパネルで補足情報を表示する。閉じるリンクを内包する
  - しない: 単純なテキストヒント（Tooltip を使う）/ アクション選択メニュー（Dropdown menu を使う）/ モーダルコンテンツ（Modals を使う）
- **Target Platform**: デスクトップ Web アプリ（業務 SaaS）

**原則**: トリガーは明示的なアクション（ホバーではなくクリック/フォーカスで開閉）/ タイトル + 閉じる + コンテンツの3要素（必ずタイトルと閉じるリンクを設ける）/ 外部クリックで閉じる。

### Tooltip / Dropdown menu との違い

| | Popover | Tooltip | Dropdown menu |
|--|---------|---------|---------------|
| 開閉トリガー | クリック | ホバー / フォーカス | クリック |
| コンテンツ | テキスト + インタラクティブ要素可 | テキストのみ | アクション項目 |
| 閉じ方 | 閉じるリンク / 外部クリック / Escape | ホバーアウト | 項目選択 / 外部クリック / Escape |
| ARIA | `role="dialog"` | `role="tooltip"` | `role="menu"` |
| 用途 | 補足情報の閲覧 | 短いヒント | アクション実行 |

---

## 2. Variants & States

**Variants**: `default`（`w-[304px]` 固定幅。タイトル行 + コンテンツ）。

**States**

| State | 変化 |
|-------|------|
| closed | 非表示（`hidden` / `panel.hidden`） |
| open | 表示。タイトルまたは閉じるボタンにフォーカス。トリガー `aria-expanded="true"` |
| 閉じるリンク hover | `hover:text-primary-800` |

---

## 3. パーツ

| パーツ | 役割 | 必須 | class（適用例） |
|--------|------|:----:|------|
| Panel | フローティングパネル本体（`w-[304px]` 固定幅） | Yes | `bg-white rounded-sm border border-[#dde3eb] shadow-[0px_10px_15px_-5px_rgba(10,10,10,0.1),0px_5px_5px_-2px_rgba(10,10,10,0.05)] w-[304px] px-4 py-3 flex flex-col gap-1` |
| タイトル行 | タイトル + 閉じるリンクのラッパー | Yes | `flex items-center gap-1 w-full` |
| タイトル | 補足情報の見出し | Yes | `flex-1 text-[14px] font-bold text-[#081a27] leading-[1.5]` |
| 閉じるリンク | パネルを閉じるアクション | Yes | `flex-shrink-0 text-[12px] font-medium text-primary-700 leading-[1.3] tracking-[0.24px] cursor-pointer hover:text-primary-800 transition-colors` |
| コンテンツ | 説明テキスト | No | `text-[12px] font-normal text-[#5a6c7f] leading-[1.75] w-full` |

`overflow-hidden` は不要（Dropdown menu との違い。角丸クリップ不要）。

---

## 4. Composition Rules

許可: タイトル / 閉じるリンク / コンテンツテキスト / 軽量なインタラクティブ要素。
禁止: Popover 内に Popover をネスト / Tooltip の代わりに使用（読み取り専用ヒントは Tooltip）/ パネル内に複数の CTA ボタン（補足説明に徹し、主要 CTA はモーダル等へ）/ `w-auto` や可変幅（`w-[304px]` 固定）。

---

## 5. Layout & Spacing

| プロパティ | トークン | 値 |
|-----------|---------|-----|
| Panel padding | `spacing.4` / `spacing.3` | `px-4 py-3`（16px / 12px） |
| Panel 内 gap | `spacing.1` | `gap-1` |
| タイトル行 gap | `spacing.1` | `gap-1` |
| Panel 幅 | — | `w-[304px]` 固定 |
| Panel radius | `radius.sm`（4px） | `rounded-sm` |
| offset（トリガーから） | `spacing.1` | `mt-1` / `mb-1` |

---

## 6. Token Mapping

| 用途 | トークン |
|------|---------|
| Panel 背景 | `#ffffff`（`color.surface.overlay`） |
| Panel ボーダー | `#dde3eb`（`color.border.subtle`） |
| Panel shadow | `elevation.shadow.md`（`0px_10px_15px_-5px...` の具体 class が実装値） |
| タイトルテキスト | `#081a27`（`color.content.primary`） |
| 閉じるリンク | `color.primary.700` / hover `.800` |
| コンテンツテキスト | `#5a6c7f`（`color.content.secondary`） |
| z-index | `elevation.z.dropdown`（20） |
| radius | `radius.sm`（4px） |

---

## 7. Accessibility

- `role="dialog"` を Panel に付与 / `aria-labelledby` でタイトル要素の `id` を参照 / `aria-modal="false"`（背面とのインタラクションを許可）
- トリガー: `aria-expanded="true|false"` + `aria-controls` でパネルの `id` を参照
- フォーカス管理: 開いた時タイトルまたは閉じるボタンにフォーカス
- Escape で閉じてトリガーにフォーカスを戻す。外部クリックでも閉じる
- コントラスト比: テキスト 4.5:1 以上

---

## 8. Content Guidelines
- コンテンツは補足情報の表示のみ。ユーザーの理解を深める説明を記載する
- タイトルはポップオーバーの目的を示す名詞句
- コンテンツが長い場合は内部スクロールを検討（`max-height` + `overflow-y: auto`）

---

## 9. Usage Do / Don't

```html
<!-- トリガー -->
<button type="button" id="popover-trigger" aria-expanded="false" aria-controls="popover-panel"
  class="inline-flex items-center justify-center gap-1 h-7 px-3 text-[12px] font-medium tracking-[0.24px] bg-[#f7f9fb] text-[#3e5062] rounded hover:bg-[#edf0f3] active:bg-primary-100 cursor-pointer">
  ヘルプ
</button>

<!-- Popover パネル -->
<div id="popover-panel" role="dialog" aria-labelledby="popover-title" aria-modal="false"
  class="absolute top-full mt-1 z-20 bg-white rounded-sm border border-[#dde3eb]
    shadow-[0px_10px_15px_-5px_rgba(10,10,10,0.1),0px_5px_5px_-2px_rgba(10,10,10,0.05)]
    w-[304px] px-4 py-3 flex flex-col gap-1">
  <div class="flex items-center gap-1 w-full">
    <p id="popover-title" class="flex-1 text-[14px] font-bold text-[#081a27] leading-[1.5]">タイトル</p>
    <button type="button" aria-label="ポップオーバーを閉じる"
      class="flex-shrink-0 text-[12px] font-medium text-primary-700 leading-[1.3] tracking-[0.24px] cursor-pointer hover:text-primary-800 transition-colors">閉じる</button>
  </div>
  <p class="text-[12px] font-normal text-[#5a6c7f] leading-[1.75] w-full">補足情報テキスト。ユーザーが理解を深めるための説明を記載する。</p>
</div>
```

**Don't**: `role="tooltip"`（→ `role="dialog"` + `aria-labelledby`）/ ホバーで開閉（→ クリック）/ 閉じるボタンの省略 / `w-auto` や可変幅（→ `w-[304px]`）/ アクション実行に使用（→ Dropdown menu）/ パネル内に複数の CTA。

---

## 10. Implementation Notes
- 絶対配置（`absolute`）でトリガーに対して配置。優先位置はトリガーの下（`top-full mt-1`）または上（`bottom-full mb-1`）
- ビューポート内収まり: 右端・下端のはみ出しを JS で補正（Flip パターン）。位置計算は `@floating-ui/react` 等も可
- 開閉・Escape・外部クリック閉じは `keydown` / `click` で実装（`panel.hidden` をトグル）
- 思想は `FOUNDATIONS.md`、使い分けは `COMPONENT_GUIDE.md`
