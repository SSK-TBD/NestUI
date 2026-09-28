# Component Guideline: Skeleton

> NestUI Skeleton / ローディング系プレースホルダー仕様。Tailwind CSS。値は `tokens/` を正とし、以下の class はその適用例。

## 1. Component Identity
- **Name**: Skeleton
- **Variants**: `card`（カード型） / `list`（リスト型） / `table`（テーブル型）（+ パーツ: Circle / Bar）。関連ローダーとして `inline-spinner`（ボタン内）・`dot-loader`（部分読み込み）を含む
- **Responsibility**:
  - する: コンテンツの形状を予告し、読み込み完了後のレイアウトを示すプレースホルダーを表示する
  - しない: 形状不明のローディング（→ Loader）/ 進捗表示（→ Progress）
- **Target Platform**: デスクトップ Web アプリ（業務 SaaS）

**原則**: Effortless（形状を予告し認知コストを下げる）/ Whisper（アニメーションは控えめに。pulse 1.5s のみ）/ Inclusive（`prefers-reduced-motion` で停止）/ 差し替え前提（実コンテンツ到着で即置換）。

---

## 2. Variants & States

**Variants**（スケルトン）

| Variant | 用途 | 行数目安 |
|---------|------|----------|
| `card` | アバター + テキスト2行 + 本文2行 | 4〜6行 |
| `list` | アバター + テキスト1行 を繰り返す | 3〜5アイテム |
| `table` | ヘッダー + 行を繰り返す | 3〜5行 |

**パーツ**

| パーツ | 役割 | class |
|--------|------|-------|
| Container | スケルトン全体 | `space-y-4` + `aria-busy="true"` `role="status"` |
| Circle | アバター等の円形 | `rounded-full bg-slate-200 skeleton-pulse` |
| Bar | テキスト行 | `rounded-md bg-slate-200 skeleton-pulse` + 高さ（h-3〜h-4） |
| SR-only text | 読み込み状態テキスト | `<span class="sr-only">読み込み中</span>` |

**関連ローダー**

| 種類 | 用途 | class |
|------|------|-------|
| inline-spinner | ボタン内の処理中（`disabled` + `opacity-60` + `cursor-not-allowed`） | `.inline-spinner`（CSS: clip-path arc） |
| dot-loader | ページの一部領域だけの読み込み（全画面占有禁止） | `.dot-loader` > `<span>` × 3 |

**States**: loading（pulse アニメーション）/ 表示・非表示は親で制御。

---

## 3. サイズ / Props

| 要素 | 高さ目安 |
|------|----------|
| Bar（見出し） | `h-4`（約16px） |
| Bar（本文） | `h-3`（約12px） |
| Circle（アバター） | `w-10 h-10`（40px、`rounded-full`） |

主な Props（参考）: `variant`（card/list/table）/ `width` / `height` / `lines`（行数）/ `isAnimated`（`prefers-reduced-motion` 時は false）。text 最終行は幅 60〜80% にして自然な見た目を再現。

---

## 4. Composition Rules

許可: Circle / Bar の組み合わせ。
禁止: 実コンテンツと大きく異なる形状（レイアウトシフトの原因）/ ローディング完了後の常時表示。
配置: コンテナを `aria-busy="true" role="status"` で包む。

---

## 5. Layout & Spacing

- text 行間 `space-y-2`（`spacing.2`）
- 角丸: text/Bar = `rounded-md`（`radius.sm`）/ Circle = `rounded-full`
- カードラッパー: `bg-white rounded-xl border border-slate-200 p-6 shadow-sm space-y-4`

### CSS（SSOT: `guidelines/INTERACTION_STATES`「Loading」相当）

