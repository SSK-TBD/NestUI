# Component Guideline: Badge

> NestUI Badge 仕様。Tailwind CSS。値は `tokens/`（色は `color.status.*` / `color.semantic.*` 等）を正とし、以下の class はその適用例。

## 1. Component Identity
- **Name**: Badge
- **Variants**: `default`（neutral） / `primary` / `success` / `warning` / `error`（+ Dot付き / Count / Outline の表示形態）
- **Responsibility**:
  - する: ステータス・分類・カウントを短いラベルで補助的に表示する
  - しない: クリック操作・フィルタリング（→ Chip）/ 削除可能ラベル（→ Tag）/ 長文の説明
- **Target Platform**: デスクトップ Web アプリ（業務 SaaS）

**原則**: バッジは状態・分類を簡潔に伝えるラベル / 色だけで情報を伝えない（テキスト必須）/ サイズは小さく保ちメインコンテンツの邪魔をしない / 一貫したカラーコード（成功・警告・エラー・ニュートラル・アクセント）。

---

## 2. Variants & States

**Variants**（カラー）

| Variant | 用途 | class（塗り） |
|---------|------|------|
| `default`（neutral） | 中立的なラベル・未着手・カテゴリ | `bg-slate-100 text-slate-700` |
| `primary`（accent） | アクティブ・選択中・新規 | `bg-primary-50 text-primary-700` |
| `success` | 完了・成功・正常 | `bg-emerald-50 text-emerald-700`（`color.status.success.container`/`.text`） |
| `warning` | 進行中・保留・確認待ち | `bg-amber-50 text-amber-700`（`color.status.warning.container`/`.text`） |
| `error` | ブロック・失敗・危険 | `bg-red-50 text-red-700`（`color.status.danger.container`/`.text`） |

全 variant 共通: `inline-flex items-center px-3 py-1 rounded-full text-xs font-medium`

**表示形態 / States**

| 形態 | 説明 | 追加 class |
|------|------|------|
| Dot 付き | テキスト前に色付きドットを置き状態を補強（オンライン等） | `gap-1.5` + `<span class="w-1.5 h-1.5 rounded-full bg-emerald-500">` |
| Count | 通知数・未読件数を円形で表示 | `justify-center w-5 h-5 text-white bg-red-500 rounded-full`（0 は非表示検討） |
| Outline | 背景なし・ボーダーのみ。情報密度が高い画面向け | `border border-slate-300 text-slate-700`（塗りなし） |

バッジは hover / focus / disabled を持たない（非インタラクティブ）。状態変更時はカラーとテキストを同時更新する。

---

## 3. サイズ / Props

| サイズ | 高さ | padding | font |
|--------|------|---------|------|
| Medium（既定） | 20px | `px-3 py-1` | `text-xs`（11px） |
| Small | 16px | `px-2 py-0.5` | `text-xs` |
| Count（円形） | 20px | `w-5 h-5`（円形固定） | `text-xs` |

主な Props（参考）: `variant`（default/primary/success/warning/error）/ `size`（sm/md）/ `count`（99 超は `99+`）/ `label`（count と排他）/ `dot`（boolean）。

---

## 4. Composition Rules

許可: テキストラベル / Dot Indicator（`<span>`）。
禁止: バッジ内に `<button>` / `<a>` 等インタラクティブ要素。`count` と `label` の同時指定。テキストなしの色ドットのみ表示。
配置: 複数並置は `flex flex-wrap gap-2`。アイコンボタン・ナビアイテムへ重ねる場合は `position: absolute`。

---

## 5. Layout & Spacing

- 角丸は `rounded-full` 固定（`rounded-lg` / `rounded-md` 禁止）
- テキストは最大2〜3語（12文字以内目安）。超過はツールチップで補足
- Count が 0 のときはレンダリングしない（親で条件分岐）

---

## 6. Token Mapping

| Variant | 背景 | テキスト |
|---------|------|---------|
| default | `color.surface.sunken` 近傍（slate-100） | `color.content.secondary`（slate-700 近傍） |
| primary | `color.primary.50` | `color.primary.700` |
| success | `color.status.success.container`(#F2FBEA) | `color.status.success.text`(#376F1C) |
| warning | `color.status.warning.container`(#FEF8EE) | `color.status.warning.text`(#B95415) |
| error | `color.status.danger.container`(#FEF2F4) | `color.status.danger.text`(#B51B4E) |
| count 背景 | `color.status.danger.base`(#E93766) | `color.content.inverse`（white） |

> 角丸: `radius.full`。Dot サイズ 8×8px（`radius.full`）。

---

## 7. Accessibility
- **Role**: 動的更新されるカウントバッジは `role="status"`
- 数値カウント: `aria-label="3件の通知"` 等（数値のみでは文脈不明）
- Dot バッジ: `aria-label="オンライン"` 等を必須
- 色だけで状態を伝えない（テキスト/アイコン併用）。コントラストは背景とテキストで 4.5:1 以上

## 8. Content Guidelines
- ステータスラベルは体言止め（「実行中」「完了」「エラー」）
- カウントは `99+` で上限を表現
- `label` は最大12文字以内

## 9. Usage Do / Don't

```html
<!-- Do: ステータス（カラーコード） -->
<span class="inline-flex items-center px-3 py-1 rounded-full text-xs font-medium bg-emerald-50 text-emerald-700">完了</span>

<!-- Do: ドット付き -->
<span class="inline-flex items-center gap-1.5 px-3 py-1 rounded-full text-xs font-medium bg-emerald-50 text-emerald-700">
  <span class="w-1.5 h-1.5 rounded-full bg-emerald-500"></span>オンライン
</span>

<!-- Do: カウント -->
<span class="inline-flex items-center justify-center w-5 h-5 text-xs font-medium text-white bg-red-500 rounded-full" aria-label="3件の通知">3</span>

<!-- Do: アウトライン -->
<span class="inline-flex items-center px-3 py-1 rounded-full text-xs font-medium border border-slate-300 text-slate-700">下書き</span>
```

**Don't**: バッジをクリッカブルにする（→ Chip）/ 色のみでステータス伝達 / `rounded-lg`・`rounded-md`（→ `rounded-full`）/ 長文テキスト / 削除ボタン付与（→ Tag）/ `count` と `label` 同時指定。

---

## 10. Implementation Notes
- アイコンは Lucide。ステータス色は `color.status.*` / `color.semantic.*` から選ぶ
- 削除可能ラベルは `Tag.md`、ステータス選択トリガーは `Status tag.md`、フィルター/選択は Chip を参照
- 思想は `FOUNDATIONS.md`
