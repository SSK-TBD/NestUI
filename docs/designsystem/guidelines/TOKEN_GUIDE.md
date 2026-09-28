# Token Guide

`docs/designsystem/tokens/` 配下のトークンを参照・適用するための **機構ガイド**。
「どの値を選ぶか」のデザイン原則は `docs/designsystem/guidelines/FOUNDATIONS.md` を参照。
このファイルでは「どう参照するか」を定義する。値はすべて NestUI の実体。

---

## ファイル構成

カテゴリごとに JSON を分けている。タスクに必要なものだけを選択的に読み込む。カテゴリ別の JSON がそのまま正（canonical）で、スキルはここから必要なものだけを読む。

| ファイル | 主なキー | 用途 |
|---|---|---|
| [color.json](../tokens/color.json) | `color.primary` / `color.brand` / `color.status` / `color.surface` / `color.content` / `color.border` / `color.semantic` / `color.wireframe` | プリミティブ配色スケール + セマンティックカラー |
| [typography.json](../tokens/typography.json) | `typography.fontFamily` / `fontWeight` / `fontSize` / `lineHeight` / `letterSpacing` / `scale` | フォント・サイズ・ウェイト・行間・字間、type スケール |
| [spacing.json](../tokens/spacing.json) | `spacing.{0..24}` / `spacing.layout` | 4px グリッドの余白スケール・レイアウト固定寸法 |
| [radius.json](../tokens/radius.json) | `radius.{none,sm,md,lg,xl,full}` | 角丸 |
| [sizing.json](../tokens/sizing.json) | `sizing.max-width` | コンテナ最大幅 |
| [elevation.json](../tokens/elevation.json) | `elevation.z` / `elevation.shadow` | z-index・shadow |
| [motion.json](../tokens/motion.json) | `motion.duration` / `motion.easing` | アニメーション |

すべて [Design Tokens Community Group format](https://design-tokens.github.io/community-group/format/) 準拠。`color` / `spacing` 等は具体値を持つ。`typography.scale.*` のみ `{typography.fontSize.xl}` 形式のエイリアス参照を使う。

## 選択ロード方針

| タスク内容 | 最低限読むファイル |
|---|---|
| 配色のみの調整 | `color.json` |
| レイアウト・余白調整 | `spacing.json` + `radius.json` |
| 文字スタイル調整 | `typography.json` |
| 新規コンポーネント・ページ生成 | `color.json` + `spacing.json` + `typography.json` + `radius.json`（必要に応じて `elevation.json` / `motion.json` / `sizing.json`） |
| アニメーション調整 | `motion.json` |
| z-index・shadow調整 | `elevation.json` |

---

## トークンの参照モデル（primitive → semantic → component）

参照は **FOUNDATIONS → semantic → primitive** の一方向。

```
FOUNDATIONS.md     ← 適用箇所のマッピング（「Primary は CTA」「セクション間 24px」等）
   │
   ▼
semantic           ← 用途ベースの語彙（surface / content / border、status.* 等）
   │
   ▼
primitive          ← 具体値の唯一の出所（primary 50-950、4/8/12px、#2661CF 等）
```

- **primitive**: 取りうる値の全集合。`color.primary`/`brand` の 50–950 スケール、`color.status.*`、`spacing` の 4px グリッド、`radius` 値など
- **semantic**: 用途で絞った語彙。実装ではここから選ぶのが原則（`color.surface`/`content`/`border`/`semantic`）
- **FOUNDATIONS.md**: 具体的な適用シーンを semantic に紐付ける

### カテゴリ別 選択ルール早見表

| カテゴリ | 通常の選択先（semantic） | primitive 直参照を許す場面 |
|---|---|---|
| color | `color.surface` / `content` / `border` / `semantic.*` | 操作色/ブランドのスケール参照（`color.primary.700`・`color.brand.500` 等）、`color.status.*` |
| spacing | `spacing.{1..24}`（4px グリッド）/ `spacing.layout.*` | — |
| typography | `typography.scale.*`（headline / body / label / caption） | 個別の `fontSize`/`fontWeight`/`lineHeight` で独自に組む場合 |
| radius | `radius.{sm,md,lg,full}` | `none` / `xl` |
| sizing | `sizing.max-width.*` | — |
| elevation | `elevation.z.*` / `elevation.shadow.*` | — |
| motion | `motion.duration.*` / `motion.easing.*` | — |

> 原則：**semantic から選ぶ**。primitive 直参照は「該当 semantic が無い・新設すると意味が壊れる」場合のみ。

---

## color の参照ルール

NestUI は**操作色（`color.primary`）とブランド色（`color.brand`）を分離**する。

- `color.primary.700`（#2661CF）= 主操作色（CTA・focus ring）。1 View に多用しない
- `color.brand.500`（#0098B9）= ブランド表現。操作色として使わない
- 面・文字・境界はセマンティックから選ぶ:
  - 背景: `color.surface.base`（ページ）/ `raised`（カード）/ `overlay`（モーダル）/ `sunken`（サイドバー等）
  - 文字: `color.content.primary` / `secondary` / `disabled` / `inverse`
  - 境界: `color.border.default` / `subtle` / `strong` / `focus`
- ステータス: 一般用途は `color.semantic.*`（`error`/`warning`/`success`/`info` + `*Bg`）。塗り分けが要る場合のみ `color.status.{success,warning,danger}.{base,fill,container,text}`
- 低忠実度ワイヤーフレームのみ `color.wireframe.*`
- **カラーコードのハードコード（`#2661CF` 等の直書き）は禁止**。トークン名で参照する

---

## spacing の参照ルール

`spacing` は 4px グリッドのフラットスケール（`1`=4px 〜 `24`=96px）。`margin` / `padding` / `gap` はこの値のみ使用。

### margin と padding の使い分け（運用規律）

値域は同じだが役割で使い分ける。

| 用途 | 役割 | 典型例 |
|---|---|---|
| 要素**間**の距離 | `gap` / 兄弟要素間 | アイコンと右隣テキスト、フィールド間 |
| 要素**内側**の余白 | `padding` | カード内側、ボタンのタップ領域 |

### コンポーネントは padding のみ持つ

コンポーネント自体に外側 `margin` を埋め込まない。外側余白は呼び出し側のレイアウト（親の `gap` / `margin`）で指定する。

- コンポーネント仕様（`components/base/<category>/<Component>.md`）に書くのは padding まで
- レイアウト固定寸法（サイドバー幅・ヘッダー高・コンテンツ最大幅）は `spacing.layout.*`

---

## radius / elevation / motion の参照ルール

- `radius`: 通常 `sm`(4px) / `md`(8px) / `lg`(12px) / `full` から選ぶ。角丸と角張りを同一 View で混在させない
- `elevation`: `elevation.z.*`（`base` / `dropdown`=20 / `sticky`=30 / `overlay`=40 / `modal`=50 / `toast`）と `elevation.shadow.*`（`none`/`sm`/`md`/`lg`）
- `motion`: `motion.duration.*`（`fast`=150ms / `normal`=200ms / `slow`=300ms）と `motion.easing.*`。装飾アニメーション禁止・状態変化のみ

---

## ハードコード禁止

すべてのカテゴリで、トークン外の値を CSS / JSX に直書きしてはならない。

- ❌ `padding: 13px;` `color: #2661cf;` `border-radius: 5px;`
- ✅ トークン参照（`color.primary.700` / `spacing.3` / `radius.sm` 等）
