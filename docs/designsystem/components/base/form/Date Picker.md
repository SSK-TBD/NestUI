# Component Guideline: Date Picker

> NestUI Date Picker 仕様。Tailwind CSS。テキストフィールド風トリガー + カレンダーポップアップ。値は `tokens/`（色は `color.primary.*` / `color.border.*` 等）を正とし、以下の class はその適用例。`<input type="date">` は使わずカスタム実装する。

## 1. Component Identity
- **Name**: Date Picker
- **Variants**: 単一日付（既定）
- **Responsibility**:
  - する: カレンダーUIで日付を選択し `YYYY-MM-DD`（ISO 8601）で格納する
  - しない: 時刻選択（Date Time Picker として別途）、期間の数値入力（→ Input Number / Text field）
- **Target Platform**: デスクトップ Web アプリ（業務 SaaS）

**原則**: Dropdown パターン踏襲（テキストフィールド風トリガー + ポップアップ。同じ z-index・shadow 体系）/ `YYYY-MM-DD` 固定（表示・格納とも ISO 8601）/ キーボード完全対応（矢印で日付ナビ、Enter で選択、Escape で閉じる）/ ネイティブ不使用（ブラウザ間で表示が不統一）。

---

## 2. Variants & States

**トリガー状態**

| 状態 | 変化 |
|------|------|
| Default | 未選択時はプレースホルダー（`text-[#a1afc0]`） |
| Hover | `hover:border-[#a1afc0]` |
| Focus / Open | `focus-visible:ring-2 ring-primary-700/20 border-primary-700` |
| Error | `border-[#e93766] ring-2 ring-[#e93766]/20` + エラーメッセージ |
| Disabled | `opacity-50 cursor-not-allowed` |

**日付セル状態**

| 状態 | 背景 | テキスト |
|------|------|----------|
| Default（今月・平日） | `bg-white` | `#3e5062` |
| Default（今月・日曜） | `bg-white` | `#e93766` |
| Default（今月・土曜） | `bg-white` | `#2661cf` |
| Hover / Press（今月） | `#f7f9fb` / `#dde3eb` | 同上 |
| Selected | `#f0f7fe`（hover `#ddedfc` / press `#c2e0fb`） | `#2661cf` |
| Today（未選択） | Default に準拠 | `#3e5062` bold + 下線インジケーター（`#3e5062`） |
| Today（選択済み） | `#f0f7fe` | `#2661cf` bold + 下線インジケーター（`#2661cf`） |
| 他月 | `bg-white` | `#a1afc0`（クリック不可） |
| Disabled | `#edf0f3` | `#c4cdd9` |
| Focus | Selected/Default BG | + FocusRing（`absolute inset-[-3px] border-2 border-[#2661cf]`） |

---

## 3. サイズ / Anatomy

| パーツ | 寸法 / class |
|--------|------|
| Trigger | `w-full h-[36px] px-3 rounded border border-[#c4cdd9]`（`h-10`/`py-2` 禁止） |
| Calendar Icon（左） | `w-4 h-4 text-[#5a6c7f]`（Lucide calendar） |
| Trigger Text | `flex-1 text-[14px] font-medium tracking-[0.28px]`（選択済 `#081a27` / 未選択 `#a1afc0`） |
| Chevron（右） | `w-4 h-4 text-[#5a6c7f]`（chevron-down） |
| Popup | `absolute mt-1 w-[312px] rounded-[4px] border border-[#c4cdd9] p-[16px] z-20` + 指定 shadow |
| Day Cell | `w-[40px] h-[32px] rounded-[3px]` テキスト `text-[14px]`（Today `font-bold`） |
| Nav Button | `w-[28px] h-[28px] rounded-[2px] hover:bg-[#edf0f3] active:bg-[#dde3eb]` |

**Anatomy**: Trigger + Hidden Input（`type="hidden"`、フォーム送信用）+ Calendar Popup（Month Header〔前年/前月/年月ラベル/次月/次年〕+ Day-of-Week Header〔日〜土〕+ Day Grid）。Popup shadow: `shadow-[0px_4px_10px_0px_rgba(10,10,10,0.1),0px_2px_5px_0px_rgba(10,10,10,0.05)]`。

---

## 4. Composition Rules

許可: Trigger 上に `<label>` / Hidden Input / Popup 内のカレンダー構造。
禁止: `<input type="date">` のネイティブ表示 / Selected セルに `bg-primary-500 text-white` 等の塗りつぶし（→ `bg-[#f0f7fe]`）/ MonthHeader を2ボタン（前月/次月のみ）にすること（前年/次年も必須）/ 曜日ヘッダーの省略 / キーボードナビ省略。
配置: 年月ラベルは「N月　YYYY」全角スペース区切り。曜日カラーは日=赤 / 土=青 / 月〜金=グレー。

---

## 5. Layout & Spacing

- Popup 幅 `w-[312px]`（`w-[320px]` 禁止）、内側 `p-[16px]`
- Month Header `pb-[12px]`、Day-of-Week Header `h-[32px] w-[280px]`、Day Grid `w-[280px]`
- 各週 `flex items-start`、各セル `w-[40px] h-[32px]`
- Today インジケーター `absolute bottom-[4px] left-[4px] right-[4px] h-[2px]`

