# Component Guideline: Chip

> NestUI Chip 仕様。Tailwind CSS。値は `tokens/`（色は `color.primary.*` / slate スケール等）を正とし、以下の class はその適用例。選択操作型（Form / Filter）とファイル添付型（File Chip）を含む。

## 1. Component Identity
- **Name**: Chip
- **Variants**: Form Chip（複数/単一）/ Filter Chip（複数/単一）/ File Chip（ファイル添付）
- **Responsibility**:
  - する: 選択肢・フィルター条件のオン/オフ切り替え（Form/Filter）、ファイル添付の表示と操作（File）
  - しない: 読み取り専用ステータス表示（→ Badge）、削除可能なメタデータ表示（→ Tag）
- **Target Platform**: デスクトップ Web アプリ（業務 SaaS）

**原則**: 選択操作を内包する（複数=チェックボックス / 単一=ラジオのセマンティクス）/ use case で2種類（`Form`=フォーム内選択肢、`Filter`=一覧の絞り込み）/ Tag・Badge と混同しない / 状態は border-color・背景・アイコンで伝達（アイコン省略可は Filter 複数選択のみ）。

---

## 2. Variants & States

**選択操作型 Variants**

| use case | シーン | 複数選択 | 単一選択 |
|----------|--------|----------|----------|
| **Form** | フォーム内の選択肢（物件条件・希望タグ等） | チェックボックスアイコン内蔵 | ラジオアイコン内蔵 |
| **Filter** | 一覧画面の絞り込み条件 | アイコンなし（bg/border のみ） | ラジオアイコン内蔵 |

**選択操作型 States**（Form/Filter 共通スタイル）

| State | Unselected | Selected |
|-------|-----------|---------|
| Default | `bg-white border-slate-300 text-slate-600` | `bg-primary-50 border-primary-700 text-primary-700` |
| Hover | `hover:bg-primary-50` | `hover:bg-[#ddedfc]` |
| Active | `active:bg-[#ddedfc]` | `active:bg-[#c2e0fb]` |
| Focus | `focus-visible:ring-2 ring-primary-700 ring-offset-1` | 同左 |
| Disabled | `bg-white border-slate-200 text-slate-300 cursor-not-allowed` + `aria-disabled="true"` `tabindex="-1"` | — |

**File Chip States**

| State | 背景 | アイコン背景 | テキスト |
|-------|------|------------|---------|
| Enable | `bg-white cursor-pointer` | `bg-[#5a6c7f]` | `text-[#081a27]` |
| Hover | `bg-[#edf0f3]` | `bg-[#5a6c7f]` | `text-[#081a27]` |
| Pressed | `bg-[#c4cdd9]` | `bg-[#5a6c7f]` | `text-[#081a27]` |
| Disabled | `bg-white cursor-not-allowed` + `aria-disabled="true"` | `bg-[#c4cdd9]` | `text-[#a1afc0]` |

> File Chip の Media Container（画像プレビュー）は **Enable 状態のみ** 表示。Delete/More ボタンは Disabled でも表示するが操作不能（`<span>` に置換 + `aria-hidden`）。

---

## 3. サイズ / Anatomy

**選択操作型**: `h-6 rounded-full border text-xs font-medium tracking-[0.24px] inline-flex items-center transition-colors`
- アイコンあり（Form 全種・Filter 単一）: `pl-1 pr-2 gap-1` + アイコン `w-3.5 h-3.5 shrink-0`
- アイコンなし（Filter 複数）: `px-2`

**File Chip**: `border border-[#dde3eb] rounded-[8px] p-2 flex flex-col gap-3`（例 `w-[374px]`）。SummaryIcon `size-9 rounded-[8px]`、Title `text-[14px] font-bold leading-[1.5] truncate`、More/Delete `w-4 h-4`、Media Container `h-[216px]`（セパレーター + 画像）。

**Anatomy（選択操作型）**: Container + Icon（チェックボックス/ラジオ SVG）+ Label Text。
**Anatomy（File Chip）**: Container + SummaryIcon（36×36）+ Title（truncate）+ More Button（任意）+ Delete Button（任意）+ Media Container（任意・Enable のみ）。

---

## 4. Composition Rules

