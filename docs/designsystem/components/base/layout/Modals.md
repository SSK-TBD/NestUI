# Component Guideline: Modals

> NestUI Modal 仕様。Tailwind CSS。値は `tokens/`（色は `color.surface.*` 等）を正とし、以下の class はその適用例。

## 1. Component Identity
- **Name**: Modals
- **Variants**: `confirm`（確認）/ `destroy`（破壊的確認）/ `form`（フォーム）/ `alert`（アラート）
- **Responsibility**:
  - する: ユーザーの注意を要する確認・フォーム・通知をオーバーレイ上に表示する
  - しない: 一時的な通知（Toast を使う）/ サイドパネル・詳細表示（Drawer を使う）/ インライン補足（Callout / Popover を使う）
- **Target Platform**: デスクトップ Web アプリ（業務 SaaS）

**原則**: 中断を最小限に（インラインで解決できる操作には使わない）/ 1 モーダル = 1 タスク（目的を絞る）/ 脱出経路を常に提供（×ボタン・オーバーレイクリック・Escape の3経路。alert はオーバーレイクリック無効）/ コンテキストの保持（閉じても背面のスクロール位置・入力途中データを維持）。

---

## 2. Variants & States

**Variants**

| Variant | role | 用途 | 主ボタン |
|---------|------|------|---------|
| `confirm`（確認） | `dialog` | 通常の確認 | Contained（CTA） |
| `destroy`（破壊的確認） | `dialog` | 削除など破壊的操作の最終確認 | Danger（アウトライン型 `text-[#e93766] border border-[#e93766]`。フィル `bg-red-*` 禁止） |
| `form`（フォーム） | `dialog` | 簡易データ入力・編集（フィールド最大5つ程度） | Contained（CTA） |
| `alert`（アラート） | `alertdialog` | システムからの重要通知。オーバーレイクリックで閉じない | Contained（CTA） |

**サイズ**（`max-w-*`。Tailwind 標準ブレークポイント幅 `max-w-sm` 等は禁止）

| サイズ | max-width | 用途 |
|--------|-----------|------|
| Small | `max-w-[480px]` | 確認ダイアログ・シンプルなアラート |
| Medium（既定） | `max-w-[640px]` | フォーム入力・標準コンテンツ |
| Large | `max-w-[800px]` | 複雑なフォーム・プレビュー |
| Full | `max-w-[1024px]` | テーブル・大量データ |

**States**: closed（非表示）/ open（表示・フォーカストラップ有効）/ loading（フッターボタンにスピナー追加・操作不可。ラベルは消さない）。

---

## 3. パーツ

| パーツ | 要素 | 必須 | class（適用例） |
|--------|------|:----:|------|
| Overlay | `<div>` | Yes | `fixed inset-0 bg-black/50 z-40`。クリックで閉じる（alert は無効化） |
| Dialog | `<div role="dialog">` | Yes | `fixed inset-0 flex items-center justify-center z-50 p-4` + 本体 `bg-white rounded-[8px] w-full max-w-[640px]`。`aria-modal="true"` |
| Header | 見出し + ×ボタン | Yes | `flex gap-4 items-center pt-5 pb-3 px-6`（ヘッダーに border なし）。見出し `text-[21px] font-bold leading-[1.5] tracking-[0.63px] text-[#081a27]` |
| ×ボタン | `<button>` | Yes | `w-6 h-6 inline-flex items-center justify-center text-[#3e5062] hover:text-[#081a27] rounded-sm focus:ring-2 focus:ring-primary-700 focus:ring-offset-1` + `aria-label="閉じる"` |
| Body | 自由コンテンツ | Yes | `px-6 pb-2`。本文 `text-[14px] text-[#3e5062] leading-[1.75]` |
| Footer | アクションボタン群 | No | `flex gap-3 items-center border-t border-[#c4cdd9] px-4 py-3`（フッター上部に border あり） |

ボタン高さは `h-9`（36px）。詳細は `base/navigation/Buttons.md` を参照。

---

## 4. Composition Rules

許可: 任意のコンテンツ / Footer のボタン群。
配置（フッター）: Tertiary（Neutral・補助）が左端、Secondary（Outlined Primary）+ Primary（CTA）が `flex flex-1 gap-3 justify-end` で右寄せ。確認系は `Subtle/Neutral → Contained`、破壊系は `Neutral → Danger`。
禁止: モーダルのネスト（3層以上禁止・最大2層、背面は `pointer-events-none`）/ `title` の省略 / `alert` でオーバーレイクリックを有効化 / `bg-red-500` のフィル型 Danger（→ アウトライン型）/ `max-w-sm` `max-w-lg` 等の Tailwind 標準幅。

---

## 5. Layout & Spacing

| プロパティ | トークン | 値 |
|-----------|---------|-----|
| Header padding | `spacing.5` / `spacing.3` / `spacing.6` | `pt-5 pb-3 px-6` |
| Body padding | `spacing.6` / `spacing.2` | `px-6 pb-2` |
| Footer padding | `spacing.4` / `spacing.3` | `px-4 py-3` |
| Header 内 gap | `spacing.4` | `gap-4` |
| Footer 内 gap | `spacing.3` | `gap-3` |
| Dialog radius | `radius.*`（8px） | `rounded-[8px]` |
| Dialog padding（外） | `spacing.4` | `p-4`（ビューポート余白） |
| Footer border-top | — | `border-t border-[#c4cdd9]` |