---

## 6. Token Mapping

| 用途 | トークン |
|------|---------|
| トリガー / Popup ボーダー | `color.border.default`(#c4cdd9) |
| フォーカスボーダー | `color.border.focus`(#2661cf = primary-700) |
| トリガーアイコン | `color.content.secondary`(#5a6c7f) |
| 日曜テキスト / エラー | `color.semantic.error`(#e93766) |
| 土曜・選択テキスト | `color.primary.700`(#2661cf) |
| 平日テキスト | `#3e5062`（content 補助） |
| Selected 背景 / hover / press | `color.primary.50`(#f0f7fe) / `#ddedfc`(primary-100) / `color.primary.200`(#c2e0fb) |
| 他月テキスト | `color.content.disabled`(#a1afc0) |
| Disabled 背景 / テキスト | `color.surface.sunken`(#edf0f3) / `color.border.default`(#c4cdd9) |
| Popup shadow | `elevation.shadow.md` 近傍（指定値を使用） |
| z-index | `elevation.z.dropdown`(20) |
| 角丸（Popup / セル / Nav） | `radius.sm` 近傍（`rounded-[4px]` / `rounded-[3px]` / `rounded-[2px]`） |

---

## 7. Accessibility
- Trigger: `role="combobox" aria-haspopup="dialog" aria-expanded` + `aria-controls`
- Popup: `role="dialog" aria-label="カレンダー"` / Week Row `role="row"` / Day Cell `role="gridcell"` + `tabindex`（フォーカス対象のみ）
- Selected: `aria-selected="true"` / Today: `aria-current="date"` / Disabled: `aria-disabled="true"`
- Nav ボタン: `aria-label="前の年/前の月/次の月/次の年"`
- Trigger 上に `<label>`（`for` で関連付け）
- キーボード: Enter/Space（開閉・選択）、Escape（閉じてトリガーへ）、Arrow（前日/翌日/前週/翌週）、PageUp/PageDown（前月/翌月）

## 8. Content Guidelines
- プレースホルダーは「日付を選択」
- 表示・格納は `YYYY-MM-DD` 固定

## 9. Usage Do / Don't

```html
<!-- Do: トリガー（calendar アイコン + プレースホルダー + chevron） -->
<div class="relative" id="dp-wrapper">
  <label for="dp-trigger" class="block text-[14px] font-medium text-[#081a27] tracking-[0.28px] mb-1 leading-normal">日付</label>
  <button id="dp-trigger" type="button" role="combobox" aria-haspopup="dialog" aria-expanded="false" aria-controls="dp-popup"
    onclick="toggleDatePicker()"
    class="w-full flex items-center gap-2 rounded border border-[#c4cdd9] bg-white px-3 h-[36px] hover:border-[#a1afc0] transition-colors focus:outline-none focus-visible:ring-2 focus-visible:ring-primary-700/20 focus-visible:border-primary-700">
    <svg class="w-4 h-4 text-[#5a6c7f] flex-shrink-0" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
      <rect width="18" height="18" x="3" y="4" rx="2" ry="2"/><line x1="16" x2="16" y1="2" y2="6"/><line x1="8" x2="8" y1="2" y2="6"/><line x1="3" x2="21" y1="10" y2="10"/>
    </svg>
    <span id="dp-display" class="flex-1 text-left text-[14px] font-medium tracking-[0.28px] text-[#a1afc0]">日付を選択</span>
    <svg class="w-4 h-4 text-[#5a6c7f] flex-shrink-0" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><polyline points="6 9 12 15 18 9"/></svg>
  </button>
  <input type="hidden" id="dp-value" name="date" value="">
  <!-- Popup: Month Header（前年/前月/年月/次月/次年）+ 曜日ヘッダー（日=赤・土=青）+ Day Grid -->
</div>
```

> Day Grid と開閉/矢印キーナビは JS で描画する。年月ラベルは `(month+1) + '月　' + year`、ISO 値生成は `year-MM-DD`、Selected セル `bg-[#f0f7fe]`、Today は `font-bold` + 2px 下線インジケーター。

**Don't**: `rounded-xl`/`rounded-lg` を Popup に（→ `rounded-[4px]`）/ `w-[320px]`（→ `w-[312px]`）/ キーボードナビ省略 / `aria-selected` 省略 / Today インジケーター省略 / トリガー `h-10`/`py-2`（→ `h-[36px]`）/ Selected セルの塗りつぶし。

---

## 10. Implementation Notes
- アイコンは Lucide: calendar（トリガー）/ chevron-left・chevron-right（月送り）/ chevrons-left・chevrons-right（年送り）。stroke ベース
- 外部クリック・Escape で閉じる。開いたら selected → today → 最初の有効セルの順でフォーカス
- トークン参照は `guidelines/TOKEN_GUIDE.md`。フォーム全体は `Form.md`、トリガーは Text field のスタイル体系を参照
