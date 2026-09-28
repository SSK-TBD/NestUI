```
   ███╗   ██╗███████╗███████╗████████╗██╗   ██╗██╗
   ████╗  ██║██╔════╝██╔════╝╚══██╔══╝██║   ██║██║
   ██╔██╗ ██║█████╗  ███████╗   ██║   ██║   ██║██║
   ██║╚██╗██║██╔══╝  ╚════██║   ██║   ██║   ██║██║
   ██║ ╚████║███████╗███████║   ██║   ╚██████╔╝██║
   ╚═╝  ╚═══╝╚══════╝╚══════╝   ╚═╝    ╚═════╝ ╚═╝
                    Agentic Design System Framework
```

# What is NestUI ?

"もっといい当たり前" が生まれ、育ち、還る場所。

何が良いか、なぜそれを選ぶか、どこで妥協しないか。
人間の判断を、AIが参照できる形で残す。

ユーザーへの誠実さ、半歩先へ導く意思、遠回りした記憶を、設計の態度として基盤に編み込む。
だからAIがどれだけ速く生成しても、作り手の意思は流されない。

育ったものは、世へ出て、季節がめぐればまた戻る。
還るたびに、"当たり前" は、すこし良くなっている。

**AIネイティブなデザインとは、速く平均をつくることではない。**
**AIと共に、もっといい「当たり前」を育てつづけること。**

# 概要

Markdownをインターフェースとして使用する（「Markdown-as-Interface」または「Markdown-UI」）構造化されたプレーンテキストMarkdownが、人間、AIエージェント、エンタープライズシステム間の直接的なインタラクションレイヤーとして機能する、新たなパラダイムが出現している。単なるドキュメントの域を超え、意味論的な契約、つまり機械が処理可能で、読みやすく、高度に適応可能なユーザーインターフェースとして機能する。

このような考えを踏まえて、AI が参照することを前提にした **Design System の雛形** を作成した。これらは、トークン・ガイドライン・コンポーネント仕様・レイアウトテンプレートを `docs/designsystem/` に集約し、UI の生成やレビューで判断基準を揃えるための単一の真実情報源 (source of truth) として使う。

本リポジトリは公開用の部分公開版。汎用コンポーネント（`components/base/`）の仕様本文を含み、サービス固有コンポーネント（`components/specific/`）は README のみの空の置き場になっている。

## ディレクトリ構成

```
NestUI/
├── README.md                          ← このファイル
├── LICENSE                            ← MIT License
├── NOTICE                             ← 商標と第三者素材（アイコン）のライセンス表記
├── LICENSE-APACHE-2.0                 ← Apache License 2.0 の本文（Material Icons 用）
├── .claude/
│   └── skills/                        ← Claude Code 用 Skill（下記「付属 Skill」）
│       └── ui-ideation/               ← 仕様書から UI パターン案を複数生成
├── works/                             ← 検討の成果物置き場
│   ├── artifacts/ui-ideation/         ← ui-ideation の出力先（空）
│   ├── mocks/                         ← 画面モックの原本（1 画面 1 ディレクトリ。直して育てる場所）
│   └── features/                      ← 検討中の施策
└── docs/
    └── designsystem/
        ├── gallery/
        │   └── index.html             ← NestUI Gallery（コンポーネント・トークン・レイアウトの見本＋仕様本文のビューア）。ダブルクリックで開ける
        ├── tokens/                    ← カテゴリ別のデザイントークン（Figma Variables 互換 JSON）
        │   ├── color.json
        │   ├── typography.json
        │   ├── spacing.json
        │   ├── sizing.json
        │   ├── radius.json
        │   ├── motion.json
        │   └── elevation.json
        ├── guidelines/                ← 設計原則・ルール
        │   ├── FOUNDATIONS.md         ← デザイン原則（何を選ぶか・なぜそうするか）
        │   ├── COMPONENT_GUIDE.md     ← コンポーネントの選択・使い分け・状態設計
        │   └── TOKEN_GUIDE.md         ← トークン参照機構（primitive → semantic → component）
        ├── components/
        │   ├── component-index.md     ← コンポーネント選択用の軽量インデックス
        │   ├── base/                  ← 汎用コンポーネント仕様
        │   │   ├── display/
        │   │   ├── form/
        │   │   ├── layout/
        │   │   ├── navigation/
        │   │   └── table/
        │   └── specific/              ← サービス固有のコンポーネント置き場
        └── layout_patterns/           ← ページ骨格テンプレート
            ├── README.md
            ├── layout-index.md        ← テンプレート選択基準
            └── pane-*.html            ← ペイン構成別の骨格 ×9
```

