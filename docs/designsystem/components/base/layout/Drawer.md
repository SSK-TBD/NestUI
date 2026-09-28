# Component Guideline: Drawer

> NestUI Drawer（コンテキストパネル）仕様。Tailwind CSS。値は `tokens/`（色は `color.surface.*` 等）を正とし、以下の class はその適用例。
> 画面右端から Push 表示される詳細パネル。閉じるボタン（DrawerMenu）＋ コンテンツエリアで構成する。

## 1. Component Identity
- **Name**: Drawer
- **Variants**: `horizontal`（横並び 2 カラム）/ `header-body`（ヘッダー + ボディ）
- **Responsibility**:
  - する: メインコンテンツに隣接して詳細情報を Push 表示する（オブジェクト詳細・案件詳細）。背面のコンテキストを保持する
  - しない: 中央オーバーレイで作業を遮断する確認操作（Modals を使う）/ 補足情報の小窓（Popover を使う）/ メインナビゲーション（Side nav を使う）
- **Target Platform**: デスクトップ Web アプリ（業務 SaaS）

**原則**: 右端 Push 型（メインを押し出して隣接配置。モーダルのように重ならない）/ 閉じるボタンは左端固定（DrawerMenu 40px が常に最左端に位置し、閉じる起点を直感的に認識できる）/ スロット設計（コンテンツエリアは任意のコンテンツを差し込む）。

---

## 2. Variants & States

**Variants**

| Variant | 用途 | レイアウト |
|---------|------|-----------|
| `horizontal`（横並び 2 カラム） | メインエリア + サブパネルの並列表示 | DrawerMenu(40px) + メイン(`flex-1 min-w-[400px]`) + サブパネル(`w-[320px]`) |
| `header-body`（ヘッダー + ボディ） | 上部ヘッダー + 下部に左サブナビ + メイン | DrawerMenu(40px) + 縦レイアウト（ヘッダー `shrink-0` + ボディ: 左サブパネル `w-[200px]` + メイン `flex-1`） |

**DrawerMenu / CloseButton の States**（DrawerMenu に `group` を付与し、子要素を `group-hover:` / `group-active:` で一括制御）

| State | 右ボーダー幅 / 色 | CloseButton ボーダー | CloseButton 背景 |
|-------|------------------|---------------------|-----------------|
| Enable（resting） | `w-px` / `#c4cdd9` | `#c4cdd9`（`color.border.default`） | `#ffffff` |
| Hover | `w-[2px]` / `#2661cf` | `#7b8d9f`（`color.border.strong`） | `#edf0f3` |
| Press | `w-[3px]` / `#2661cf` | `#7b8d9f` | `#ddedfc` |

---

## 3. パーツ

| パーツ | 要素 | 必須 | 説明 |
|--------|------|:----:|------|
| Drawer | `<div>` | Yes | 外枠コンテナ。`flex h-full items-stretch`。左ボーダーなし（DrawerMenu の RightBorder が境界を示す） |
| DrawerMenu | `<div>` | Yes | 閉じるボタンコンテナ。幅 40px・全高。`group` クラス + 背景 `#edf0f3`（`color.surface.sunken`） |
| RightBorder | `<div>` | Yes | DrawerMenu 右端のボーダーライン（absolute 配置）。状態で太さ・色が変化（CSS `border` 不可・独立 `<div>` の width で実装） |
| CloseButton | `<button>` | Yes | ×ボタン。DrawerMenu の `top-[12px] right-0` に absolute 配置。40×40px・左/上/下の3辺ボーダー（右辺なし）・左角丸 `rounded-bl-[8px] rounded-tl-[8px]` |
| ContentArea | `<div>` | Yes | メインコンテンツ。`flex-1 min-w-0`。`overflow-y-auto`（`overflow:hidden` 禁止） |

CloseButton アイコン: Lucide `X`（`size-[24px]`、`text-[#5a6c7f]` = `color.content.secondary`）。

---

## 4. Composition Rules

許可: DrawerMenu（必須・最左端）/ メインエリア / サブパネル（`horizontal`）/ ヘッダー + 左サブナビ（`header-body`）/ 任意のスロットコンテンツ（フォーム・リスト・詳細表示）。
禁止: Drawer 内に Drawer をネスト / `border-r` を DrawerMenu コンテナに付与（太さ変化に対応不可 → 独立 `<div>` で実装）/ DrawerMenu 右ボーダーを CSS `border` で実装（→ width アニメーションが必要）/ コンテンツエリアに `overflow: hidden`（スクロールが切れる）/ X アイコンに `aria-label`（親 `<button>` に `aria-label="閉じる"` があり二重読み上げになる）。
サブパネルは `bg-[#f7f9fb]`（`color.surface.base`）必須（コンテンツが埋まらない余白が白く見えるのを防ぐ）。

---

## 5. Layout & Spacing

| プロパティ | トークン | 値 |
|-----------|---------|-----|
| DrawerMenu 幅 | — | 40px（`w-[40px]` 固定） |
| メインエリア（horizontal） | — | `flex-1 min-w-[400px]` |
| サブパネル（horizontal） | — | `w-[320px]` 固定 |
| 左サブパネル（header-body） | `spacing.4` | `w-[200px]` 固定・`p-[16px]` |
| CloseButton | `spacing.2` | 40×40px・`p-[8px]`・top offset `top-[12px]` |
| CloseButton radius | `radius.*`（8px） | `rounded-bl-[8px] rounded-tl-[8px]` |
| 右ボーダー | — | 1→2→3px（状態で変化） |

