# Component Guideline: Toast

> NestUI Toast / Notification 仕様。Tailwind CSS。値は `tokens/`（色は `color.semantic.*` / `color.status.*`、z-index は `elevation.z.toast`）を正とし、以下の class はその適用例。

## 1. Component Identity
- **Name**: Toast
- **Variants**: `success`（成功・情報） / `error`（失敗） / `loading`（処理中）
- **Responsibility**:
  - する: ユーザー操作の結果を一時的にオーバーレイ（`fixed`）通知する。非侵入的でフローを中断しない
  - しない: ページ内常駐メッセージ（→ Alerts）/ 確認を要するダイアログ（→ Modal）/ フォームバリデーションの唯一の表示
- **Target Platform**: デスクトップ Web アプリ（業務 SaaS）

**原則**: 操作結果のフィードバック / 非侵入的（操作を要求しない）/ 自動消去（Success は一定時間で消える。Error / Loading は手動閉じまで残す）/ スタック表示（古い順に積む）。

---

## 2. Variants & States

**Variants**（種類）

| 種類 | アイコン（Lucide） | 背景 | ボーダー（全辺） | テキスト | role / aria-live |
|------|------|------|----------|----------|------|
| `success` | `check` | `bg-primary-50`(#f0f7fe) | `border-primary-700`(#2661cf) | `text-primary-700` | `status` / `polite` |
| `error` | `triangle-alert` | `bg-[#fef2f4]` | `border-[#e93766]` | `text-[#e93766]` | `alert` / `assertive` |
| `loading` | `loader-circle`（`animate-spin`） | `bg-[#f7f9fb]` | `border-[#a1afc0]` | `text-[#5a6c7f]` | `status` / `polite` |

> Warning・Info タイプは使用しない。情報通知は Success（blue）で代替する。

**バリエーション**: シンプル（タイトルのみ）/ 説明付き（タイトル + 説明テキスト）。

**States / 振る舞い**

| 動作 | ルール |
|------|--------|
| 表示アニメーション | 右からスライドイン + フェードイン（`translateX(100%)`→`translateX(0)`） |
| 自動消去 | Success: 5秒後に自動消去。Error / Loading: 手動閉じのみ |
| 手動閉じ | × ボタンクリック、または Escape（フォーカス中） |
| ホバー時 | 自動消去タイマーを一時停止 |
| スタック | 同時最大5つ。超過時は最古を消去。間隔 `gap-3` |
| 永続性 | SPA ルート変更後も維持。フルリロードで消去 |

---

## 3. サイズ / Props

| パーツ | 仕様 |
|--------|------|
| Container | `flex items-center gap-2 px-4 py-3 border border-solid rounded-sm` + shadow + `w-full max-w-[360px]` |
| Title | `text-[14px] font-bold leading-[1.5] tracking-[0.28px]` |
| Description（任意） | `text-[12px] font-normal leading-[1.75] tracking-[0.24px]` |
| Icon | `w-5 h-5 flex-shrink-0`（`stroke="currentColor"`） |
| Close btn | `w-5 h-5 flex-shrink-0 inline-flex items-center justify-center rounded-sm cursor-pointer hover:bg-black/5` + `aria-label="閉じる"` |

主な Props（参考）: `variant`（success/error/loading）/ `title`/`description` / `duration`（ms、`null` で自動消滅なし＝error 推奨）/ `action` / `onClose`。

---

## 4. Composition Rules

許可: タイトル・説明テキスト / Status Icon / Close Button / アクションボタン。
禁止: フォーム要素 / `<Toast>` の入れ子 / 確認が必要なアクションの処理（→ Modal）。
配置: 表示位置 — 右上 `fixed top-4 right-4`（推奨）/ 上中央 / 下中央（モバイル）。コンテナ: `fixed top-4 right-4 z-50 flex flex-col gap-3 w-full max-w-[360px]`。

**Anti-patterns**
- NG: 左端だけに色付きの縦アクセントバー（border-left のみ）を付ける → variant は淡色背景＋全辺の semantic ボーダー（枠）＋アイコン色で区別する

---

## 5. Layout & Spacing

- padding `px-4 py-3`、アイコン-テキスト `gap-2`、Toast 間 `gap-3`
- border-width（全辺）1px（`border border-solid`、variant の semantic 色）
- max-width 360px、角丸 `rounded-sm`
- shadow: `shadow-[0px_10px_15px_-5px_rgba(10,10,10,0.1),0px_5px_5px_-2px_rgba(10,10,10,0.05)]`（`elevation.shadow.md`）
- z-index: `z-50`（`elevation.z.toast`）

> variant の識別は 淡色背景 + 全辺の semantic ボーダー（枠 1px）+ アイコン色 で行う。オーバーレイで浮くため枠で輪郭を補強する。左端のみのアクセントバー（border-left のみ）は使わない。

---

## 6. Token Mapping

| Variant | 背景 | ボーダー（全辺）/ テキスト |
|---------|------|---------|
| success | `color.semantic.infoBg`(#F0F7FE) | `color.primary.700`(#2661CF) |
| error | `color.status.danger.container`(#FEF2F4) | `color.status.danger.base`(#E93766) |
| loading | `color.surface.base`(#F7F9FB) | `color.content.disabled`(#A1AFC0) / `color.content.secondary`(#5A6C7F) |
| shadow / z | `elevation.shadow.md` / `elevation.z.toast` |
| radius / padding | `radius.sm` / `spacing.4`・`spacing.3` |

---

## 7. Accessibility
- **Role**: success/loading=`role="status"` + `aria-live="polite"`。error=`role="alert"` + `aria-live="assertive"`（省略禁止）
- 閉じるボタン: `aria-label="閉じる"` 必須
- フォーカスはトースト表示時に移動しない（非侵入的）。フォーカス中 Escape で閉じる
- 自動消去は SR 読み上げに十分な時間（最低5秒）を確保。`prefers-reduced-motion` でアニメーション無効化
- コントラスト 4.5:1 以上

## 8. Content Guidelines
- description は1〜2文以内。詳細はリンク/アクションへ誘導
- success: 「保存しました」（過去形）/ error: 見出し「〜できませんでした」+ 説明「〜してください」（原因＋対処法）
- 1つの文字列にまとめる場合は「〜できませんでした。〜してください」。句点は文の切れ目のみで、末尾には付けない
- 「失敗」は使わず「〜できませんでした」で書く

## 9. Usage Do / Don't

```html
<!-- Do: Success（説明付き） -->
<div role="status" aria-live="polite" class="flex items-center gap-2 px-4 py-3 bg-primary-50 border border-primary-700 rounded-sm shadow-[0px_10px_15px_-5px_rgba(10,10,10,0.1),0px_5px_5px_-2px_rgba(10,10,10,0.05)]">
  <svg class="w-5 h-5 text-primary-700 flex-shrink-0" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true"><path d="M20 6 9 17l-5-5"/></svg>
  <div class="flex-1 min-w-0 text-primary-700">
    <p class="text-[14px] font-bold leading-[1.5] tracking-[0.28px]">保存しました</p>
    <p class="text-[12px] font-normal leading-[1.75] tracking-[0.24px]">変更内容が正常に保存されました</p>
  </div>
  <button type="button" aria-label="閉じる" class="w-5 h-5 flex-shrink-0 inline-flex items-center justify-center rounded-sm text-primary-700 hover:bg-primary-100 cursor-pointer transition-colors">
    <svg class="w-5 h-5" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true"><path d="M18 6 6 18M6 6l12 12"/></svg>
  </button>
</div>

<!-- Do: Error（手動閉じ・assertive） -->
<div role="alert" aria-live="assertive" class="flex items-center gap-2 px-4 py-3 bg-[#fef2f4] border border-[#e93766] rounded-sm shadow-[0px_10px_15px_-5px_rgba(10,10,10,0.1),0px_5px_5px_-2px_rgba(10,10,10,0.05)]">
  <svg class="w-5 h-5 text-[#e93766] flex-shrink-0" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true"><path d="m21.73 18-8-14a2 2 0 0 0-3.48 0l-8 14A2 2 0 0 0 4 21h16a2 2 0 0 0 1.73-3Z"/><path d="M12 9v4"/><path d="M12 17h.01"/></svg>
  <div class="flex-1 min-w-0 text-[#e93766]">
    <p class="text-[14px] font-bold leading-[1.5] tracking-[0.28px]">保存できませんでした</p>
    <p class="text-[12px] font-normal leading-[1.75] tracking-[0.24px]">時間をおいてもう一度お試しください</p>
  </div>
  <button type="button" aria-label="閉じる" class="w-5 h-5 flex-shrink-0 inline-flex items-center justify-center rounded-sm text-[#e93766] hover:bg-[#fccfd9] cursor-pointer transition-colors">
    <svg class="w-5 h-5" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true"><path d="M18 6 6 18M6 6l12 12"/></svg>
  </button>
</div>
```

**Don't**: Warning / Info タイプの使用（→ Success / Error / Loading の3タイプ）/ `aria-live` 省略 / 色のみでタイプ識別（アイコン必須）/ 閉じるボタンの `aria-label` 省略 / エラーを自動消去（→ 手動閉じ）/ 自動消去を3秒未満にする / 確認が必要なアクションを Toast で処理（→ Modal）。

---

## 10. Implementation Notes
- Toast Manager をグローバルに管理（Context API + `useToast()`）。同時表示最大5件・超過分はキュー
- アイコンは Lucide（`w-5 h-5`、`stroke="currentColor"`）。Loading は `loader-circle` + `animate-spin`
- ページ内常駐は `Alerts.md`、ローディング単体は `Loader.md` を参照

---

## 11. Change Log & Deprecation

| Version | 変更内容 | 対応期限 |
|---------|---------|---------|
| v1.0 | 初期リリース | — |
| v1.1 | 左端のみのアクセントバー（border-left のみ）を廃止。全辺ボーダー＝枠方式へ変更（淡色背景＋全辺 semantic ボーダー＋アイコン色） | — |
| v1.2 | Error の Do 例が Content Guidelines（「〜できませんでした」）に反して「保存に失敗しました」だった問題を修正。Success と同じ 見出し+説明 構造にし、次の一手を明記 | — |
