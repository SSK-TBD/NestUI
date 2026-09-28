# Component Guideline: Alerts

> NestUI Alert 仕様。Tailwind CSS。値は `tokens/`（色は `color.semantic.*` / `color.status.*` 等）を正とし、以下の class はその適用例。

## 1. Component Identity
- **Name**: Alerts
- **Variants**: `primary`（情報・参照） / `red`（エラー・警告） / `gray`（中立・補足）
- **Responsibility**:
  - する: ページ内にインライン常駐するシステムメッセージ・状態通知を表示する。操作・ページ遷移まで残る
  - しない: 一時的なフィードバック（→ Toast）/ フォームフィールドのエラー（入力欄直下に表示）/ レイアウト容器
- **Target Platform**: デスクトップ Web アプリ（業務 SaaS）

**原則**: 常時表示（Toast と対の存在）/ 色+アイコン併用で種類を伝える（色だけで伝達しない）/ ボタンはオプション。

**Alert と Toast の違い**

|  | Alerts | Toast |
|--|--------|-------|
| 表示方式 | インライン（ページ内フロー） | オーバーレイ（`fixed`） |
| 永続性 | 操作・遷移まで残る | 自動消去（5秒） |
| 用途 | 状態・参照情報の通知 | 操作結果のフィードバック |
| 出現 | ページ読み込み時 or 状態変化時 | ユーザー操作の直後 |

---

## 2. Variants & States

**Variants**（カラー）

| Variant | 背景 | アイコン色 | ボタン種別 | role |
|---------|------|-----------|-----------|------|
| `primary` | `#f0f7fe`（primary-50） | `#2661cf` | Primary（`bg-[#2661cf] text-white`） | `status`（`aria-live="polite"`） |
| `red` | `#fef2f4`（danger container） | `#e93766` | Neutral（`bg-[#f7f9fb] text-[#3e5062]`） | `alert`（`aria-live="assertive"`） |
| `gray` | `#f7f9fb`（neutral container） | `#5a6c7f` | Neutral | `status`（`aria-live="polite"`） |

テキスト色は全カラー共通: `#081a27`（`color.content.primary`）。

**States / ボタン配置**

| 項目 | 値 |
|------|-----|
| Horizontal | ボタンをテキスト右横に並べる（`flex items-center` + ボタンに `flex-shrink-0`） |
| Vertical | ボタンをテキスト下に積む（`flex flex-col items-start`） |
| ボタンなし | アクション不要時はボタンを省略 |

ボーダーは使わない（`border-t-4` / `border-l-4` のカラーバーも全周ボーダーも不可）。variant は背景色 + アイコン色で区別する。

---

## 3. サイズ / Props

| サイズ | パディング | gap | アイコン | 見出し（title） | 本文（description） | ボタン |
|--------|-----------|-----|---------|----------------|--------------------|--------|
| Medium | `px-4 py-3` | `gap-3` | `w-6 h-6` | `text-[14px] font-bold leading-6 tracking-[0.03em]` | `text-[14px] leading-[1.75]` | M（`h-9` / `rounded`） |
| Small | `px-2 py-1.5` | `gap-1` | `w-5 h-5` | `text-[11px] font-bold leading-5 tracking-[0.03em]` | `text-[11px] leading-[1.75]` | S（`h-7` / `rounded-sm`） |

**見出しと本文のタイポ**
- 見出しと本文は**同じサイズ**にし、差は**ウェイトだけ**でつける（見出し `bold` 700 / 本文 `regular` 400）。Alert は画面に残り、本文に「なぜできないか」が書かれるので、本文を小さくして読みにくくしない（本文を 1 段小さくする Toast とはここが違う）
- 見出しの行の高さはアイコンと同じにする（Medium 24px / Small 20px）。アイコンと見出し行の中心がそろう
- `bold` にするのは**見出しと本文の両方があるときの見出しだけ**。本文だけ・見出しだけの 1 要素の Alert は `regular` で書く
- `semibold`(600) は使わない（FOUNDATIONS のウェイト規定に従う）

