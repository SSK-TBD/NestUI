# Layout Template Index

ページ生成時はこのインデックスでテンプレートを選択し、選んだ1ファイルのみ読むこと。

## 選択基準

- **補助ペインなし** → `pane-1`
- **左にナビ・フィルタ** → `pane-2l`
- **上にタブ・ステップバー** → `pane-2t`
- **右に詳細パネル** → `pane-2r`
- **上全幅 + 左サブ + メイン** → `pane-3tl`
- **左全高 + 右上サブ + 右下メイン** → `pane-3lt`
- **左サブ + メイン + 右詳細** → `pane-3lr`
- **上全幅 + 左下メイン + 右下詳細** → `pane-3tr`
- **右全高 + 左上サブ + 左下メイン** → `pane-3rt`

## テンプレート一覧

| ファイル | 構成 | デフォルトサイズ | 典型的な用途 |
|---|---|---|---|
| `pane-1.html` | Main のみ | — | 全画面リスト・ランディング・エラー画面 |
| `pane-2l.html` | Sub（左）＋ Main | Sub: 200px | フィルタ付きリスト・ツリーナビ |
| `pane-2t.html` | Sub（上）＋ Main | Sub: 48px | タブ切り替え・ウィザードステップ |
| `pane-2r.html` | Main ＋ Helper（右）| Helper: 320px | リスト選択 → 右に詳細 |
| `pane-3tl.html` | Sub1（上全幅）＋ Sub2（左）＋ Main | Sub1: 48px / Sub2: 200px | カテゴリ × タブ × コンテンツ |
| `pane-3lt.html` | Sub1（左全高）＋ Sub2（右上）＋ Main（右下）| Sub1: 200px / Sub2: 48px | ツリー選択 → タブ → コンテンツ |
| `pane-3lr.html` | Sub（左）＋ Main ＋ Helper（右）| Sub: 200px / Helper: 320px | リスト → 本文 → 詳細/アクション |
| `pane-3tr.html` | Sub（上全幅）＋ Main（左下）＋ Helper（右下）| Sub: 48px / Helper: 320px | タブ → コンテンツ ＋ 補足パネル |
| `pane-3rt.html` | Sub（左上）＋ Main（左下）＋ Helper（右全高）| Sub: 48px / Helper: 320px | フィルタ → 結果 ＋ 右プレビュー |

## OOUI パターンとの対応

NestUI のオブジェクト指向 UI（OOUI）パターンは以下の pane に対応する。該当する場合は対応する pane の構造をそのまま使う。

| OOUI パターン | 対応 pane | 用途 |
|---|---|---|
| Object List + Drawer | `pane-2r` / `pane-3lr` | 一覧 → 行クリックで右ドロワー詳細（案件・物件等） |
| Inbox Column Detail | `pane-2r` | 受信トレイ型（左カード縦積み → 右に詳細カラム） |
| Secondary Nav + Scrollspy | `pane-2l` / `pane-3lt` | 4セクション以上の設定・詳細（左サブナビ + Scrollspy） |

## 使い方

1. 上の表から最も近いテンプレートを1つ選ぶ（OOUI 該当時は上の対応表を参照）
2. そのHTMLファイルを読み、App Shell・グリッド構造を継承する
3. `zone--main` / `zone--sub` / `zone--helper` の中身を実装する
4. `:root {}` のトークンはテンプレートから引き継ぐ（再定義不要）