## Design System の構成要素

| 構成要素 | ファイル | 内容 |
|---|---|---|
| トークン値 | `tokens/{color,typography,spacing,sizing,radius,motion,elevation}.json` | 色・タイポグラフィ・余白・寸法・角丸・モーション・影の値。値の正はここ |
| トークン参照ルール | `guidelines/TOKEN_GUIDE.md` | primitive → semantic → component の参照ルート |
| 設計原則 | `guidelines/FOUNDATIONS.md` | UI 全体を貫くデザイン原則 |
| コンポーネント判断ルール | `guidelines/COMPONENT_GUIDE.md` | 混同しやすいコンポーネントの使い分け、Loading / Empty / Error / Complete の状態設計 |
| コンポーネント仕様 | `components/base/<category>/*.md` / `components/specific/*.md` | 個別コンポーネントの仕様。索引は `components/component-index.md` |
| ページ骨格 | `layout_patterns/pane-*.html` / `layout_patterns/layout-index.md` | ペイン構成で分類したページテンプレートと、その選択基準 |
| ギャラリー | `gallery/index.html` | 上の構成要素を見本付きで一覧できるビューア。md・トークン・骨格の内容を取り込んだ 1 ファイルで、単体で開ける |

## レイアウトテンプレート

| テンプレート | 構成 |
|---|---|
| `pane-1` | 補助ペインなし |
| `pane-2l` | 左にナビ・フィルタ |
| `pane-2t` | 上にタブ・ステップバー |
| `pane-2r` | 右に詳細パネル |
| `pane-3tl` | 上全幅 + 左サブ + メイン |
| `pane-3lt` | 左全高 + 右上サブ + 右下メイン |
| `pane-3lr` | 左サブ + メイン + 右詳細 |
| `pane-3tr` | 上全幅 + 左下メイン + 右下詳細 |
| `pane-3rt` | 右全高 + 左上サブ + 左下メイン |

※ 付属のテンプレートは、サービス固有の情報を含まないよう、ペインの骨格だけに抽象化している。そのため、これを元に生成した画面は、ペインの中身の組み方が実行ごとにぶれやすい。

## 使い方

- **トークンを差し替える**: `tokens/*.json` を自プロダクトの値（Figma Variables のエクスポート等）で置き換える
- **原則・ルールを書く**: `guidelines/` の各ガイドを自プロダクトの方針に合わせて編集する
- **コンポーネント仕様を書く**: `components/base/<category>/*.md` を自プロダクトの仕様に合わせて書き換え、固有部品は `components/specific/` に追加する
- **ページを組む**: `layout_patterns/layout-index.md` で最も近いテンプレートを選び、その骨格を元に中身を埋める
- **見た目を確かめる**: `gallery/index.html` をブラウザで開く。ギャラリーは取り込んだ時点の内容を表示するので、md・トークン・骨格を直してもギャラリーには自動で反映されない（正は常に md・JSON・HTML のほう）

## 付属 Skill

このリポジトリを Claude Code で開くと、`.claude/skills/` の Skill をそのまま使える。`docs/designsystem/` を参照の正として動く。

| Skill | 呼び方 | すること |
|---|---|---|
| `ui-ideation` | `/ui-ideation <仕様書のパス>`（「パターン出し」「デザイン案を出して」でも起動） | 仕様書（PRD）から設計の軸を選び、DS 準拠の UI パターン案を 3〜5 案の HTML として `works/artifacts/ui-ideation/<日時>-<slug>/` に生成する。比較用の `index.html` 付き |

`works/` は検討の成果物置き場。`artifacts/` は Skill の出力先、`mocks/`（画面モックの原本）と `features/`（検討中の施策）は手で直して育てる場所。どれも Design System の正（`docs/designsystem/`）には書き込まない。

## ライセンス

[MIT License](LICENSE)（Copyright (c) 2026 Canary Inc.）。

- **商標**: 「カナリー」「Canary」「カナリークラウド」「Canary Cloud」「NestUI」の名称・ロゴは Canary Inc. の商標で、MIT License の対象外
- **第三者素材**: アイコンに [Lucide](https://lucide.dev/)（ISC License。Feather 由来のアイコンは MIT License）・[Heroicons](https://heroicons.com/) v1（MIT License）・[Material Icons](https://fonts.google.com/icons)（Apache License 2.0）の SVG を埋め込んでいる。フォント（Noto Sans JP / Material Symbols Rounded）は Google Fonts から実行時に読み込み、同梱していない

商標の条項と、各アイコンのライセンス本文・埋め込みファイルの一覧は [NOTICE](NOTICE) を参照。