---

## 6. Token Mapping

| 用途 | トークン |
|------|---------|
| Overlay | `rgba(0,0,0,0.5)`（`bg-black/50`） |
| Dialog 背景 | `#ffffff`（`color.surface.overlay`） |
| タイトルテキスト | `#081a27`（`color.content.primary`） |
| 本文テキスト | `#3e5062`（`color.content.secondary` 近傍） |
| ×ボタン / Footer border | `#3e5062`（`color.content.secondary`）/ `#c4cdd9`（`color.border.default`） |
| Danger（destroy）テキスト・ボーダー / container | `color.status.danger.base`(#e93766) / `.container`(#fef2f4) / active(#fccfd9) |
| Overlay z / Dialog z | `elevation.z.overlay`(40) / `elevation.z.modal`(50)。2層目は 60/70 |
| Dialog radius | `radius.*`（8px） |

---

## 7. Accessibility

- Role: `role="dialog"`（通常）/ `role="alertdialog"`（アラート）
- `aria-modal="true"` を Dialog に付与 / `aria-labelledby` で Header 見出しの `id` を参照 / 補足は `aria-describedby` で Body の `id` を参照
- フォーカストラップ: モーダル内でフォーカスを循環（Tab / Shift+Tab で外に出ない）。開時に最初のフォーカス可能要素へ、閉時にトリガーへ戻す
- Escape でモーダルを閉じる（`alert` は閉じない設計も可）
- 背面スクロール: 表示中は `body` に `overflow-hidden` を付与
- トリガーボタンに `aria-haspopup="dialog"`

---

## 8. Content Guidelines
- タイトルはモーダルの目的を示す（「このアイテムを削除しますか？」「アイテムを追加」）
- Body で「何が起きるか」を具体的に説明する（破壊的操作は特に）
- フォームはフィールド数を最大5つ程度に。多い場合はページ遷移や Drawer を検討

---

## 9. Usage Do / Don't

```html
<!-- Do: 確認ダイアログ（confirm） -->
<div class="fixed inset-0 bg-black/50 z-40" onclick="closeModal('modal-confirm')"></div>
<div role="dialog" aria-modal="true" aria-labelledby="modal-confirm-title"
  class="fixed inset-0 flex items-center justify-center z-50 p-4">
  <div class="bg-white rounded-[8px] w-full max-w-[640px]">
    <div class="flex gap-4 items-center pt-5 pb-3 px-6">
      <h2 id="modal-confirm-title" class="flex-1 text-[21px] font-bold leading-[1.5] tracking-[0.63px] text-[#081a27]">このアイテムを削除しますか？</h2>
      <button aria-label="閉じる" onclick="closeModal('modal-confirm')"
        class="w-6 h-6 flex-shrink-0 inline-flex items-center justify-center text-[#3e5062] hover:text-[#081a27] rounded-sm focus:outline-none focus:ring-2 focus:ring-primary-700 focus:ring-offset-1 transition-colors cursor-pointer">
        <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24" stroke-width="2"><path stroke-linecap="round" stroke-linejoin="round" d="M6 18L18 6M6 6l12 12"/></svg>
      </button>
    </div>
    <div class="px-6 pb-2"><p class="text-[14px] text-[#3e5062] leading-[1.75]">この操作は取り消せません。選択したアイテムを完全に削除します。</p></div>
    <!-- Footer: Tertiary 左 / Primary 右 -->
    <div class="flex gap-3 items-center border-t border-[#c4cdd9] px-4 py-3">
      <button onclick="closeModal('modal-confirm')" class="inline-flex items-center justify-center gap-1 h-9 px-4 text-[14px] font-medium tracking-[0.28px] bg-[#f7f9fb] text-[#3e5062] rounded hover:bg-[#edf0f3] active:bg-primary-100 cursor-pointer">キャンセル</button>
      <div class="flex flex-1 gap-3 items-center justify-end">
        <button onclick="closeModal('modal-confirm')" class="inline-flex items-center justify-center gap-1 h-9 px-4 text-[14px] font-medium tracking-[0.28px] bg-primary-700 text-white rounded hover:bg-primary-600 active:bg-primary-800 cursor-pointer">確認する</button>
      </div>
    </div>
  </div>
</div>

<!-- Do: 破壊的（destroy）の主ボタンは Danger アウトライン型 -->
<button class="inline-flex items-center justify-center gap-1 h-9 px-4 text-[14px] font-medium tracking-[0.28px] bg-white text-[#e93766] border border-[#e93766] rounded hover:bg-[#fef2f4] active:bg-[#fccfd9] cursor-pointer">削除する</button>
```

**Don't**: フォーカストラップの省略 / `aria-modal="true"` の省略 / Escape で閉じる機能の省略（`alert` 除く）/ `bg-red-500` のフィル型 Danger / モーダルの3層以上の重ね表示 / `max-w-sm` `max-w-lg` 等の標準幅。

---

## 10. Implementation Notes
- スピナーは `.inline-spinner` を再利用（ボタン loading 状態）。ラベルは消さない
- Portal で `<body>` 直下にレンダリングし z-index 衝突を回避。`isOpen` 時に `body` の `overflow` を制御
- `prefers-reduced-motion`: フェードアニメーションを無効化
- 思想は `FOUNDATIONS.md`、使い分けは `COMPONENT_GUIDE.md`
