# Component Guideline: Card

> NestUI Card 仕様。Tailwind CSS。値は `tokens/`（背景は `color.surface.*`、境界は `color.border.*` 等）を正とし、以下の class はその適用例。

## 1. Component Identity
- **Name**: Card
- **Variants**: `static`（静的）/ `link`（リンク・カード全体クリック可）
- **Responsibility**:
  - する: 関連する情報を視覚的な境界（背景・ボーダー）でグループ化するコンテナを提供する
  - しない: ページレベルのセクション分割（→ `Panel` / セクション見出し）、ダイアログ（→ `Modal`）、浮き上がる要素の格納
- **Target Platform**: デスクトップ Web アプリ（業務 SaaS）

**原則**: カードは関連情報をグループ化するコンテナ（境界で「まとまり」を明示）/ 1カード1コンテキスト（詰め込みすぎない・複数文脈は複数カードへ分割）/ 視覚的階層はタイトル → 説明 → アクションの順 / インタラクティブなカードのみ `hover:shadow-md` でクリック可能を暗示。

---

## 2. Variants & States

**Variants**

| Variant | 用途 | class（基本） |
|---------|------|------|
| `static` | 情報表示のみ（ダッシュボード・概要・メトリクス）。ホバー効果なし | `bg-white rounded-xl border border-slate-200 p-6 shadow-sm` |
| `link` | カード全体がクリック可能。一覧から詳細への遷移 | `<a>` で全体を囲む + `block hover:shadow-md transition-shadow rounded-xl` |

> `static` カード内に個別アクション（ボタン）を置く場合、ボタン側が個々のインタラクションを持つ（カード自体はホバーしない）。

**States**

| State | 変化 |
|-------|------|
| default | 背景 `bg-white`(`color.surface.raised`) + ボーダー `border-slate-200`(`color.border.default`) + `shadow-sm`(`elevation.shadow.sm`) |
| hover（link のみ） | `hover:shadow-md` + `transition-shadow`（浮き上がり） |
| active（link のみ） | さらにシャドウ変化（クリック中） |
| focus（link のみ） | `<a>` のフォーカスリングを可視化（キーボード操作時） |

> `static` カードに hover / active を付けない。色だけでカードの状態・種類を伝えない（アイコン・テキストを併用）。

---

## 3. パーツ / 解剖

| パーツ | 役割 | 必須 | 適用例 |
|--------|------|------|--------|
| Container | カードの外枠。背景・ボーダー・シャドウで境界を示す | Yes | `bg-white rounded-xl border border-slate-200 p-6 shadow-sm` |
| Image / Media | サムネイル・ビジュアル要素（上部） | No | `w-full h-48 object-cover`（`alt` 必須） |
| Title | カードの主題 | Yes | `text-lg font-bold text-slate-900` |
| Description | 補足説明文 | No | `mt-2 text-sm text-body` |
| Meta | 日付・カテゴリ・著者などのメタ情報 | No | `text-sm text-body` |
| Footer / Actions | ボタン・リンクなどの操作 | No | `px-6 py-3 border-t border-slate-200 flex justify-end gap-3` |

```
┌──────────────────────────────────┐
│ [Image / Media]                   │  ← Optional（メディアカード）
├──────────────────────────────────┤
│  Title                            │
│  Description                      │
│                                   │
│  [Meta]          [Action Button]  │  ← Footer は border-t で区切る
└──────────────────────────────────┘
```

---

## 4. Composition Rules

許可: すべてのコンポーネント（汎用コンテナとして使用可）。`Card` の入れ子は最大2階層まで。`static` カード内のアクションボタンは個別に配置可。
禁止: `<Modal>` / `<Drawer>` など浮き上がる要素の格納 / `link` カード内にネストしたインタラクティブ要素（リンク・ボタンの入れ子）/ カード直下の `<fieldset>` + `<legend>`（→ `<div>` + `<h2>` でセクション見出し）。
配置: メディアは上部、続いてタイトル → 説明 → フッター（アクション）。フッターは `border-t border-slate-200` で本文と区切り、アクションは右寄せ。

---

## 5. Layout & Spacing

- padding: `p-6`(24px) を標準。最低でも `p-4`(16px) を確保（`p-0` 禁止・極小パディング禁止）
- グリッド内のカード: `grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6`
- カード間・要素間の余白は呼び出し側のレイアウト（親の `gap`）で指定。カード自体は外側 `margin` を持たない
- フッターは `px-6 py-3 border-t border-slate-200`、アクション間は `gap-3`

**link カードのグリッド内・高さ揃え（必須）**

バッジ有無等でカード高さが異なってもホバー shadow の範囲を統一するため、以下を必ず付与する。

- `<a>` と `<article>` に `h-full`
- コンテンツ部を `flex flex-col flex-1`
- 末尾要素（価格等）に `mt-auto`

