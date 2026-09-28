# 仕様書フォーマット（YAML）

PRD や仕様書から抽出する構造化データの定義。

---

## スキーマ定義

```yaml
# ─────────────────────────────────────────
# 基本情報
# ─────────────────────────────────────────

spec_id: ""
# 英数字・ハイフンのみ。生成ファイルのフォルダ名に使う。
# 例: "case-management" / "contact-list" / "contract-form"

spec_title: ""
# 日本語の画面名。比較ページのタイトルに使う。
# 例: "案件管理" / "顧客一覧" / "契約書作成"

# ─────────────────────────────────────────
# オブジェクト
# ─────────────────────────────────────────

primary_object: ""
# この画面が主に扱うオブジェクト（1つ）。
# 例: "案件" / "顧客" / "物件" / "契約"

related_objects: []
# 主オブジェクトに紐づく関連オブジェクト。
# 例: ["顧客", "物件", "担当者"] / [] （なし）

# ─────────────────────────────────────────
# コアタスク
# ─────────────────────────────────────────

core_tasks:
  - name: ""
    # タスクの名前。動詞止めで記述する。
    # 例: "一覧を確認する" / "ステータスを更新する" / "詳細を閲覧する"

    frequency: daily
    # daily    = 毎日・複数回
    # weekly   = 週に数回
    # occasional = 月数回以下

    data_volume: medium
    # 一覧に表示するデータ件数の規模。
    # small  = 〜10件
    # medium = 10〜100件
    # large  = 100件以上

    bulk_possible: false
    # このタスクを複数件まとめて行うことがあるか。
    # true: チェックボックス選択→一括操作が有効
    # false: 1件ずつ操作

    detail_depth: medium
    # 詳細情報の深さ（フィールド数・関連情報の量）。
    # low    = 5フィールド以下・シンプル
    # medium = 6〜15フィールド・標準
    # high   = 16フィールド以上・関連情報が多い

# ─────────────────────────────────────────
# ユーザー
# ─────────────────────────────────────────

user_types:
  - role: ""
    # ユーザーの役割名。
    # 例: "営業担当" / "管理者" / "オーナー" / "入居者"

    it_literacy: medium
    # low    = PCに不慣れ・説明が必要
    # medium = 一般的なWebアプリを使える
    # high   = 効率重視・ショートカット活用

    primary_tasks: []
    # このユーザーが主に行うタスク名のリスト（core_tasks.name と一致させる）

# ─────────────────────────────────────────
# データ・UI特性
# ─────────────────────────────────────────

data_complexity: medium
# 全体的なデータ構造の複雑さ（フィールド数・関連オブジェクト数・例外の多さ）。
# low    = シンプル・フィールド少ない
# medium = 標準的
# high   = フィールド多い・関連が深い・例外が多い

has_filters: true
# 一覧にフィルター機能が必要か

has_sorting: true
# 一覧にソート機能が必要か

has_pagination: true
# 一覧にページネーションが必要か

has_bulk_operations: false
# 一括操作（複数選択→まとめて処理）が必要か

# ─────────────────────────────────────────
# 制約・文脈
# ─────────────────────────────────────────

constraints:
  - ""
  # 設計上の制約を列挙する。
  # 例:
  #   - "PC専用（モバイル対応なし）"
  #   - "既存プロダクトのナビゲーション内に収める"
  #   - "既存APIレスポンスの変更不可"
  #   - "初回リリースは読み取り専用・編集は次フェーズ"

notes: |
  # 仕様書から読み取れる重要な文脈・業務特性・例外を自由に記述する。
  # パターン生成時のサンプルデータや表現に活用される。
  # 例:
  #   案件は「工事」「修繕」「クレーム」等の種別がある。
  #   ステータスは5段階（受付→確認中→手配中→完了→クローズ）。
  #   1案件に複数の業者が関わることがある。
```

---

## 記入例（案件管理画面）

```yaml
spec_id: "case-management"
spec_title: "案件管理"
primary_object: "案件"
related_objects: ["顧客", "物件", "工事業者"]

core_tasks:
  - name: "一覧を確認する"
    frequency: daily
    data_volume: large
    bulk_possible: false
    detail_depth: low
  - name: "ステータスを更新する"
    frequency: daily
    data_volume: medium
    bulk_possible: true
    detail_depth: low
  - name: "詳細・関連情報を閲覧する"
    frequency: daily
    data_volume: medium
    bulk_possible: false
    detail_depth: high
  - name: "新規案件を登録する"
    frequency: weekly
    data_volume: small
    bulk_possible: false
    detail_depth: medium

user_types:
  - role: "営業担当"
    it_literacy: medium
    primary_tasks: ["一覧を確認する", "ステータスを更新する", "詳細・関連情報を閲覧する"]
  - role: "管理者"
    it_literacy: high
    primary_tasks: ["一覧を確認する", "新規案件を登録する", "ステータスを更新する"]

data_complexity: high
has_filters: true
has_sorting: true
has_pagination: true
has_bulk_operations: true

constraints:
  - "PC専用（モバイル対応なし）"
  - "既存プロダクトのナビゲーション内に収める"

notes: |
  案件は「工事」「修繕」「クレーム」「設備点検」等の種別がある。
  ステータスは5段階（受付→確認中→手配中→完了→クローズ）。
  1案件に複数の業者が関わることがある。
  担当者が変わっても履歴が追える必要がある。
```

---

## 抽出のコツ

| PRD の表現 | YAML の値 |
|-----------|----------|
| 「毎日確認する」「頻繁に見る」 | `frequency: daily` |
| 「週次で」「定期的に」 | `frequency: weekly` |
| 「まとめて」「一括で」「複数選択」 | `bulk_possible: true` |
| 「詳細情報が多い」「関連情報を参照」 | `detail_depth: high` |
| 「シンプルな確認」「一覧で完結」 | `detail_depth: low` |
| フィールド数が多い・例外が多い | `data_complexity: high` |
| 「PC のみ」「スマホ不要」 | constraints に追記 |
| 明記なし | 文脈から推定し `notes` に記す |
