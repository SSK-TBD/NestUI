# Component Guideline: Copy button

> NestUI Copy Button 仕様。Tailwind CSS + 専用 CSS。クリップボードコピー + アイコン切替アニメーション付きのアイコンボタン。値は `tokens/`（色は `color.border.*` 等）を正とし、以下の class / CSS はその適用例。

## 1. Component Identity
- **Name**: Copy button
- **Variants**: Outlined / Subtle
- **Responsibility**:
  - する: クリップボードへコピーし、アイコン切替で成功を即時フィードバックする（トースト不要の軽量フィードバック）
  - しない: ラベル付きアクションボタン（→ Buttons）、ナビゲーション
- **Target Platform**: デスクトップ Web アプリ（業務 SaaS）

**原則**: 即時フィードバック（アイコン切替で成功を伝える。トースト不要）/ バネ感（`cubic-bezier(0.175,0.885,0.32,1.275)` のオーバーシュートで「ポンッ」）/ 自動復帰（2秒後にデフォルトへ。追加操作不要）/ アイコンのみ（`aria-label` 必須）。

---

## 2. Variants & States

**Variants**

| Variant | Container | ボーダー | 用途 |
|---------|-----------|---------|------|
| Outlined | `bg-white` | `border border-slate-200` | カード内、明るい背景上 |
| Subtle | `bg-transparent` | なし | ツールバー、コードブロック上 |

**States**

| 状態 | 視覚変化 | Duration / Easing |
|------|---------|-------------------|
| Default | Copy アイコン表示 | — |
| Hover | `scale(1.05)` + `bg-slate-50` | 250ms `cubic-bezier(0.175,0.885,0.32,1.275)` |
| Active | `scale(0.9)` | 100ms `ease-out` |
| Copied | Copy→Check アイコン切替（pop: opacity + scale 同時遷移） | 180ms `cubic-bezier(0.175,0.885,0.32,1.275)` |
| 復帰 | Check→Copy 切替 | 180ms（2秒後に自動実行） |
| Focus | `focus-visible:ring-2 ring-primary-700 ring-offset-1` | — |

> 状態遷移: `Default → Hover(1.05) → Active(0.9) → Copied(swap) → Default(2s後)`。`.copied` クラスの付け外しで制御。

---

## 3. サイズ / Anatomy

| サイズ | Container | アイコン | 角丸 | パディング | 用途 |
|--------|-----------|---------|------|-----------|------|
| Large | 48×48px | `w-6 h-6` | `rounded-2xl` | `p-3` | 単体デモ・ヒーロー |
| Medium（既定） | 40×40px | `w-5 h-5` | `rounded-xl` | `p-2.5` | コードブロックヘッダー |
| Small | 32×32px | `w-4 h-4` | `rounded-lg` | `p-2` | テーブルセル・コンパクト |

**Anatomy**: Container（hover/active の transform を受ける）+ Copy Icon（デフォルト表示）+ Check Icon（完了時に pop。`position:absolute` で重ねる）。

---

## 4. Composition Rules

許可: Copy アイコン SVG + Check アイコン SVG（2つを `position:absolute` で重ねる）。
禁止: `aria-label` の省略 / コピー完了フィードバックなし（アイコン切替を省略しない）/ 復帰タイマーの省略（必ず自動で元に戻す）。
配置: コードブロック上では `absolute top-3 right-3` + Subtle で配置。

---

## 5. Layout & Spacing

- Container: `inline-flex items-center justify-center relative`
- サイズは 32/40/48px の正方形（上表）
- コードブロック内: `absolute top-3 right-3`

---

## 6. Token Mapping

| 用途 | トークン |
|------|---------|
| Outlined 背景 / ボーダー | `color.surface.input`(white) / slate-200（`color.border.subtle` 近傍） |
| hover 背景 | slate-50（`color.surface.base` 近傍） |
| focus ring | `color.border.focus`(= primary-700) |
| アイコン | currentColor（配置先の文字色に追従） |
| 角丸 | `radius.lg`〜`xl`（Medium `rounded-xl`） |
| アニメーション | `motion.duration.*` 近傍（250ms/100ms/180ms）+ オーバーシュート easing |

