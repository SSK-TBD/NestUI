# Component Guideline: Tree view

> NestUI Tree Connector 仕様。Tailwind CSS。値は `tokens/`（色は `color.border.*` 等）を正とし、以下の class はその適用例。
> NestUI での実体は「スレッド/返信/階層ビューで使う分岐コネクター」。32×40px の固定セルにラインを組み合わせ、ツリー状の接続関係を表現する。

## 1. Component Identity
- **Name**: Tree view（Tree Connector）
- **Variants**: コネクター型 `┃`（たて）/ `━`（よこ）/ `┳`（よこした・分岐起点）/ `┣`（たてみぎ・中間）/ `┗`（ひだりした・最後）/ `（から）`（空白セル）
- **Responsibility**:
  - する: 階層構造・コメント返信スレッド・親子関係を分岐ラインで可視化する
  - しない: フラットなリスト表示（List Group を使う）、深い階層のグローバルナビ（Side nav を使う）
- **Target Platform**: デスクトップ Web アプリ（業務 SaaS）

**原則**: 固定セル（32×40px、または flex-1 で伸縮。grid では列幅 36px）/ ライン色は `#dde3eb`（DS 標準ボーダー）固定・変更不可 / ライン幅 1px 固定 / コネクターは装飾なので `aria-hidden="true"`。

---

## 2. Variants & States

**コネクター型**

| 型 | 垂直ライン | 水平ライン | 用途 |
|----|-----------|-----------|------|
| `┃`（たて） | 上端→下端 | なし | 連続スレッドの中間 |
| `━`（よこ） | なし | 左端→右端 | 水平区切り（単独使用） |
| `┳`（よこした） | 中央→下端 | 左端→右端 | 分岐の起点（下に続く） |
| `┣`（たてみぎ） | 上端→下端 | 中央→右端 | 中間の返信アイテム |
| `┗`（ひだりした） | 上端→50% | 中央→右端 | 最後の返信アイテム |
| `（から）` | なし | なし | 空白セル |

ライン位置（32×40px セル）: 垂直 `absolute top-0 bottom-0 left-[15px] w-px bg-[#dde3eb]` / 水平 `absolute left-[16px] right-0 top-[19px] h-px bg-[#dde3eb]`（┣ / ┗ のみ）。

**States**: コネクターは静的。展開/折りたたみは親アイテム側で制御（コネクターは状態を持たない）。

---

## 3. Parts / 構成

| パーツ | 役割 | 必須 | class |
|--------|------|:----:|------|
| Container | 32×40px の相対配置基点 | Yes | `relative w-8 h-10`（grid では列幅 36px + `self-stretch`） |
| 垂直ライン | 上下の接続を示す線 | 型による | `absolute left-[15px] w-px bg-[#dde3eb]` |
| 水平ライン | 横方向の分岐を示す線 | ┣ / ┗ のみ | `absolute left-[16px] right-0 h-px bg-[#dde3eb]` |

---

## 4. Composition Rules

- 許可: Container 内に絶対配置の `<div>` ライン要素のみ
- 禁止: `border-left` / `border-top` でのライン代替（必ず絶対配置 `div` で実装）、セル寸法（32px セル / 36px grid 列 / 40px 高さ）の変更、水平ラインを 2px 以上にすること
- 入れ子: コンテンツ列を `pl-4`〜`pl-8` で段階的にインデントし、各レベルで同じコネクター構造を繰り返す

---

## 5. Layout & Spacing

- Activity スレッド（主要ユースケース）は `grid-template-columns: 36px 1fr`（tree cell 36px | content cell 1fr）
- セル高さは grid の `self-stretch` でコンテンツ高さに自動追従
- 水平ラインの垂直位置: コンテンツに `py-3`（12px）を使う場合、アバター中央 = `12px(上padding) + 12px(アバター半径) = 24px` から → `style="top:24px"`

---

## 6. Token Mapping

| 用途 | class（Hex） | トークン |
|------|------|---------|
| 接続ライン色 | `bg-[#dde3eb]` | `color.border.subtle`（DS 標準ボーダー） |

> ライン色は `#dde3eb` 固定で変更不可。`#dde3eb` は装飾ラインのため WCAG コントラスト要件対象外。

---

## 7. Accessibility
- コネクター全体に `aria-hidden="true"` を付与（支援技術から隠す）
- スレッドの親子関係は `aria-label` や `role` でコンテンツ側に記述する
- 操作可能なツリー（WAI-ARIA Tree パターン）として実装する場合は、コンテンツ側に `role="tree"` / `role="treeitem"` / `aria-expanded` / `aria-level` を付与

## 8. Content Guidelines
- コネクター自体はテキストを持たない。ノードラベルはコンテンツ列に簡潔に（ファイル名・エンティティ名・コメント本文）

## 9. Usage Do / Don't

```html
<!-- Do: Activity スレッド grid（┃ → ┣ → ┗） -->
<div class="grid" style="grid-template-columns:36px 1fr;" aria-label="コメントスレッド">
  <!-- 親コメント: ┃ -->
  <div class="relative self-stretch border-b border-slate-100" aria-hidden="true">
    <div class="absolute top-0 bottom-0 left-[17px] w-px bg-[#dde3eb]"></div>
  </div>
  <div class="pr-4 py-3 border-b border-slate-100"><!-- 親コメント --></div>
  <!-- 返信（最後）: ┗ -->
  <div class="relative self-stretch" aria-hidden="true">
    <div class="absolute top-0 bottom-[50%] left-[17px] w-px bg-[#dde3eb]"></div>
    <div class="absolute h-px bg-[#dde3eb]" style="left:18px;right:0;top:24px;"></div>
  </div>
  <div class="pr-4 py-3"><!-- 返信 --></div>
</div>
```

**Don't**: ライン色を `#dde3eb` 以外にする / `border-left` や `border-top` でラインを代替する（→絶対配置 `div`）/ セル寸法（32px / 36px / 40px）を変更する / 水平ライン高さを 2px 以上にする。

---

## 10. Implementation Notes
- ラインは必ず絶対配置 `div`（`w-px` / `h-px`）で実装。`py-3` 使用時の水平ラインは `top:24px`（= 12px 上padding + 12px アバター半径）
- 大量ノードはコンテンツ側で遅延ロード/仮想スクロールを検討
