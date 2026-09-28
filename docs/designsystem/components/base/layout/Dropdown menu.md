# Component Guideline: Dropdown menu

> NestUI Dropdown menu（アクションメニュー）仕様。Tailwind CSS。値は `tokens/`（色は `color.surface.*` 等）を正とし、以下の class はその適用例。
> クリックして即時にアクションを実行するメニュー。フォーム内の値選択は `form/Dropdown`（Select）を使う（別物）。

## 1. Component Identity
- **Name**: Dropdown menu
- **Variants**: `basic`（テキストのみ）/ `with-icon`（アイコン付き）/ `grouped`（グループ + セパレータ）/ `with-destructive`（破壊的アクション含む）/ `with-tag`（ステータスタグ付き）
- **Responsibility**:
  - する: クリックでアクション項目を即時実行する（プロフィールメニュー・設定・「その他」・削除/ログアウト）
  - しない: フォーム内の値選択（`form/Dropdown`（Select）を使う）/ 補足情報の閲覧（Popover を使う）/ 短いヒント（Tooltip を使う）
- **Target Platform**: デスクトップ Web アプリ（業務 SaaS）

**原則**: アクション実行の第一選択 / トリガーは Button（クリックで開閉。ホバーでは開かない）/ 即時実行（選択 → 即アクション。確認が必要な操作はモーダル併用）/ 自動閉じ（外部クリック / Escape）。

### form/Dropdown（Select）との違い

| | Dropdown menu | form/Dropdown（Select） |
|--|---------------|------------------------|
| 用途 | アクション実行 | フォーム内の値選択 |
| 確定 | 即時実行 | フォーム送信で確定 |
| ARIA | `role="menu"` + `role="menuitem"` | `role="combobox"` / `<select>` |
| 例 | プロフィールメニュー、設定、「その他」 | カテゴリ選択、地域選択 |

---

## 2. Variants & States

**Variants**

| Variant | 説明 | 用途 |
|---------|------|------|
| `basic` | テキストのみのメニューアイテム | シンプルなアクション一覧 |
| `with-icon` | 左端にアイコン（`w-4 h-4 text-body`） | 設定・プロフィールメニュー |
| `grouped` | セパレータ + グループラベルで分類 | 多機能なコンテキストメニュー |
| `with-destructive` | 赤テキスト + セパレータで分離 | 削除・ログアウト等の不可逆操作 |
| `with-tag` | アイテム左端にステータスタグ | 状態（下書き・公開中等）を付帯するアクション一覧 |

**配置**: `bottom-start`（`top-full left-0 mt-1`・既定）/ `bottom-end`（`top-full right-0 mt-1`）。

**Menu Item の States**

| State | スタイル |
|-------|---------|
| Default | `text-body bg-white` |
| Hover | `text-body bg-[#edf0f3]` |
| Focus | `text-body bg-[#edf0f3] outline-none ring-2 ring-primary-700 ring-offset-0` |
| Active | `text-body bg-[#dde3eb]` |
| Disabled | `text-[#a1afc0] cursor-not-allowed` + `aria-disabled="true"` |
| Destructive | `text-[#e93766] hover:bg-[#fef2f4] active:bg-[#fccfd9]` |

---

## 3. パーツ

| パーツ | 役割 | 必須 | class（適用例） |
|--------|------|:----:|------|
| Trigger Button | 開閉を制御するボタン | Yes | Button 仕様に準拠 + `aria-haspopup="true" aria-expanded` + ChevronDown SVG（`w-4 h-4`） |
| Menu Container | メニュー全体のコンテナ | Yes | `bg-white rounded-sm border border-[#dde3eb] shadow-[0px_10px_15px_-5px_rgba(10,10,10,0.1),0px_5px_5px_-2px_rgba(10,10,10,0.05)] z-20 overflow-hidden`（`py-1` 不要） |
| Menu Item | 個々のアクション項目 | Yes | `w-full text-left px-3 py-3 text-[14px] font-medium text-body tracking-[0.28px] hover:bg-[#edf0f3] active:bg-[#dde3eb] transition-colors` |
| Leading Icon | アイテムの意味を補強 | No | `w-4 h-4 flex-shrink-0 text-body`（アイテムは `flex items-center gap-3`） |
| Tag | アイテム左端の状態タグ | No | `inline-flex items-center px-1.5 rounded bg-[#dde3eb] text-[11px] text-body leading-[1.75] flex-shrink-0`（アイテムは `flex items-center gap-1.5`） |
| Separator | グループ区切り | No | `border-t border-[#c4cdd9]`（`my-1` なし）+ `role="separator"` |
| Group Label | グループの見出し | No | `text-xs font-medium text-slate-500`（`px-3 py-1.5` のラッパー内） |

---

## 4. Composition Rules

許可: テキスト / Leading Icon / Tag / Separator / Group Label。
禁止: ホバーだけでメニューを開く（アクセシビリティ問題）/ `aria-haspopup` なしのトリガー / Separator に `role="menuitem"` を付与 / Disabled アイテムにフォーカスを移す / 確認が必要な破壊的操作を確認なしで即実行（モーダルを併用）。

**Anti-patterns**
- NG: 左端に色付きの縦アクセントバー（border-left 等）で hover/active を示す → 状態は背景色（`hover:bg-[#edf0f3]` / `active:bg-[#dde3eb]`）で示す

---

## 5. Layout & Spacing

