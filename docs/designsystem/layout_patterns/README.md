# layout_patterns

ページ全体の骨組み（App Shell のペイン構成）を HTML で持つ。**一覧の正は `layout-index.md`**。

## 構成

| ファイル | 内容 |
|---|---|
| `layout-index.md` | 9 種のペイン構成の索引・選択基準・OOUI パターンとの対応 |
| `pane-*.html` ×9 | App Shell のスキャフォールド（Sub / Main / Helper の配置だけを持つ抽象的な骨組み） |

## 使い分け

- **新しいページを組む** → `layout-index.md` の選択基準で `pane-*` を 1 つ選び、その App Shell・グリッドを継承して中身を埋める
- **特定コンポーネントの細部** → `../components/base/<category>/<Name>.md`

## 規律

- 値はトークン（`../tokens/`）が正。骨組み HTML 内の hex/class は実装サンプルで、SSoT ではない
- 手書き視覚ギャラリーは作らない（drift する。視覚一覧が要るなら `.md` から生成する向きにする）
