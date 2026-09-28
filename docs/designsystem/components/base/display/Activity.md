# Component Guideline: Activity

> NestUI Activity（アクティビティパネル）仕様。Tailwind CSS。値は `tokens/` を正とし、以下の class はその適用例。オブジェクト詳細ドロワーに配置し、コメント投稿・ピン留め・システムログを一元表示する。

## 1. Component Identity
- **Name**: Activity
- **Variants**: 構成パーツ — `ActivityTabs` / `CommentItem`（通常・ピン留め） / `LogItem` / `MentionTag` / `CommentInput`（Memo / Message）
- **Responsibility**:
  - する: 案件・物件・契約等のオブジェクトに紐づくコメント・変更履歴をチームで共有し、@メンションで通知する
  - しない: 受信トレイ型のメッセージ送受信単体（→ inbox パターン）/ 汎用チャット / 通知センター
- **Target Platform**: デスクトップ Web アプリ（業務 SaaS）

**使い分け**: コメント（Memo）= チームへの申し送り・状況メモ・質問（@メンションで通知可）/ ログ（Log）= 担当変更・ステップ変更などシステムが自動生成する変更履歴（ユーザーは投稿不可・ピン留め不可）/ ピン留め = 重要なコメントを上位表示。

---

## 2. Variants & States

**ActivityTabs**（タブフィルター）

| タブ | 表示内容 |
|------|----------|
| すべて | CommentItem + LogItem を時系列表示 |
| コメント | CommentItem のみ |
| ピン留め | PinBanner 付き CommentItem のみ |
| ログ | LogItem のみ |

**CommentItem のバリアント**

| バリアント | 背景色 | PinBanner |
|------------|--------|-----------|
| 通常 | `bg-white` | なし |
| ピン留め | `bg-[#f0f7fe]` | `ピン留めしたコメント`（primary-700） |

**CommentInput のバリアント / States**

| type | placeholder | 用途 |
|------|-------------|------|
| Memo | コメントを追加する... | アクティビティパネル内 |
| Message | メッセージを入力 | 受信トレイ型 2カラム詳細 |

| state | Area ボーダー | 投稿ボタン |
|-------|---------------|-----------|
| Enable / Hover（未フォーカス） | `#c4cdd9` | Disabled（`#edf0f3` bg / `#a1afc0` text） |
| Selected / Focus | `#2661cf`（`focus-within:border-[#2661cf]`） | Disabled |
| Fill / Typing（入力済み） | `#c4cdd9` / `#2661cf` | Active（`bg-primary-700 text-white`） |
| Template（テンプレート選択済み） | `#c4cdd9` + TemplateBlock 表示 | Active |

アクションボタン（Pin/編集/削除）は CommentItem ホバー時のみ表示（`group-hover`）。LogItem にはアクションボタンなし。

---

## 3. サイズ / Props（主要パーツ）

| パーツ | 主な class |
|--------|-----------|
| ActivityTabs | `flex border-b border-[#dde3eb]` + 各タブ `h-[40px] px-4`（選択: `border-b-2 border-primary-700 text-primary-700` / 未選択: `text-body`） |
| CommentItem | `relative group bg-white border-b border-[#dde3eb] px-4 py-3`（ピン留めは `bg-[#f0f7fe]`） |
| アバター | `w-6 h-6 rounded-full` + `role="img"` `aria-label` |
| 名前 | `text-[14px] font-bold text-[#081a27] leading-[1.5]` / 日時 `text-[12px] font-medium text-[#6f757d]` |
| 本文 | `pl-[32px]`（アバター分インデント）`text-[14px] text-[#081a27] leading-[1.75]` |
| アクションボタン | `w-7 h-7 inline-flex items-center justify-center bg-[#f7f9fb] rounded-[2px]`（削除は `hover:bg-[#fef2f4] text-[#e93766]`） |
| LogItem | `bg-white border-b border-[#dde3eb] pl-[52px] pr-4 py-3`（アバターなし・本文 `text-[#5a6c7f]`） |
| MentionTag | `inline-flex items-center gap-[2px] bg-[#d8f0f5] text-[#2661cf] px-[4px] py-[2px] rounded-[4px] text-[11px] font-medium tracking-[0.22px]` |
| CommentInput Area | `border border-[#c4cdd9] rounded-[4px] bg-white p-1 focus-within:border-[#2661cf]` |
| 投稿ボタン | `h-7 px-3 rounded-[2px]`（Disabled: `bg-[#edf0f3] text-[#a1afc0]` / Active: `bg-primary-700 text-white`） |