---

## 7. Accessibility
- `aria-label="コピー"` 必須（アイコンのみボタン）
- コピー完了時に `aria-label` を `"コピーしました"` に更新し、2秒後に戻す
- フォーカスリング: `focus-visible:ring-2 ring-primary-700 ring-offset-1`
- `prefers-reduced-motion: reduce` 時は `scale` アニメーションを無効化し、`opacity` 切替のみにする

## 8. Content Guidelines
- ラベル文言（`aria-label`）: 既定「コピー」→ 完了「コピーしました」
- コピー対象が明確になるよう近接配置（共有URL・APIキー・コードブロック等）

## 9. Usage Do / Don't

```css
.copy-button{ display:inline-flex; align-items:center; justify-content:center; position:relative;
  background:#fff; border:1px solid #e2e8f0; border-radius:.75rem; width:40px; height:40px; cursor:pointer;
  transition:transform .25s cubic-bezier(0.175,0.885,0.32,1.275); }
.copy-button:hover{ transform:scale(1.05); background:#f8fafc; }
.copy-button:active{ transform:scale(0.9); transition:transform .1s ease-out; }
.copy-button:focus-visible{ outline:none; box-shadow:0 0 0 2px rgba(38,97,207,.5); } /* ring = primary-700 */
.copy-button .cb-icon{ position:absolute; transition:opacity .18s ease, transform .18s cubic-bezier(0.175,0.885,0.32,1.275); }
.copy-button .cb-icon-copy{ opacity:1; transform:scale(1); }
.copy-button .cb-icon-check{ opacity:0; transform:scale(0.5); }
.copy-button.copied .cb-icon-copy{ opacity:0; transform:scale(0.5); }
.copy-button.copied .cb-icon-check{ opacity:1; transform:scale(1); }
@media (prefers-reduced-motion: reduce){
  .copy-button, .copy-button .cb-icon{ transition-duration:.01ms; }
  .copy-button:hover, .copy-button:active{ transform:none; }
}
```

```html
<!-- Do: Medium（Outlined）— Copy/Check の2 SVG を重ねる -->
<button class="copy-button" aria-label="コピー" id="copyBtn">
  <svg class="cb-icon cb-icon-copy w-5 h-5" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
    <rect x="3" y="3" width="13" height="13" rx="2.5" ry="2.5"></rect><path d="M8 21h11a2 2 0 0 0 2-2v-11"></path>
  </svg>
  <svg class="cb-icon cb-icon-check w-5 h-5" viewBox="0 0 24 24">
    <circle cx="12" cy="12" r="11" fill="currentColor" opacity="0.15"/>
    <polyline points="7 12.5 10.5 16 17 9" fill="none" stroke="currentColor" stroke-width="2.2" stroke-linecap="round" stroke-linejoin="round"/>
  </svg>
</button>

<!-- Do: コピー挙動（成功で .copied 付与 → 2秒後に解除） -->
<script>
const btn = document.getElementById('copyBtn'); let timer;
btn.addEventListener('click', () => {
  navigator.clipboard.writeText('対象テキスト').catch(() => {});
  btn.classList.add('copied'); btn.setAttribute('aria-label','コピーしました');
  clearTimeout(timer);
  timer = setTimeout(() => { btn.classList.remove('copied'); btn.setAttribute('aria-label','コピー'); }, 2000);
});
</script>
```

**Don't**: `aria-label` 省略 / コピー成功のフィードバックなし（チェックアイコン切替必須）/ 復帰タイマーの省略（2秒後に戻す）/ `focus-visible:ring-primary-500/50`（→ `ring-primary-700 ring-offset-1`）。

---

## 10. Implementation Notes
- 2つの SVG を `position:absolute` で重ね、`opacity` + `scale` の同時遷移で pop を実現
- CopyTextField の右端ボタンとしても使う（→ `Text field.md`）
- アイコンは Lucide（copy / check）
- トークン参照は `guidelines/TOKEN_GUIDE.md`