各ゾーンは `overflow-y-auto` で個別スクロール。コンポーネント自体に外側 `margin` を持たせない。

---

## 6. Token Mapping

| 用途 | トークン |
|------|---------|
| DrawerMenu 背景 / 左サブパネル背景 | `#edf0f3`（`color.surface.sunken`） |
| サブパネル / ヘッダー背景 | `#f7f9fb`（`color.surface.base`） |
| CloseButton 背景（Enable / Hover / Press） | `#ffffff`（`color.surface.raised`）/ `#edf0f3` / `#ddedfc`（`color.primary.100`） |
| CloseButton ボーダー（Enable / Hover・Press） | `#c4cdd9`（`color.border.default`）/ `#7b8d9f`（`color.border.strong`） |
| DrawerMenu 右ボーダー（Enable / Hover・Press） | `#c4cdd9`（`color.border.default`）/ `#2661cf`（`color.border.focus` = primary-700） |
| X アイコン色 | `#5a6c7f`（`color.content.secondary`） |
| z-index | `elevation.z.overlay`（40） |
| アニメーション | `motion.duration.slow`（300ms、`translateX` + `ease-in-out`、右→左） |

---

## 7. Accessibility

- Drawer コンテナ: `role="complementary"`（補助）または `role="dialog"` + `aria-labelledby`（モーダル相当時 `aria-modal="false"`）
- CloseButton: `aria-label="閉じる"` 必須。X アイコンには付与しない（`aria-hidden="true"`）
- 開閉状態: トリガーボタンに `aria-expanded="true|false"` + `aria-controls="[drawer-id]"`
- Escape: パネルを閉じ、トリガーボタンにフォーカスを戻す
- フォーカス管理: 開いたとき CloseButton または最初のインタラクティブ要素にフォーカスを移動。長いフォームはボディ部分のみ `overflow-y: auto`

---

## 8. Content Guidelines
- 1 Drawer = 1 オブジェクトの詳細。複数オブジェクトを詰め込まない
- 案件詳細など標準レイアウトはオブジェクト一覧 + ドロワー詳細パターンに従う（左ペイン: 詳細 + アクティビティ、右ペイン: 関連オブジェクトのカード群）。アクションボタンはフッターに配置

---

## 9. Usage Do / Don't

```html
<!-- Do: horizontal（横並び 2 カラム） -->
<div id="drawer-panel-1" class="flex h-full items-stretch" role="complementary" aria-label="詳細パネル">
  <!-- DrawerMenu: group で子要素の状態を一括制御 -->
  <div class="relative h-full w-[40px] flex-shrink-0 group bg-[#edf0f3]">
    <!-- 右端ボーダー: Enable=1px #c4cdd9 / Hover=2px / Press=3px #2661cf -->
    <div class="absolute right-0 top-0 bottom-0 w-px bg-[#c4cdd9]
                group-hover:w-[2px] group-hover:bg-[#2661cf]
                group-active:w-[3px] group-active:bg-[#2661cf]
                transition-all duration-150 pointer-events-none"></div>
    <button type="button" aria-label="閉じる" aria-controls="drawer-panel-1"
      class="absolute top-[12px] right-0 size-[40px] flex items-center justify-center p-[8px]
             bg-white border-l border-t border-b border-[#c4cdd9]
             group-hover:border-[#7b8d9f] group-hover:bg-[#edf0f3] group-active:bg-[#ddedfc]
             rounded-bl-[8px] rounded-tl-[8px]
             focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-primary-700 focus-visible:ring-offset-1
             transition-colors cursor-pointer">
      <svg class="size-[24px] text-[#5a6c7f]" fill="none" stroke="currentColor" viewBox="0 0 24 24"
           stroke-width="2" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true">
        <line x1="18" y1="6" x2="6" y2="18"/><line x1="6" y1="6" x2="18" y2="18"/>
      </svg>
    </button>
  </div>
  <!-- メインエリア -->
  <div class="flex-1 min-w-[400px] h-full overflow-y-auto"><!-- コンテンツ --></div>
  <!-- サブパネル: bg-[#f7f9fb] 必須 -->
  <div class="w-[320px] flex-shrink-0 h-full overflow-y-auto bg-[#f7f9fb]"><!-- サブコンテンツ --></div>
</div>
```

**Don't**: CloseButton に `aria-label` なし / X アイコンに `aria-label` 付与（二重読み上げ）/ DrawerMenu 右ボーダーを CSS `border` で実装 / `border-r` を DrawerMenu コンテナに付与 / コンテンツエリアに `overflow:hidden` / Drawer のネスト。

---

## 10. Implementation Notes
- 開閉は `hidden` クラスのトグル + トリガーの `aria-expanded` 更新。開時 CloseButton にフォーカス、Escape で閉じてトリガーへ戻す
- 表示アニメーションは `translateX` + 300ms `ease-in-out`（右から左）。`prefers-reduced-motion` 時は無効化
- 案件詳細パネル等の標準レイアウトでは Tabs / Tags / 関連オブジェクトのカード / Tree view 等を組み合わせる（各コンポーネント仕様を参照）
- 思想は `FOUNDATIONS.md`、使い分けは `COMPONENT_GUIDE.md`
