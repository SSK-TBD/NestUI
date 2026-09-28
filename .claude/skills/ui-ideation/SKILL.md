---
name: ui-ideation
description: "仕様書（PRD）を読み取り、UIデザインのパターン案を複数自動生成する。トリガー: 「パターン出し」「アイディア出し」「ideation」「デザイン案を出して」「パターン生成」「UIの案を出して」。仕様書またはPRDのパスを引数で受け取る。生成物は Design System（`docs/designsystem/`）に準拠する。"
user-invokable: true
args:
  - name: spec
    description: 仕様書またはPRDのパス（省略時はユーザーに確認）
    required: false
---

# UIアイディエーション

仕様書（PRD）から設計の観点（軸）を自動選択し、異なるアプローチのUIパターンを 3〜5 案生成する。
生成する HTML は Design System（`docs/designsystem/`）のトークン・コンポーネント仕様・レイアウトテンプレートに準拠させる。

---

## パス解決と出力先（実行の最初に確定）

実行開始時に以下を確定し、以降に登場する相対パスをこれに対して解決する。

1. **PROJECT_ROOT** ── `docs/designsystem/` を含むディレクトリ。通常はこのリポジトリのルート（`README.md` と `docs/` が並ぶ階層）。CWD 直下に `docs/designsystem/` が無ければ、親方向に探して最初に見つかった階層を採用する
2. **`<RUN>`** ── `<PROJECT_ROOT>/works/artifacts/ui-ideation/<yyyymmddhhmm>-<slug>/`
   - `<yyyymmddhhmm>` は実行開始時刻（JST）12桁。`date +%Y%m%d%H%M` で採番する
   - `<slug>` は**その実行が何を作った / 見たかを表す短い kebab-case**。要件の `spec_id`（無ければ `spec_title` を kebab-case 化したもの）を使う（例 `remittance-group-settings`）。同じ対象の作り直しは slug を変えず、日時だけが進む。判断に迷う場合はユーザーに確認する
   - **実行の最初に `mkdir -p <RUN>` で出力先を作る**（`works/` 自体が無ければ併せて作る。SKILL ディレクトリ配下に `outputs/` は作らない）
   - **上書きしない。** 再実行のたびに新しい `<yyyymmddhhmm>-<slug>` ディレクトリを作る

成果物も入力素材も `<RUN>/` に集約する。`works/` は検討の成果物置き場であり、Design System の正（`docs/designsystem/`）には何も書き込まない。

---

## 手順

### Step 0: 入力の確認

- 引数 `spec` が指定されている → そのファイルを読み込む
- 指定がない → 「仕様書またはPRDのパスを教えてください。直接テキストで貼り付けてもOKです」と返す
- ファイルが見つからない → ユーザーが直接テキストで貼り付けることを提案する

受け取った素材（仕様書・PRD・参考資料）は **`<RUN>/_inputs/` にコピーしてから読む**（原本は動かさない）。テキストを直接貼り付けられた場合は `<RUN>/_inputs/spec.md` として保存する。

### Step 1: 仕様の構造抽出

`references/spec-format.md` を読み込む。

仕様書（PRD）から以下の YAML 構造を抽出する:

```yaml
spec_id: "[英数字・ハイフンのみ。フォルダ名に使用]"
spec_title: "[画面名]"
primary_object: "[主オブジェクト。例: 案件、顧客、物件]"
related_objects: ["[関連オブジェクト]"]

core_tasks:
  - name: "[タスク名]"
    frequency: daily|weekly|occasional
    data_volume: small|medium|large
    bulk_possible: true|false
    detail_depth: low|medium|high

user_types:
  - role: "[役割名]"
    it_literacy: low|medium|high
    primary_tasks: ["[タスク名]"]

data_complexity: low|medium|high
has_filters: true|false
has_sorting: true|false
has_pagination: true|false
has_bulk_operations: true|false

constraints:
  - "[制約の説明]"

notes: |
  [自由記述。仕様書から読み取れる重要な文脈・例外・業務特性]
```

**抽出のポイント:**
- 「頻繁に」「毎日」→ `frequency: daily`
- 「まとめて」「一括」→ `bulk_possible: true`
- フィールド数が多い・関連が深い → `data_complexity: high`
- PRD に明記がない項目は文脈から推定し、推定であることを `notes` に記す

抽出した YAML をユーザーに提示する。大きな誤りや不明点がある場合のみ確認を求める（小さな推定は自己判断でよい）。