**縦方向のそろえ**
- テキストが 1 行: `items-center`
- 見出し + 本文の 2 行以上: `items-start`。アイコンは見出し行にそろえ、横並びのボタン・リンクは `self-center` で縦中央に置く

主な Props（参考）: `variant`（primary/red/gray）/ `size`（md/sm）/ `title`/`description` / `action`（ボタン）/ `layout`（horizontal/vertical）/ `icon`（省略時は variant 標準アイコン）。

---

## 4. Composition Rules

許可（テキスト内）: テキスト / `<a>` リンク / `<strong>` 強調 / アクションボタン。
禁止: フォーム要素 / `<Alert>` の入れ子 / アイコンなしのアラート（色だけで伝達不可）。
配置: 関連コンテンツの直前（フォーム上部・セクション先頭）。複数並べる場合は `space-y-3`。

**Anti-patterns**
- NG: 左端に色付きの縦アクセントバー（border-left 等）を付ける → variant は背景色とアイコン色で区別する

---

## 5. Layout & Spacing

- ページコンテンツのフロー内にインラインで配置
- 同種の Alert を同一ページに重複表示しない（3個以上の積み重ね禁止）
- 角丸は `rounded`（Medium）/ `rounded-sm`（Small ボタン）。`rounded-lg` 禁止

> variant の識別は 背景色 + アイコン色 で行う。左ボーダー（左端の縦アクセントバー）は使わない。

---

## 6. Token Mapping