| プロパティ | トークン | 値 |
|-----------|---------|-----|
| アイテム padding | `spacing.3` | `px-3 py-3`（12px） |
| アイコン付きアイテム gap | `spacing.3` | `gap-3` |
| タグ付きアイテム gap | `spacing.1.5` | `gap-1.5` |
| Container offset | `spacing.1` | `mt-1` |
| Container radius | `radius.sm`（4px） | `rounded-sm` |
| Menu 幅 | — | `w-48`〜`w-56`（用途に応じ固定） |

Container パディングは不要（`overflow-hidden` を使用）。`py-1` は付けない。

---

## 6. Token Mapping

| 用途 | トークン |
|------|---------|
| Container 背景 | `#ffffff`（`color.surface.overlay`） |
| Container ボーダー | `#dde3eb`（`color.border.subtle`） |
| Container shadow | `elevation.shadow.md`（`0px_10px_15px_-5px...` の具体 class が実装値） |
| アイテムテキスト | `color.content.primary`（`text-body`） |
| アイテム hover / active 背景 | `#edf0f3`（`color.surface.sunken`）/ `#dde3eb`（`color.border.subtle`、active 兼用） |
| Separator | `#c4cdd9`（`color.border.default`） |
| Disabled テキスト | `#a1afc0`（`color.content.disabled`） |
| Destructive テキスト / hover / active | `color.status.danger.base`(#e93766) / `.container`(#fef2f4) / (#fccfd9) |
| focus ring | `color.border.focus`（= primary-700） |
| z-index | `elevation.z.dropdown`（20） |
| radius | `radius.sm`（4px） |

---

## 7. Accessibility

- Trigger Button: `aria-haspopup="true"` + `aria-expanded="true|false"`（必須）
- Menu Container: `role="menu"` / Menu Item: `role="menuitem"` + `tabindex="-1"`（roving tabindex）
- Separator: `role="separator"` / Disabled Item: `aria-disabled="true"`
- キーボード: `Arrow Down/Up` で移動（ループ）、`Home/End` で先頭/末尾、`Enter/Space` で開閉・実行、`Escape` で閉じてトリガーへフォーカスを戻す、`Tab` で閉じて次要素へ
- 開いた直後は最初のメニューアイテムにフォーカス。破壊的アクションは赤テキスト + テキストで意図を明示

---

## 8. Content Guidelines
- アイテムラベルは動詞始まり・簡潔に（「編集」「複製」「削除」「ログアウト」）
- グループラベルでアクションを分類（「編集」「管理」）。破壊的アクションはセパレータで分離して最下部に

---

## 9. Usage Do / Don't

```html
<!-- Do: アイコン付き + セパレータ + 破壊的アクション -->
<div class="relative inline-block">
  <button type="button" aria-haspopup="true" aria-expanded="false" onclick="toggleMenu('icon-menu', this)"
    class="inline-flex items-center gap-1 h-9 px-3 text-[14px] font-medium tracking-[0.28px] bg-white text-primary-700 border border-primary-700 rounded hover:bg-primary-50 active:bg-primary-100 cursor-pointer">
    アカウント
    <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 9l-7 7-7-7"/></svg>
  </button>
  <div id="icon-menu" role="menu" class="hidden absolute top-full left-0 mt-1 w-56 bg-white rounded-sm border border-[#dde3eb] shadow-[0px_10px_15px_-5px_rgba(10,10,10,0.1),0px_5px_5px_-2px_rgba(10,10,10,0.05)] z-20 overflow-hidden">
    <button role="menuitem" tabindex="-1" class="w-full flex items-center gap-3 px-3 py-3 text-[14px] font-medium text-body tracking-[0.28px] hover:bg-[#edf0f3] active:bg-[#dde3eb] transition-colors">
      <svg class="w-4 h-4 flex-shrink-0 text-body" fill="none" stroke="currentColor" viewBox="0 0 24 24" aria-hidden="true"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M16 7a4 4 0 11-8 0 4 4 0 018 0zM12 14a7 7 0 00-7 7h14a7 7 0 00-7-7z"/></svg>
      プロフィール
    </button>
    <div role="separator" class="border-t border-[#c4cdd9]"></div>
    <button role="menuitem" tabindex="-1" class="w-full flex items-center gap-3 px-3 py-3 text-[14px] font-medium tracking-[0.28px] text-[#e93766] hover:bg-[#fef2f4] active:bg-[#fccfd9] transition-colors">
      <svg class="w-4 h-4 flex-shrink-0" fill="none" stroke="currentColor" viewBox="0 0 24 24" aria-hidden="true"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M17 16l4-4m0 0l-4-4m4 4H7m6 4v1a3 3 0 01-3 3H6a3 3 0 01-3-3V7a3 3 0 013-3h4a3 3 0 013 3v1"/></svg>
      ログアウト
    </button>
  </div>
</div>
```

**Don't**: `rounded-lg` / `rounded-md`（→ `rounded-sm`）/ `border-slate-200`（→ `border-[#dde3eb]`）/ `py-1` のコンテナパディング（→ 不要。`overflow-hidden`）/ `hover:bg-gray-50`（→ `hover:bg-[#edf0f3]`）/ `aria-haspopup` の省略 / キーボードナビゲーション（Arrow Up/Down）の未実装 / ホバーで開閉。

---

## 10. Implementation Notes
- 開閉・キーボードナビ（Arrow/Home/End/Escape）・外部クリック閉じは `toggleMenu()` + `keydown` / `click` で実装。開時に全メニューを閉じてから最初のアイテムにフォーカス
- 表示は `transition-opacity duration-150 ease-out`（opacity 0→1）、非表示は即座
- アイコンは Lucide。確認が必要な破壊的操作は Modals を併用
- 思想は `FOUNDATIONS.md`、使い分けは `COMPONENT_GUIDE.md`