### Step 2: 軸の選択とパターン定義

`references/axis-rules.md` を読み込む。

仕様 YAML に基づいて適用する軸を決定し、3〜5 案のパターンを定義する。

各パターンに以下を定義する:

| 項目 | 説明 |
|------|------|
| **パターン名** | コンセプトを表す短い名前（例: 「ベテラン特化・インライン型」） |
| **ファイル名** | `pattern-a.html` / `pattern-b.html` ... |
| **ターゲット** | どのユーザーの・どの状態を主軸にするか |
| **インタラクション** | 主要操作の構造（Drawer/フルページ/インライン/Modal 等） |
| **情報密度** | コンパクト / 標準 / ゆったり |
| **フォーカス** | オブジェクト中心 / タスク中心 / 時系列中心 |
| **レイアウト** | 採用するレイアウトテンプレート（`pane-1` / `pane-2l` / `pane-2r` 等。`layout_patterns/layout-index.md` から選ぶ） |
| **Pro** | このパターンが得意なユースケース |
| **Con** | このパターンが苦手なユースケース |

パターン定義の一覧をユーザーに提示し、「このまま生成しますか？」と確認してから次へ進む。

### Step 3: Design System のコアドキュメントを読む（必須・毎回）

パターン HTML はすべて Design System に準拠させる。生成前に以下を必ず読む:

1. `docs/designsystem/tokens/` 配下のトークンファイル群 — トークン値の source of truth
   - `color.json` / `typography.json` / `spacing.json` / `sizing.json` / `radius.json` / `elevation.json` / `motion.json`
2. `docs/designsystem/guidelines/FOUNDATIONS.md` — 設計原則
3. `docs/designsystem/guidelines/TOKEN_GUIDE.md` — トークンの参照ルール（primitive → semantic → component）
4. `docs/designsystem/guidelines/COMPONENT_GUIDE.md` — コンポーネントの使い分けと Loading / Empty / Error / Complete の状態設計
5. `docs/designsystem/layout_patterns/layout-index.md` — レイアウトテンプレートの選択基準
6. `docs/designsystem/components/component-index.md` — コンポーネント一覧（軽量インデックス）

各パターンが使うレイアウトに応じて、`docs/designsystem/layout_patterns/pane-*.html` を **1 ファイルだけ**読む。その `:root`（トークン）と App Shell（`.app-shell` / `.sidenav` / `.navbar` / `.main` / `.zone--*`）の構造をそのまま継承する:

| テンプレート | 構成 | 典型的な用途 |
|---|---|---|
| `pane-1.html` | Main のみ | 全画面リスト・ウィザード・ランディング |
| `pane-2l.html` | Sub（左）＋ Main | フィルタ付きリスト・ツリーナビ |
| `pane-2t.html` | Sub（上）＋ Main | タブ切り替え・ステップバー |
| `pane-2r.html` | Main ＋ Helper（右） | 一覧選択 → 右に詳細（Drawer 型） |
| `pane-3lr.html` | Sub（左）＋ Main ＋ Helper（右） | フィルタ → 一覧 → 詳細 |
| その他 `pane-3*.html` | 3 ペイン構成 | `layout-index.md` の表を参照 |

各パターンで使うコンポーネントのみ、`docs/designsystem/components/base/<category>/<Component>.md` の仕様を読む（全件は読まない。`components/specific/` はこのリポジトリでは空の置き場なので参照しない）。代表的な対応:

| やりたいこと | 参照する DS |
|---|---|
| 一覧 + サイド詳細（Drawer 型） | `components/base/layout/Drawer.md`、`components/base/table/Table.md`、`pane-2r.html` |
| 軽量な確認・更新（Modal 型） | `components/base/layout/Modals.md` |
| 構造化データの一覧 | `components/base/table/Table.md`、`components/base/navigation/Pagination.md` |
| 一覧の絞り込み | `components/base/form/Search Bar.md`、`components/base/form/Chip.md`、`components/base/form/Dropdown.md` |
| インラインエディット一覧 | `components/base/table/Table.md`（editable）、`components/base/form/Text field.md` |
| ステップ進行（ウィザード） | `components/base/display/Steps.md` |
| 一括操作 | `components/base/form/Checkbox.md`、`components/base/navigation/Buttons.md` |
| 状態表示 | `components/base/display/Status tag.md`、`components/base/display/Badge.md`、`components/base/display/Empty prompt.md` |