---

## 4. Composition Rules

許可: ActivityTabs + （CommentItem | LogItem）リスト + CommentInput。CommentInput ツールバー: テンプレート / 添付 / @メンション の各 IconButton。
禁止: LogItem にアクションボタン（ピン留め・編集・削除）を配置 / アクションボタンを常時表示（`group-hover` で制御）/ `<input type="text">` でコメント入力（→ `<textarea>`）/ MentionTag に `border-l-4` のカラーバー。
配置: パネル全体は `flex flex-col h-full`（タブ → スクロール領域 `flex-1 overflow-y-auto` → CommentInput）。本文はアバター分 `pl-[32px]` でインデント。

---

## 5. Layout & Spacing

- 各アイテムは下ボーダー `border-b border-[#dde3eb]` で区切る
- アバター（`w-6 h-6`）+ 本文インデント `pl-[32px]`、LogItem は `pl-[52px]`（アバター位置に揃える）
- CommentInput はパネル下部に `border-t border-[#dde3eb]` で固定

### @メンション

1. 入力中に `@` 入力、またはツールバーの `@` ボタンで候補リスト（メンバー Dropdown）を表示
2. 候補選択で MentionTag をテキスト内に挿入
3. 投稿後、メンションされたメンバーの受信トレイに通知

---

## 6. Token Mapping

