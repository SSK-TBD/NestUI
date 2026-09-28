# Component Guideline: Tooltip

> NestUI Tooltip 仕様。Tailwind CSS。値は `tokens/`（背景は `color.content.secondary`）を正とし、以下の class はその適用例。

## 1. Component Identity
- **Name**: Tooltip
- **Variants**: 方向 `top`（既定） / `bottom` / `left` / `right`（+ タイトル有無）
- **Responsibility**:
  - する: ラベルやボタンの補足情報を、ホバーまたはフォーカスで一時表示する
  - しない: リッチコンテンツ・インタラクティブ要素の表示（→ Popover）/ 確認ダイアログ（→ Modal）/ 長文説明
- **Target Platform**: デスクトップ Web アプリ（業務 SaaS）

**原則**: 補足情報（ホバー/フォーカスで表示）/ 非侵入的（閲覧のみ・インタラクティブ要素を含めない）/ 4方向対応 / キーボード対応（フォーカスでも表示・Escape で非表示）。

---

## 2. Variants & States

**Variants**（方向）

| 方向 | カレット位置 | 配置クラス |
|------|------------|----------|
| Top（既定） | 下辺中央（下向き） | `bottom-full mb-[6px] left-1/2 -translate-x-1/2` |
| Bottom | 上辺中央（上向き） | `top-full mt-[6px] left-1/2 -translate-x-1/2` |
| Left | 右辺中央（右向き） | `right-full mr-[6px] top-1/2 -translate-y-1/2` |
| Right | 左辺中央（左向き） | `left-full ml-[6px] top-1/2 -translate-y-1/2` |

**タイトル**: オプション。使う場合 12px Bold（本文 12px Medium）+ 上下 `gap-[8px]`。テキストのみの場合はタイトル要素を DOM から省略。

**States**: hidden（既定 `opacity-0 pointer-events-none`）/ hover・focus（`opacity-100`）。アニメーション `transition-opacity duration-200`。

---

## 3. サイズ / Props

| プロパティ | 値 |
|-----------|-----|
| 背景色 | `bg-[#5a6c7f]`（`color.content.secondary`） |
| テキスト色 | `text-white` |
| フォント（本文） | `text-[12px] font-medium tracking-[0.24px] leading-[1.3]` |
| フォント（タイトル） | `text-[12px] font-bold` |
| テキスト揃え | `text-center` |
| 角丸 | `rounded-[8px]` |
| padding | `p-[8px]` |
| 最大幅 | `max-w-[256px]`（`w-max` で内容に合わせる） |
| z-index | `z-20` |
| トリガーからの間隔 | 6px（カレット含む） |

主な Props（参考）: `label`（必須）/ `placement`（top/bottom/left/right）/ `title`（任意）/ `delay`（既定 400ms）/ `isDisabled`。

---

## 4. Composition Rules

許可（トリガー = children）: `<button>` / アイコンボタン / テキスト / 任意の HTML 要素。
禁止（tooltip 内）: インタラクティブ要素（ボタン・リンク）/ リッチコンテンツ（リスト・フォーム）→ Popover を使う。
配置: トリガーを `relative inline-block` で包み、tooltip を絶対配置する。

---

## 5. Layout & Spacing

- shadow: `shadow-[0px_4px_10px_0px_rgba(10,10,10,0.1),0px_2px_5px_0px_rgba(10,10,10,0.05)]`
- カレットはインライン SVG（コンテナと同色 `#5a6c7f`）。Top/Bottom は 14×6px、Left/Right は 6×14px
- トリガーから本体まで 6px 空ける（方向別マージン上記）

### カレット SVG（方向別）

| 方向 | path | 配置 |
|------|------|------|
| Top（下向き） | `M7 6L0 0H14L7 6Z` | `bottom-[-6px] left-1/2 -translate-x-1/2` |
| Bottom（上向き） | `M7 0L14 6H0L7 0Z` | `top-[-6px] left-1/2 -translate-x-1/2` |
| Left（右向き） | `M6 7L0 0V14L6 7Z` | `right-[-6px] top-1/2 -translate-y-1/2` |
| Right（左向き） | `M0 7L6 0V14L0 7Z` | `left-[-6px] top-1/2 -translate-y-1/2` |