---

## 6. Token Mapping

| 用途 | トークン |
|------|---------|
| 背景 | `color.surface.raised`（white・`bg-white`） |
| ボーダー | `color.border.default`（`border-slate-200`） |
| ヘッダー / フッター区切り | `color.border.subtle`（`border-t border-slate-200`） |
| 角丸 | `radius.xl`（`rounded-xl`） |
| shadow（既定） | `elevation.shadow.sm`（`shadow-sm`） |
| shadow（link hover） | `elevation.shadow.md`（`shadow-md`） |
| padding | `spacing.6`(24px) / 最低 `spacing.4`(16px) |
| タイトル文字 | `color.content.primary`（`text-slate-900`） |
| 本文 / メタ文字 | `color.content.secondary`（`text-body` #081a27） |
| グリッド gap | `spacing.6`(24px) |

---

## 7. Accessibility
- **Role**: 静的コンテンツは `<article>`、`link` カードは `<a>`（フォーカス可能にする）
- `link` カードは `<a>` で全体を囲み Tab でフォーカス → Enter で遷移
- `static` カード内のアクションボタンは個別にフォーカス可能にする
- メディアカードの画像には適切な `alt` を付与する
- テキストのコントラスト 4.5:1 以上。カード背景 vs ページ背景は 3:1 以上（境界線で補完）
- カードグリッドはキーボードで順序通りにナビゲート可能にする

## 8. Content Guidelines
- タイトルは1行（折り返し不可）
- カード内は関連性の高い情報のみをグループ化する（1カード1コンテキスト）

## 9. Usage Do / Don't

```html
<!-- Do: 基本カード（静的） -->
<div class="bg-white rounded-xl border border-slate-200 p-6 shadow-sm">
  <h3 class="text-xl font-bold text-slate-900">カードタイトル</h3>
  <p class="mt-2 text-base text-body">カードの説明文がここに入ります。</p>
</div>

<!-- Do: メトリクスカード -->
<div class="bg-white rounded-xl border border-slate-200 p-6 shadow-sm">
  <p class="text-sm font-medium text-body">月間アクティブユーザー</p>
  <p class="mt-1 text-3xl font-bold text-slate-900">12,345</p>
  <p class="mt-1 text-sm text-emerald-600">+12.5% 前月比</p>
</div>

<!-- Do: アクションカード（フッターは border-t で区切り、右寄せ） -->
<div class="bg-white rounded-xl border border-slate-200 shadow-sm">
  <div class="p-6">
    <h3 class="text-lg font-bold text-slate-900">タイトル</h3>
    <p class="mt-2 text-sm text-body">説明文</p>
  </div>
  <div class="px-6 py-3 border-t border-slate-200 flex justify-end gap-3">
    <button class="h-8 px-3 text-[14px] font-medium text-[#3e5062] rounded hover:bg-[#edf0f3] transition-colors">キャンセル</button>
    <button class="h-8 px-3 text-[14px] font-medium text-white bg-primary-700 rounded hover:bg-primary-600 transition-colors">保存</button>
  </div>
</div>

<!-- Do: link カード（グリッド内・高さ揃え） -->
<a href="#" class="block h-full hover:shadow-md transition-shadow rounded-xl">
  <article class="h-full bg-white rounded-xl border border-slate-200 shadow-sm overflow-hidden flex flex-col">
    <img src="..." alt="画像の説明" class="w-full h-48 object-cover">
    <div class="p-4 flex flex-col flex-1">
      <h3 class="text-base font-medium text-slate-900">タイトル</h3>
      <p class="mt-auto pt-3 text-base font-bold text-slate-900">末尾要素</p>
    </div>
  </article>
</a>
```

**Don't**: `rounded-none`（→ `rounded-xl`）/ `shadow-lg`・`shadow-2xl`（→ `shadow-sm`、オーバーレイのみ `shadow-xl`）/ カード上部・左端の `border-t-4` カラーバー（→ 全周 `border border-slate-200`。AI生成UIの典型）/ `p-0` のパディングなし（→ 最低 `p-4`）/ `bg-gray-300` 以上の暗い背景（→ `bg-white` 標準）/ `link` カード内のインタラクティブ要素ネスト / `static` カードへの hover 効果付与 / 色だけで状態・種類を伝達。

---

## 10. Implementation Notes
- `link` カードのグリッドでは高さ揃え 3点セット（`h-full` / `flex flex-col flex-1` / `mt-auto`）を必ず適用し、ホバー shadow 範囲を統一する
- アクションボタンは `navigation/Buttons.md` 準拠（フッターは Subtle → Contained の順で右寄せ）
- 思想は `FOUNDATIONS.md`、トークン参照は `guidelines/TOKEN_GUIDE.md`、使い分けは `COMPONENT_GUIDE.md`