### Step 4: 出力先ディレクトリの準備

出力先は **`<RUN>`**（= `works/artifacts/ui-ideation/<yyyymmddhhmm>-<slug>/`）。無ければ `mkdir -p <RUN>` で作ってから書き出す。
ディレクトリ名は実行日時なので、`<spec_id>`（Step 1 の YAML の識別子）は `<RUN>/README.md` に記録する。

以下の構成でファイルを生成する:

```
works/artifacts/ui-ideation/<yyyymmddhhmm>-<slug>/
  README.md          ← この実行の目次（spec_id・spec_title・入力の原本パス・パターン一覧）
  index.html         ← 比較ビュー
  pattern-a.html     ← パターンA
  pattern-b.html     ← パターンB
  pattern-c.html     ← パターンC
  _inputs/           ← この実行に渡した素材のコピー
  ...
```

`<RUN>/README.md` には、日時ディレクトリ名からは読み取れない情報（`spec_id` / `spec_title` / 入力の**原本のパス** / 各パターンの名前と狙い）を記載する。この README がその実行の唯一の目次になる。

### Step 5: パターン HTML の生成

`references/pattern-template.md` を読み込む。

各パターンを 1 ファイルずつ順番に生成する。

**生成ルール（必ず守る）:**

1. **自己完結 HTML** — レイアウトテンプレート（`pane-*.html`）の `:root` デザイントークンと基本構造を継承した、単体で開ける完全な HTML ファイル
2. **トークン使用** — カラーコード等の直書き禁止。`docs/designsystem/tokens/` のトークン値を `:root` の CSS 変数として持ち、`var(--...)` で参照する（`pane-*.html` の `:root` がそのまま使える）
3. **メタコメント** — ファイル先頭（`<!DOCTYPE html>` の直前）に以下を必ず含める:

```html
<!--
  PATTERN: [パターン名]
  TARGET:  [ターゲットユーザー状態]
  INTERACTION: [インタラクションモデル]
  DENSITY: [コンパクト/標準/ゆったり]
  FOCUS:   [フォーカス]
  LAYOUT:  [使用したレイアウトテンプレート（例: pane-2r）]
  PRO:     [得意なこと]
  CON:     [不得意なこと]
-->
```

4. **リアルなサンプルデータ** — `primary_object` に合わせた日本語の具体的なデータを使う。"Lorem ipsum" / "テスト" / "サンプル" 禁止
5. **DS 準拠** — `docs/designsystem/`（トークン・コンポーネント仕様・ガイドライン）に準拠する。`FOUNDATIONS.md` の原則と `COMPONENT_GUIDE.md` の使い分けに反しない
6. **コンポーネント活用** — `component-index.md` に存在するコンポーネントは必ずそれを使う（再実装しない）。variant・state は各コンポーネント仕様に定義されたもののみ
7. **インタラクション** — 主要操作（Drawer 開閉・Modal・タブ切替・バルク選択等）は JavaScript で動作させる

### Step 6: 比較 index.html の生成

全パターンを比較できる `index.html` を生成する。

**構成:**
- ページタイトル: `[spec_title] — デザインパターン比較`
- 各パターンのカード（クリックで別タブに開く）
  - カード上部: パターン名・ターゲット
  - カード中部: Pro / Con
  - カード下部: 「開く →」リンク
- ページ下部: 選定メモ用テキストエリア（`localStorage` で保存）
- スタイルは `docs/designsystem/tokens/` のトークンを `:root` 変数として参照する（直書き禁止）

### Step 7: 結果を報告する

```
## パターン生成完了

**仕様**: [spec_title]
**生成パターン数**: N 案

| パターン | ターゲット | インタラクション | 密度 | レイアウト |
|---------|-----------|----------------|------|-----------|
| A: [名前] | ... | ... | ... | ... |
| B: [名前] | ... | ... | ... | ... |
...

**出力先**: `works/artifacts/ui-ideation/<yyyymmddhhmm>-<slug>/`

### 次のステップ
- [ ] `index.html` をブラウザで開いてパターンを比較する
- [ ] 採用するパターンを決定し、`index.html` の選定メモに理由を残す
- [ ] 採用案をベースに、`docs/designsystem/` の仕様に沿って本実装へ展開する
- [ ] 使ったコンポーネントの見た目を確認したい場合は `docs/designsystem/gallery/index.html` をブラウザで開く
```