許可（選択操作型）: アイコン SVG（14×14）+ ラベルテキスト。グループは `role` 付きラッパーで囲む。
許可（File Chip）: SummaryIcon + Title + More/Delete ボタン + Media（画像）。
禁止: `aria-checked`/`aria-selected` の省略 / Filter 複数選択以外でアイコンを省略 / Badge・Tag と混同 / Disabled に `cursor-pointer`（→ `cursor-not-allowed`）/ File Chip の Media を Hover/Pressed/Disabled で表示。
振る舞い: 複数=同時に複数 Active / 単一=排他制御。Click/Enter/Space で切替、Arrow Left/Right でグループ内移動（単一は移動と同時に選択変更）。

---

## 5. Layout & Spacing

- グループラッパー: `flex flex-wrap gap-1.5`
- File Chip 内部: Header `flex gap-3 items-center`、Media `flex flex-col gap-2`（セパレーター `py-1` の中に `h-px bg-[#c4cdd9]`）
- 角丸: 選択操作型 `rounded-full` / File Chip `rounded-[8px]`（内部画像 `rounded-[4px]`）

---

## 6. Token Mapping

| 用途 | トークン |
|------|---------|
| Selected ボーダー・テキスト | `color.primary.700` |
| Selected 背景 / hover / active | `color.primary.50` / `#ddedfc`(primary-100) / `color.primary.200`(#c2e0fb) |
| Unselected ボーダー / 背景 | slate-300（`color.border.default` 近傍）/ `color.surface.input`(white) |
| Disabled ボーダー / テキスト | slate-200（`color.border.subtle` 近傍）/ slate-300 |
| アイコン（未選択/選択） | `#c4cdd9`(`color.border.default`) / `#2661cf`(primary-700) |
| File Chip ボーダー | `color.border.subtle`(#dde3eb) |
| File Chip アイコン背景（Enable/Disabled） | `#5a6c7f`(`color.content.secondary`) / `#c4cdd9`(`color.border.default`) |
| File Chip Title（Enable/Disabled） | `color.content.primary`(#081a27) / `color.content.disabled`(#a1afc0) |
| File Chip hover / pressed 背景 | `color.surface.sunken`(#edf0f3) / `color.border.default`(#c4cdd9) |
| focus ring | `color.border.focus`(= primary-700) |

---

## 7. Accessibility

| use case | コンテナ role | 各チップ role | 選択状態属性 |
|----------|--------------|--------------|-------------|
| Form 複数 | `group` + `aria-label` | `checkbox` | `aria-checked` |
| Form 単一 | `radiogroup` + `aria-label` | `radio` | `aria-checked` |
| Filter 複数 | `listbox` + `aria-multiselectable="true"` | `option` | `aria-selected` |
| Filter 単一 | `listbox` + `aria-multiselectable="false"` | `option` | `aria-selected` |
| File Chip | — | Container `role="article"` | Disabled は `aria-disabled="true"` |

- 色だけで状態を伝達しない（アイコン併用。Filter 複数のみ bg/border で区別）
- Disabled チップは `aria-disabled="true"` + `tabindex="-1"`
- File Chip ボタンは `aria-label`（「その他のオプション」「削除」）、画像は `alt`、Disabled ボタンは `<span aria-hidden>` に置換
- `prefers-reduced-motion` でトランジション停止

## 8. Content Guidelines
- ラベルは選択肢・条件を端的に
- グループ `aria-label` で何の選択かを明示（「物件種別」「カテゴリ」）
- File Chip の Title はファイル名（truncate、`font-feature-settings: 'pwid' 1`）

## 9. Usage Do / Don't

```html
<!-- Do: Form Chip 複数選択（チェックボックス内蔵・Selected） -->
<button type="button" role="checkbox" aria-checked="true"
  class="h-6 pl-1 pr-2 inline-flex items-center gap-1 rounded-full border border-primary-700 bg-primary-50 text-primary-700 text-xs font-medium tracking-[0.24px]
         hover:bg-[#ddedfc] active:bg-[#c2e0fb] transition-colors cursor-pointer focus:outline-none focus-visible:ring-2 focus-visible:ring-primary-700 focus-visible:ring-offset-1">
  <svg class="chip-icon w-3.5 h-3.5 shrink-0" viewBox="0 0 14 14" fill="none">
    <rect width="14" height="14" rx="2" fill="#2661CF"/><path d="M3 7l2.5 2.5L11 5" stroke="white" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
  </svg>
  ラベル
</button>

<!-- Do: Filter Chip 複数選択（アイコンなし・Unselected） -->
<button type="button" role="option" aria-selected="false"
  class="h-6 px-2 inline-flex items-center rounded-full border border-slate-300 bg-white text-slate-600 text-xs font-medium tracking-[0.24px]
         hover:bg-primary-50 active:bg-[#ddedfc] transition-colors cursor-pointer focus:outline-none focus-visible:ring-2 focus-visible:ring-primary-700 focus-visible:ring-offset-1">ラベル</button>

<!-- Do: グループラッパー -->
<div role="group" aria-label="物件種別" class="flex flex-wrap gap-1.5"><!-- Form Multi チップ --></div>
<div role="listbox" aria-label="カテゴリ" aria-multiselectable="true" class="flex flex-wrap gap-1.5"><!-- Filter Multi チップ --></div>

<!-- Do: File Chip（Enable・メディア + ボタンあり） -->
<div class="border border-[#dde3eb] rounded-[8px] p-2 flex flex-col gap-3 bg-white cursor-pointer hover:bg-[#edf0f3] active:bg-[#c4cdd9] transition-colors w-[374px]" role="article">
  <div class="flex gap-3 items-center w-full">
    <div class="bg-[#5a6c7f] rounded-[8px] size-9 shrink-0 flex items-center justify-center overflow-hidden">
      <svg class="w-5 h-5 text-white" viewBox="0 0 20 20" fill="none" stroke="currentColor" stroke-width="1.5">
        <path stroke-linecap="round" stroke-linejoin="round" d="M13.5 3H6.5A1.5 1.5 0 005 4.5v11A1.5 1.5 0 006.5 17h7A1.5 1.5 0 0015 15.5V6.5L13.5 3z"/>
        <path stroke-linecap="round" stroke-linejoin="round" d="M13 3v3.5H15.5"/>
      </svg>
    </div>
    <p class="flex-1 text-[14px] font-bold text-[#081a27] leading-[1.5] truncate min-w-0" style="font-feature-settings: 'pwid' 1">ファイル名が入ります.pdf</p>
    <button type="button" aria-label="その他のオプション" class="w-4 h-4 shrink-0 text-[#5a6c7f] hover:text-[#081a27] flex items-center justify-center rounded-sm focus-visible:ring-2 focus-visible:ring-primary-700">
      <svg class="w-4 h-4" viewBox="0 0 16 16" fill="currentColor"><circle cx="3" cy="8" r="1.5"/><circle cx="8" cy="8" r="1.5"/><circle cx="13" cy="8" r="1.5"/></svg>
    </button>
    <button type="button" aria-label="削除" class="w-4 h-4 shrink-0 text-[#5a6c7f] hover:text-[#081a27] flex items-center justify-center rounded-sm focus-visible:ring-2 focus-visible:ring-primary-700">
      <svg class="w-4 h-4" viewBox="0 0 16 16" fill="none" stroke="currentColor" stroke-width="1.5"><circle cx="8" cy="8" r="6.25"/><path stroke-linecap="round" d="M5.5 5.5l5 5M10.5 5.5l-5 5"/></svg>
    </button>
  </div>
  <!-- Media Container（Enable のみ） -->
  <div class="flex flex-col gap-2 h-[216px]">
    <div class="py-1"><div class="h-px bg-[#c4cdd9]" role="separator"></div></div>
    <div class="flex-1 flex flex-col min-h-0"><img src="..." alt="ファイルプレビュー" class="w-full h-full object-cover rounded-[4px]"></div>
  </div>
</div>
```

> アイコン SVG（14×14）: チェックボックス（未選択 `<rect stroke="#c4cdd9">` / 選択 `<rect fill="#2661CF">` + チェック）、ラジオ（未選択 `<circle stroke="#c4cdd9">` / 選択 `<circle stroke="#2661CF">` + 内側 `<circle fill="#2661CF">`）。

**Don't**: `aria-checked`/`aria-selected` 省略 / Filter 複数以外でアイコン省略 / Badge・Tag と混同 / Disabled に `cursor-pointer` / File Chip の Media を Enable 以外で表示 / SummaryIcon 背景を Enable/Disabled で固定。

---

## 10. Implementation Notes
- Tag（削除可能メタデータ）・Badge（読み取り専用ステータス）とは別物。選択のオン/オフは Chip
- Filter Chip はフィルタートリガー・ドロップダウンと組み合わせる（一覧の絞り込み）
- トークン参照は `guidelines/TOKEN_GUIDE.md`