| 用途 | トークン |
|------|---------|
| 区切りボーダー | `color.border.subtle`(#DDE3EB) |
| ピン留め背景 / PinBanner | `color.semantic.infoBg`(#F0F7FE) / `color.primary.700` |
| 名前テキスト | `color.content.primary`(#081A27) |
| 日時・編集済みラベル | `#6f757d`（`color.content.secondary` 近傍） |
| LogItem 本文 | `color.content.secondary`(#5A6C7F) |
| MentionTag 背景 / 文字 | `color.brand.100` 近傍(#D8F0F5) / `color.primary.700`(#2661CF) |
| 削除ボタン hover / text | `color.status.danger.container`(#FEF2F4) / `color.status.danger.base`(#E93766) |
| 投稿ボタン Active / Disabled | `color.primary.700` / `color.surface.sunken`・`color.content.disabled` |
| Pin IsSelected | `#ddedfc`（primary-100 近傍）+ `border-[#2661cf]` |
| 角丸 | `radius.sm`（4px）/ ボタン 2px |

---

## 7. Accessibility

| 項目 | 要件 |
|------|------|
| タブリスト | `role="tablist"` / 各タブ `role="tab"` + `aria-selected` + `aria-controls` |
| タブパネル | `role="tabpanel"` + `aria-labelledby` |
| アクションボタン | 各 IconButton に `aria-label="ピン留め"` 等。ピン ON 時は `aria-pressed="true"` |
| MentionTag | `role="mark"` または `aria-label="@名前 をメンション"` |
| CommentInput | `<textarea>` に `aria-label="コメントを追加"` |
| 投稿ボタン Disabled | `disabled` + `aria-disabled="true"` |
| LogItem | `role="listitem"`。変更内容を読み上げる（色だけで伝達しない） |
| メンション候補リスト | `aria-expanded` を付与して表示 |

## 8. Content Guidelines
- LogItem は「担当者を A から B に変更しました」のように変更内容を明示
- 編集済みコメントは「（編集済み | 日時）」を本文下に表示
- ピン留めは PinBanner テキストを必ず表示（アイコン色だけで伝えない）

## 9. Usage Do / Don't

```html
<!-- Do: ActivityTabs -->
<div role="tablist" aria-label="アクティビティフィルター" class="flex border-b border-[#dde3eb]">
  <button role="tab" aria-selected="true" aria-controls="tab-all" class="h-[40px] px-4 inline-flex items-center justify-center relative border-b-2 border-primary-700 text-[14px] font-medium text-primary-700 tracking-[0.28px] hover:bg-primary-50 cursor-default outline-none focus-visible:ring-2 focus-visible:ring-primary-700 focus-visible:ring-offset-1">すべて</button>
  <button role="tab" aria-selected="false" aria-controls="tab-comment" class="h-[40px] px-4 inline-flex items-center justify-center relative text-[14px] font-medium text-body tracking-[0.28px] hover:bg-primary-50 cursor-pointer outline-none focus-visible:ring-2 focus-visible:ring-primary-700 focus-visible:ring-offset-1">コメント</button>
</div>

<!-- Do: CommentItem（通常・MentionTag を含む） -->
<div class="relative group bg-white border-b border-[#dde3eb] px-4 py-3" role="listitem">
  <div class="absolute right-4 top-1 flex items-center gap-1 opacity-0 group-hover:opacity-100 transition-opacity">
    <button aria-label="ピン留め" class="w-7 h-7 inline-flex items-center justify-center bg-[#f7f9fb] rounded-[2px] hover:bg-[#edf0f3] cursor-pointer">
      <svg class="w-4 h-4" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" aria-hidden="true"><path d="m12 17-1-4H7l1-4h8l1 4h-4l-1 4z"/><path d="M12 17v4"/><path d="M8 9V5"/><path d="M16 9V5"/><path d="M8 5h8"/></svg>
    </button>
    <button aria-label="削除" class="w-7 h-7 inline-flex items-center justify-center bg-[#f7f9fb] rounded-[2px] hover:bg-[#fef2f4] text-[#e93766] cursor-pointer">
      <svg class="w-4 h-4" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" aria-hidden="true"><polyline points="3 6 5 6 21 6"/><path d="M19 6l-1 14a2 2 0 0 1-2 2H8a2 2 0 0 1-2-2L5 6"/><path d="M10 11v6"/><path d="M14 11v6"/><path d="M9 6V4h6v2"/></svg>
    </button>
  </div>
  <div class="flex flex-col gap-1 w-full">
    <div class="flex items-center gap-2">
      <div class="w-6 h-6 rounded-full bg-[#ee8c29] flex items-center justify-center flex-shrink-0" role="img" aria-label="サンプル 太郎"><span class="text-white text-[12px] font-medium leading-none">サ</span></div>
      <span class="text-[14px] font-bold text-[#081a27] leading-[1.5] whitespace-nowrap">サンプル 太郎</span>
      <span class="text-[12px] font-medium text-[#6f757d] tracking-[0.24px] whitespace-nowrap">2024/12/24 14:13</span>
    </div>
    <div class="pl-[32px] flex flex-col gap-1">
      <p class="text-[14px] text-[#081a27] leading-[1.75]">
        <span role="mark" aria-label="サンプル 花子 をメンション" class="inline-flex items-center gap-[2px] bg-[#d8f0f5] text-[#2661cf] px-[4px] py-[2px] rounded-[4px] text-[11px] font-medium tracking-[0.22px] leading-[1.3] whitespace-nowrap"><span aria-hidden="true">@</span>サンプル 花子</span>
        さん、〇〇の件よろしくお願いします。
      </p>
    </div>
  </div>
</div>

<!-- Do: LogItem（アバターなし・アクションボタンなし） -->
<div class="bg-white border-b border-[#dde3eb] pl-[52px] pr-4 py-3" role="listitem">
  <div class="flex items-center gap-[6px]">
    <span class="text-[14px] font-bold text-[#3b3f45] leading-[1.5] whitespace-nowrap">サンプル 太郎</span>
    <span class="text-[12px] font-medium text-[#6f757d] tracking-[0.24px] whitespace-nowrap">2024/12/23 09:52</span>
  </div>
  <p class="text-[14px] text-[#5a6c7f] leading-[1.75]">担当者を サンプル 太郎 から サンプル 花子 に変更しました</p>
</div>
```

**Don't**: LogItem にアクションボタンを配置 / アクションボタンを常時表示（→ `group-hover`）/ MentionTag に `border-l-4` のカラーバー / 投稿ボタンを未入力時にアクティブ（`bg-primary-700`）にする / ピン留めをアイコン色だけで伝える（PinBanner 必須）/ `<input type="text">` で入力欄を実装（→ `<textarea>`）/ メンション候補リストを `aria-expanded` なしで表示。

---

## 10. Implementation Notes
- 依存コンポーネント: Tabs（ActivityTabs）/ `Avatar.md`（AccountIcon、`w-6 h-6`）。アイコンは Lucide（`w-4 h-4 stroke="currentColor" fill="none"`）
- テンプレート選択時は TemplateBlock（青バナー `bg-[#f0f7fe] border border-[#2661cf]`）を入力エリア上部に表示し、投稿ボタンをアクティブに。× で解除
- タブ切替・投稿ボタン状態は JS で制御（`switchTab` / `handleCommentInput`）。受信トレイ型 2カラムは inbox パターンを参照