| 用途 | トークン |
|------|---------|
| primary 背景 / アイコン | `color.semantic.infoBg`(#F0F7FE) / `color.primary.700`(#2661CF) |
| red 背景 / アイコン | `color.status.danger.container`(#FEF2F4) / `color.status.danger.base`(#E93766) |
| gray 背景 / アイコン | `color.surface.base`(#F7F9FB) / `color.content.secondary`(#5A6C7F) |
| テキスト（共通） | `color.content.primary`(#081A27) |
| 見出し / 本文のウェイト | `typography.fontWeight.bold`(700) / `typography.fontWeight.regular`(400) |
| 見出しの字間 | `typography.letterSpacing.title`(0.03em) |
| primary ボタン | `color.primary.700` 背景 / `color.content.inverse` |
| red・gray ボタン | `color.surface.base` / `#3e5062`（content 補助） |
| padding / radius | `spacing.4`/`spacing.3` / `radius.sm`(rounded) |

---

## 7. Accessibility
- **Role**: `status`（primary/gray・情報）/ `alert`（red・エラー緊急）
- `aria-live`: red=`assertive` / primary・gray=`polite`（省略禁止）
- ボタンラベルは操作内容を明示（「参照を解除」「詳細を確認」）
- 色だけで種類を伝達しない（アイコン必須）。テキスト・アイコンは WCAG AA (4.5:1)

## 8. Content Guidelines

文言は **見出し（`title`）/ 本文（`description`）/ 対応方法（`action`）** の3要素で書く。

- **title**: 結果を端的に。語尾は「〜できません」（禁止・失敗）／「〜を確認してください」（要修正）の2型。15字程度まで。**「エラー」「失敗」は使わない**（想定外系のみ「エラーが発生しました」を定型で使う）。句点は付けない
- **description**: 「なぜダメか」を1文で書く。具体名（部署名・金額など）は差し込まず固定文言にする。専門用語・内部名称は避ける。句点は 1 文なら付けない／2 文なら切れ目のみ
- **action**: 次に取れる行動を1つ。遷移先の画面が実在する場合のみボタン・リンクにする。遷移先がない・未確定なら文言のみとし `action` を省略する
  - **ボタンで出す場合は体言止め**（「使用中の請求・支払を確認」）
  - **テキストリンクで出す場合は動詞で終える**（「使用中の請求・支払を確認する」）── §1 の例外。「〜はこちら」は使わない
- フォーム送信後の BE エラーは**フォーム上部に `red` を1つ置いて集約する**。フィールド単位に散らさない

- 見出しと本文の見た目の差（ウェイト・サイズ）は §3 の「見出しと本文のタイポ」に従う

## 9. Usage Do / Don't

```html
<!-- Do: Primary・Medium・Horizontal（ボタンあり） -->
<div role="status" aria-live="polite" class="flex items-center gap-3 px-4 py-3 bg-[#f0f7fe] rounded">
  <svg class="w-6 h-6 text-[#2661cf] flex-shrink-0" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true">
    <circle cx="12" cy="12" r="10"/><path d="M12 16v-4"/><path d="M12 8h.01"/>
  </svg>
  <p class="flex-1 text-[14px] leading-[1.75] text-[#081a27]">物件情報を参照しています。新規登録する場合は「物件名」から参照を解除してください。</p>
  <button class="inline-flex items-center justify-center gap-1 h-9 px-3 text-[14px] font-medium tracking-[0.28px] bg-[#2661cf] text-white rounded flex-shrink-0 hover:bg-primary-600 active:bg-primary-800 cursor-pointer">参照を解除</button>
</div>

<!-- Do: Red・Medium（見出し + 本文 + 対応方法） -->
<div role="alert" aria-live="assertive" class="flex items-start gap-3 px-4 py-3 bg-[#fef2f4] rounded">
  <svg class="w-6 h-6 text-[#e93766] flex-shrink-0" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true">
    <circle cx="12" cy="12" r="10"/><line x1="12" y1="8" x2="12" y2="12"/><line x1="12" y1="16" x2="12.01" y2="16"/>
  </svg>
  <div class="flex-1">
    <p class="text-[14px] font-bold leading-6 tracking-[0.03em] text-[#081a27]">削除できません</p>
    <p class="text-[14px] leading-[1.75] text-[#081a27]">この部署は請求・支払で使用されているため、削除できません</p>
  </div>
  <a href="#" class="text-[14px] font-medium leading-[1.75] tracking-[0.28px] text-primary-700 underline hover:text-primary-600 transition-colors flex-shrink-0 self-center">使用中の請求・支払を確認する</a>
</div>
```

**Don't**: `border-l-4` / `border-t-4` のカラーバー・全周ボーダー（→ ボーダーなし。背景色 + アイコン色で区別）/ `bg-red-50`・`bg-amber-50` 等の汎用カラー（→ トークンの HEX）/ `rounded-lg`（→ `rounded`）/ アイコンなし表示 / `aria-live` 省略 / Warning・Info タイプ（→ Primary / Red / Gray の3カラーのみ）/ Toast の代わりに使う（一時通知は Toast）/ 見出しに「エラー」「失敗」を使う（→ §8）/ 見出しを `semibold` にする・本文を見出しより小さくする（→ §3。差はウェイトだけ）/ 1文の本文の末尾に句点を付ける。

---

## 10. Implementation Notes
- アイコンは Lucide（stroke ベース・`Info` / `AlertCircle` 等）。種類はコンテキストで選択
- `role="alert"` のコンテンツ変更は SR が自動読み上げ。ページ遷移後に表示する場合は `aria-live` を確認
- 一時通知は `Toast.md`、確認ダイアログは Modal を参照

---

## 11. Change Log & Deprecation

| Version | 変更内容 | 対応期限 |
|---------|---------|---------|
| v1.0 | 初期リリース | — |
| v1.1 | 左ボーダーを廃止。背景色＋アイコン色で区別する方式へ統一 | — |
| v1.2 | Content Guidelines を見出し・本文・対応方法の 3 要素の型に揃え、Do 例を3要素構成に変更 | — |
| v1.3 | 見出し行のタイポを確定（本文と同サイズ・bold 700。bold は見出しと本文が両方あるときだけ）。2 行以上は `items-start` にそろえる | — |