---

## 6. Token Mapping

| 用途 | トークン |
|------|---------|
| 背景・カレット | `color.content.secondary`(#5A6C7F) |
| テキスト | `color.content.inverse`（white） |
| 角丸 | `radius.lg`（8px / `rounded-[8px]`） |
| padding | `spacing.2`（8px） |
| z-index | `elevation.z.dropdown`（`z-20`） |
| アニメーション | `motion.duration.normal`（200ms） |

---

## 7. Accessibility

| 属性 | 値 |
|------|-----|
| `role` | `"tooltip"`（tooltip 要素・`id` 付与） |
| `aria-describedby` | トリガー要素に tooltip の id を参照（省略禁止） |
| 表示トリガー | hover + focus（hover のみは不可。フォーカスでも表示必須） |
| Escape | 全ツールチップを非表示 |
| アイコンボタン | `aria-label` 必須（テキストがないため） |

- コントラスト 4.5:1 以上

## 8. Content Guidelines
- 40文字以内の短い説明にする
- キーボードショートカットの補足に有効（例: `保存 (Ctrl+S)`）
- 重要な情報はツールチップのみに入れない（ホバーしないと見えない）

## 9. Usage Do / Don't

```html
<!-- Do: Top・テキストのみ -->
<div class="relative inline-block">
  <button aria-describedby="tooltip-1"
    onmouseenter="showTooltip('tooltip-1')" onmouseleave="hideTooltip('tooltip-1')"
    onfocus="showTooltip('tooltip-1')" onblur="hideTooltip('tooltip-1')"
    class="inline-flex items-center justify-center h-9 px-3 text-[14px] font-medium bg-primary-700 text-white rounded cursor-pointer">ボタン</button>
  <div id="tooltip-1" role="tooltip"
    class="absolute bottom-full mb-[6px] left-1/2 -translate-x-1/2 w-max max-w-[256px] bg-[#5a6c7f] text-white text-[12px] font-medium tracking-[0.24px] leading-[1.3] text-center rounded-[8px] p-[8px] shadow-[0px_4px_10px_0px_rgba(10,10,10,0.1),0px_2px_5px_0px_rgba(10,10,10,0.05)] z-20 opacity-0 pointer-events-none transition-opacity duration-200">
    ツールチップの内容
    <svg class="absolute bottom-[-6px] left-1/2 -translate-x-1/2" width="14" height="6" viewBox="0 0 14 6" fill="none" aria-hidden="true"><path d="M7 6L0 0H14L7 6Z" fill="#5a6c7f"/></svg>
  </div>
</div>
```

```javascript
function showTooltip(id){var el=document.getElementById(id);if(el){el.classList.remove('opacity-0','pointer-events-none');el.classList.add('opacity-100');}}
function hideTooltip(id){var el=document.getElementById(id);if(el){el.classList.remove('opacity-100');el.classList.add('opacity-0','pointer-events-none');}}
document.addEventListener('keydown',function(e){if(e.key==='Escape'){document.querySelectorAll('[role="tooltip"]').forEach(function(t){t.classList.remove('opacity-100');t.classList.add('opacity-0','pointer-events-none');});}});
```

**Don't**: tooltip 内にインタラクティブ要素を置く（→ Popover）/ hover のみでフォーカス非対応 / `bg-slate-600`（→ `bg-[#5a6c7f]`）/ `rounded-lg`（→ `rounded-[8px]`）/ `max-w-xs`（→ `max-w-[256px]`）/ `aria-describedby` 省略 / 重要な情報をツールチップのみに入れる。

---

## 10. Implementation Notes
- `<Portal>` で body 直下にレンダリングすると overflow/z-index 問題を回避できる
- `delay` のデフォルト 400ms はホバーのチラつきを防ぐ
- リッチコンテンツが必要なら Popover を参照
