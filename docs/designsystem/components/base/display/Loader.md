# Component Guideline: Loader

> NestUI Loader（= Spinner）仕様。Tailwind CSS / 専用 CSS クラス。値は `tokens/`（アーク色は `color.brand.500`）を正とし、以下の class はその適用例。

## 1. Component Identity
- **Name**: Loader（Spinner）
- **Variants**: スタンドアロン `ds-spinner`（S / M / L / XL の4サイズ） / インライン `inline-spinner`（ボタン内）
- **Responsibility**:
  - する: 形状が不明な非同期処理中・読み込み中を回転インジケーターで示す
  - しない: コンテンツ形状が明確な場合（→ Skeleton）/ 進捗が計測可能な処理（→ Progress）
- **Target Platform**: デスクトップ Web アプリ（業務 SaaS）

**原則**: Brand（アーク色は Brand Blue `#0098B9` = `brand-500` で統一）/ Minimal（「何かが起きている」ことだけを伝える。テキストは原則不要）/ Accessible（`role="status"` + `aria-label="読み込み中"` 必須）/ Context-adaptive（ボタン内は `inline-spinner` で `currentColor` に追従）。

---

## 2. Variants & States

**Variants**

| 種別 | 用途 | 実装 |
|------|------|------|
| スタンドアロン `ds-spinner` | カード・パネル・モーダル中央、テーブルリフレッシュ等の単体ローディング | `div.ds-spinner.ds-spinner-{s\|m\|l\|xl}` + `role="status"` |
| インライン `inline-spinner` | ボタンの loading 状態専用。Contained / Outlined / Neutral 問わず自動適応 | `<div class="inline-spinner">` + 親 `<button disabled>` |

**パーツ**: Track（リング背景 `#dde3eb`）/ Arc（回転する弧 `#0098B9` = brand-500）/ Container（サイズ指定 + `role="status"`）。

**States**: loading（回転アニメーション 0.75s linear infinite）。`prefers-reduced-motion` で自動停止。

---

## 3. サイズ / Props

| サイズ名 | 直径 | border-width | class |
|---------|------|-------------|-------|
| S（Small） | 14px | 1.5px | `ds-spinner ds-spinner-s` |
| M（Medium） | 18px | 2px | `ds-spinner ds-spinner-m` |
| L（Large） | 24px | 2.5px | `ds-spinner ds-spinner-l` |
| XL（XLarge） | 32px | 3px | `ds-spinner ds-spinner-xl` |

インライン: `width: 1em`（親のフォントサイズに追従）。主な Props（参考）: `variant`（spinner/fullPage）/ `size`（s/m/l/xl）/ `label`（aria-label、既定「読み込み中」）/ `color`（既定 currentColor）。

---

## 4. Composition Rules

許可: なし（単体表示。ラベルが必要な場合は別要素で併置）。
禁止: `ds-spinner` をボタン内で使う（サイズ固定になるため → `inline-spinner`）/ 全画面占有をスピナーのみで行う（→ Skeleton or Progress を併用）/ Spinner のみを常時表示し続ける（タイムアウト処理必須）。

---

## 5. Layout & Spacing

- スタンドアロンは中央配置（カード・モーダルは中央、テーブルはヘッダー右）
- fullPage オーバーレイ: 背景 `color.surface.base` 80% opacity、スピナーは L（または XL）、z-index は `elevation.z.modal`
- インラインは `gap-2` でラベルと並べる

### CSS

```css
@keyframes loader-spin { to { transform: rotate(360deg); } }

/* スタンドアロン */
.ds-spinner {
  display: inline-block; flex-shrink: 0; border-radius: 50%;
  border-style: solid; border-color: #dde3eb; border-top-color: #0098B9;
  animation: loader-spin 0.75s linear infinite;
}
.ds-spinner-s  { width: 14px; height: 14px; border-width: 1.5px; }
.ds-spinner-m  { width: 18px; height: 18px; border-width: 2px; }
.ds-spinner-l  { width: 24px; height: 24px; border-width: 2.5px; }
.ds-spinner-xl { width: 32px; height: 32px; border-width: 3px; }

/* インライン（ボタン内） */
.inline-spinner {
  width: 1em; aspect-ratio: 1; border-radius: 50%; flex-shrink: 0;
  border: 2px solid transparent; border-top-color: currentColor;
  animation: loader-spin 0.75s linear infinite;
}
```

`prefers-reduced-motion: reduce` でアニメーションは自動停止する。

---

## 6. Token Mapping

| 用途 | トークン |
|------|---------|
| Arc（弧の色） | `color.brand.500`(#0098B9) |
| Track（リング背景） | `color.border.subtle`(#DDE3EB) |
| インライン色 | `currentColor`（親要素のテキスト色に追従） |
| fullPage 背景 | `color.surface.base` 80% opacity |
| z-index（fullPage） | `elevation.z.modal` |

---

## 7. Accessibility

| 属性 | 値 | 必須 |
|------|-----|------|
| `role` | `"status"` | 必須 |
| `aria-label` | `"読み込み中"`（処理内容があれば「保存中...」等） | 必須 |
| `aria-live` | `"polite"` | 推奨 |
| ボタン内スピナー | 親 `<button>` に `disabled` | 必須 |
| SR-only 補足 | ページ全体 loading 時は `<span class="sr-only">読み込み中</span>` | 推奨 |

- 非テキストコントラスト: スピナー色 vs 背景 3:1 以上

## 8. Content Guidelines
- `label` は処理内容を示す（「データを読み込み中...」「保存中...」）
- fullPage 時はスピナー下部にテキストを視覚的にも表示する

## 9. Usage Do / Don't

```html
<!-- Do: スタンドアロン（L・中央配置） -->
<div class="ds-spinner ds-spinner-l" role="status" aria-label="読み込み中"></div>

<!-- Do: ボタン内（Inline） -->
<button disabled class="inline-flex items-center gap-2 h-9 px-3 text-[14px] font-medium text-white bg-primary-700 rounded opacity-60 cursor-not-allowed">
  <div class="inline-spinner"></div>送信中...
</button>
```

**使い分け**: ボタン操作後＝`inline-spinner` / カード・パネル＝`ds-spinner-l` / ページ初期ロード＝Skeleton 優先 / テーブルリフレッシュ＝`ds-spinner-m` / モーダル内＝`ds-spinner-l`。

**Don't**: `role="status"` 省略 / `aria-label` 省略 / `ds-spinner` をボタン内で使用 / 全画面をスピナーのみで占有 / Spinner を常時表示し続ける（タイムアウト必須）/ コンテンツ形状が明確な場合に使用（→ Skeleton）。

---

## 10. Implementation Notes
- CSS アニメーション（`@keyframes loader-spin`）で実装。JS より軽量
- コンテンツ形状が明確なら `Skeleton.md`、進捗が計測可能なら `Progress.md` を参照