```css
@keyframes skeletonPulse { 0%,100% { opacity: 1; } 50% { opacity: 0.4; } }
.skeleton-pulse { animation: skeletonPulse 1.5s ease-in-out infinite; }

.inline-spinner {
  width: 1em; aspect-ratio: 1; border-radius: 50%;
  border: 2.5px solid currentColor;
  animation: spinnerClip 0.8s infinite linear alternate, spinnerRotate 1.6s infinite linear;
}

.dot-loader { display: flex; align-items: center; gap: 5px; height: 34px; }
.dot-loader span {
  width: 9px; height: 17px; background: var(--color-primary-500, #2b70ef);
  border-radius: 3px; animation: dotWave 1.2s infinite ease-in-out;
}
.dot-loader span:nth-child(2) { animation-delay: 0.2s; }
.dot-loader span:nth-child(3) { animation-delay: 0.4s; }

@media (prefers-reduced-motion: reduce) {
  .skeleton-pulse { animation: none; opacity: 0.6; }
  .inline-spinner { animation: none; border-style: dotted; }
  .dot-loader span { animation: none; }
}
```

---

## 6. Token Mapping

| 用途 | トークン |
|------|---------|
| プレースホルダー色 | `color.surface.raised` 近傍（slate-200。NestUI は `bg-slate-200` 固定） |
| dot-loader バー色 | `color.primary.500`（`--color-primary-500`） |
| 角丸 text/Bar | `radius.sm`（`rounded-md`） |
| 角丸 Circle | `radius.full` |
| 行間 / padding | `spacing.2` / `spacing.6` |

> NestUI ではスケルトン色は `bg-slate-200` 固定。他色の使用は禁止。

---

## 7. Accessibility

| 属性 | 値 | 対象 |
|------|-----|------|
| `aria-busy` | `"true"` | スケルトンのコンテナ |
| `role` | `"status"` | スケルトン / dot-loader のコンテナ |
| SR-only text | `<span class="sr-only">読み込み中</span>` | コンテナ内 |
| `disabled` | — | スピナー表示中のボタン |
| `aria-busy` 解除 | `"false"` | 実コンテンツ到着時に必ず解除 |

## 8. Content Guidelines
- 形状は実際のコンテンツに近づける（ユーザーの期待を作る）
- text の最終行は幅 60〜80% に

## 9. Usage Do / Don't

```html
<!-- Do: カードスケルトン -->
<div aria-busy="true" role="status" class="bg-white rounded-xl border border-slate-200 p-6 shadow-sm space-y-4">
  <span class="sr-only">読み込み中</span>
  <div class="flex items-center gap-3">
    <div class="w-10 h-10 rounded-full bg-slate-200 skeleton-pulse"></div>
    <div class="flex-1 space-y-2">
      <div class="h-4 bg-slate-200 rounded-md w-3/4 skeleton-pulse"></div>
      <div class="h-3 bg-slate-200 rounded-md w-1/2 skeleton-pulse"></div>
    </div>
  </div>
  <div class="space-y-2">
    <div class="h-3 bg-slate-200 rounded-md w-full skeleton-pulse"></div>
    <div class="h-3 bg-slate-200 rounded-md w-5/6 skeleton-pulse"></div>
  </div>
</div>

<!-- Do: インラインスピナー（ボタン内） -->
<button disabled class="inline-flex items-center gap-1 h-9 px-3 text-[14px] font-medium tracking-[0.28px] text-white bg-primary-700 rounded opacity-60 cursor-not-allowed">
  <div class="inline-spinner"></div>送信中...
</button>
```

**Don't**: `bg-slate-200` 以外の色を使う / 常時回転スピナーのみの全画面表示（→ スケルトン or プログレスバーを併用）/ `aria-busy="true"` 省略 / 実コンテンツ到着後に `aria-busy` を解除しない / コンテンツと全く異なる形状。

---

## 10. Implementation Notes
- スタンドアロンの回転インジケーターは `Loader.md`（`ds-spinner`）を参照。ボタン loading は `inline-spinner`
- 進捗が計測可能なら `Progress.md` を使用
- `prefers-reduced-motion` で全アニメーションを停止（上記 CSS 参照）
