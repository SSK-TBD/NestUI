# Component Guideline: Divider

> NestUI Divider 仕様。Tailwind CSS。値は `tokens/`（色は `color.border.*` 等）を正とし、以下の class はその適用例。
> セクション間やコンテンツ間の視覚的な区切りを提供する。

## 1. Component Identity
- **Name**: Divider
- **Variants**: `horizontal`（水平・既定）/ `horizontal-text`（テキスト付き水平）/ `vertical`（垂直）/ `strong`（濃い線）
- **Responsibility**:
  - する: コンテンツ・セクション間の視覚的な区切りを静かに伝える
  - しない: コンテンツのグルーピング（カード・Panel を使う）/ クリッカブルな要素
- **Target Platform**: デスクトップ Web アプリ（業務 SaaS）

**原則**: 最小限の存在感（区切りは主張せず、構造を静かに伝える）/ セマンティック HTML（水平は `<hr>`、垂直・テキスト付きは `role="separator"`）/ 一貫した色（`border-slate-200` 標準、強調時のみ `border-slate-300`。`border-slate-400` 以上は禁止）。

---

## 2. Variants & States

**Variants**

| Variant | 説明 | 用途 |
|---------|------|------|
| `horizontal`（水平・既定） | 標準的な横区切り線 | セクション間・カード内の区切り |
| `horizontal-text`（テキスト付き水平） | 中央にラベルを配置した横区切り | 「または」等の代替手段の提示 |
| `vertical`（垂直） | 縦方向の区切り線 | 横並びコンテンツ間の分離 |
| `strong`（濃い線） | `border-slate-300` を使用 | より強い視覚的分離が必要な場合 |

**マージンバリエーション**: 標準 `my-6`（セクション内）/ 広い `my-8`〜`my-10`（セクション間）/ なし（カード内 `divide-y` 等で親が制御）。

**States**: 静的表示（インタラクションなし）。

---

## 3. パーツ

| パーツ | 役割 | 必須 | class（適用例） |
|--------|------|:----:|------|
| Line | 区切り線本体 | Yes | 水平: `<hr class="border-t border-slate-200">` / 垂直: `border-l border-slate-200 self-stretch` |
| Label | 中央のテキスト（テキスト付き） | No | `text-sm text-slate-500 flex-shrink-0` |
| Container | テキスト付き / 垂直の親要素 | テキスト付き・垂直 | `flex items-center gap-4`（左右の線は `flex-1 border-t border-slate-200`） |

---

## 4. Composition Rules

許可: 水平 `<hr>` 単体 / テキスト付きの中央ラベル / 横並びコンテンツ間の垂直線。
禁止: `<div>` + `border-b` で水平区切りを実装（→ `<hr>` を使用）/ ディバイダーをクリッカブルにする / 垂直ディバイダーの `role="separator"` + `aria-orientation="vertical"` 省略。

---

## 5. Layout & Spacing

| プロパティ | トークン | 値 |
|-----------|---------|-----|
| 標準マージン | `spacing.6` | `my-6`（24px） |
| 広いマージン | `spacing.8`〜`spacing.10` | `my-8`〜`my-10`（32〜40px） |
| テキスト付き gap | `spacing.4` | `gap-4`（16px） |
| 線の太さ | — | 1px（`border-t` / `border-l`） |

コンポーネント自体に外側 `margin` を埋め込まない場合は、呼び出し側のレイアウトで制御する（カード内 `divide-y` 等）。

---

## 6. Token Mapping

| 用途 | トークン |
|------|---------|
| 標準の線 | `border-slate-200`（`color.border.subtle`） |
| 濃い線（strong） | `border-slate-300`（`color.border.default`） |
| テキストラベル | `text-slate-500`（`color.content.secondary`） |

> `border-slate-400` 以上の濃い区切り線は禁止（`border-slate-200` / `border-slate-300` のみ）。

---

## 7. Accessibility

- 水平区切り: `<hr>` 要素を使用（暗黙の `role="separator"`）
- テキスト付き: Container に `role="separator"` を付与
- 垂直区切り: `role="separator" aria-orientation="vertical"` を付与
- 装飾的な区切り: `role="presentation"` で支援技術から隠す

---

## 8. Content Guidelines
- テキスト付きラベルは短く（「または」「ここまで」）。装飾的な長文を入れない
- 多用しない。区切りはコンテンツの構造が成立しない箇所のみに使う

---

## 9. Usage Do / Don't

```html
<!-- Do: 水平（デフォルト） -->
<hr class="border-t border-slate-200 my-6">

<!-- Do: テキスト付き水平 -->
<div role="separator" class="flex items-center gap-4 my-6">
  <div class="flex-1 border-t border-slate-200"></div>
  <span class="text-sm text-slate-500 flex-shrink-0">または</span>
  <div class="flex-1 border-t border-slate-200"></div>
</div>

<!-- Do: 垂直 -->
<div class="flex items-center gap-6">
  <div class="text-sm text-body">セクション A</div>
  <div role="separator" aria-orientation="vertical" class="border-l border-slate-200 self-stretch"></div>
  <div class="text-sm text-body">セクション B</div>
</div>

<!-- Do: 濃い線 -->
<hr class="border-t border-slate-300 my-8">
```

**Don't**: `<div>` の `border-b` で水平区切りを実装（→ `<hr>`）/ `border-slate-400` 以上の太いボーダー色（→ `border-slate-200`）/ 垂直ディバイダーの `aria-orientation` 省略 / ディバイダーをクリッカブルにする。

---

## 10. Implementation Notes
- 右ペインの関連情報セクションを `<hr>` だけで区切らない。1 セクション 1 枚のカードを使う
- カード/Alert 上部・左端のカラーバー（`border-t-4` / `border-l-4`）の代替としてディバイダーを使わない
- 思想は `FOUNDATIONS.md`、使い分けは `COMPONENT_GUIDE.md`
