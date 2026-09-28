# Component Guide

コンポーネントを選択・実装する際の判断ルール。混同しやすいコンポーネントの使い分けと、状態（Loading/Empty/Error/Complete）の統一設計を含む。
具体仕様（variant・props・Tailwind class）は各コンポーネント仕様 `components/base/<category>/<Component>.md` / `components/specific/<name>.md` を参照。色・寸法はトークン（`TOKEN_GUIDE.md`）を正とし、本ガイドに実値を直書きしない。

## 基本方針

1. `components/base/` に該当コンポーネントがあれば必ずそれを使う
2. 既存コンポーネントで要件を満たせない場合のみ `components/specific/` に追加する（下記「新規追加の条件」を満たすこと）
3. 新規追加する前に、既存 variant の組み合わせで解決できないか確認する
4. 判断に迷ったら独自のハイブリッドを作らず、既存のいずれかを選ぶ

## 判断に迷う場合の順序

1. `components/base/` の該当コンポーネント仕様を読む
2. `FOUNDATIONS.md` の原則に照らす
3. 既存の実装パターンに合わせる
4. それでも判断できない場合は、より保守的な選択をする（新規 variant より既存流用）

## 新規コンポーネントを追加してよい条件

以下を**すべて**満たす場合のみ `components/specific/` に仕様ファイルを作成してから実装する:

- `components/base/` に同等のものが存在しない
- 2 画面以上で再利用される
- プロジェクト固有の要件で base には追加できない

---

## コンポーネント使い分け

混同されやすいコンポーネントの判断基準。AI 生成時の誤選択を防ぐ SSoT。

### 1. ラベル系（Tag / Chip / Badge / Status tag）

```
ユーザーが操作（オン/オフ切替）する？
├─ Yes → Chip
│         ├─ フォーム入力の選択肢 → Form Chip
│         └─ 一覧フィルターの条件 → Filter Chip
└─ No（読み取り専用）
    ├─ オブジェクトの「状態」を示す？ → Status tag（静的=Circle/Check、変更トリガー=MenuTag）
    ├─ カウント・件数・通知数？ → Badge
    └─ メタデータ（物件名・ラベル等）？ → Tag
```

| 観点 | Tag | Chip | Badge | Status tag |
|------|-----|------|-------|-----------|
| 用途 | メタデータ表示 | 選択のオン/オフ | ステータス・件数 | オブジェクト状態 |
| 操作性 | 読み取り専用 | インタラクティブ | 読み取り専用 | 静的 or トリガー |
| 角丸 | `radius.sm` | `radius.full` | `radius.full` | `radius.sm` |
| ARIA | なし | `role="checkbox"`/`"radio"` | なし | `aria-haspopup`（MenuTag） |

**禁止**: Badge をクリッカブルに（→Chip）/ Tag で状態色分け（→Status tag）/ Chip を読み取り専用に（→Tag）/ Status tag をメタデータ表示に（→Tag）。

### 2. オーバーレイ系（Modals / Drawer / Popover / Tooltip / Dropdown menu / Dropdown(Select)）

```
操作をブロック（モーダル）する必要がある？
├─ Yes → Modals（確認ダイアログ / フォーム / 重要通知）
└─ No（非モーダル）
    ├─ 詳細情報の表示・編集パネル？ → Drawer
    ├─ アクションメニュー（選択肢リスト）？ → Dropdown menu
    ├─ 補足情報の説明テキスト？ → hover/focus=Tooltip、click(操作可)=Popover
    └─ フォーム選択肢のリスト？ → Dropdown（Select）
```

| 観点 | Modals | Drawer | Popover | Tooltip | Dropdown menu |
|------|-------|--------|---------|---------|----------|
| 目的 | 操作ブロック | 詳細表示・編集 | 補足情報 | 即時ヒント | アクション選択 |
| フォーカストラップ | 必須 | 必須 | 不要 | 不要 | 不要 |
| 背景オーバーレイ | あり | あり | なし | なし | なし |
| 内部に操作要素 | あり | あり | あり | **なし** | あり |
| `aria-modal` / `role` | `true`/`dialog` | `true`/`dialog` | `false`/`dialog` | —/`tooltip` | —/`menu` |

**禁止**: Tooltip 内にボタン/リンク（→Popover）/ Modals で閲覧のみ（→Drawer）/ Dropdown menu にフォーム入力（→Modals/Popover）/ Popover にフォーカストラップ（→Modals）。

### 3. ローディング系（Skeleton / Loader / Progress）

```
処理の進捗率がわかる？
├─ Yes → Progress
└─ No（不定）
    ├─ コンテンツの形状を予告できる？ → Skeleton
    └─ 単純な処理中インジケーター？ → Loader（単体=ds-spinner / ボタン内=inline-spinner / 部分=ドットローダー）
```

| 観点 | Skeleton | Loader | Progress |
|------|----------|---------|----------|
| 進捗率 | 不定 | 不定 | 確定（0〜100%） |
| コンテンツ予告 | あり | なし | なし |
| 使用場面 | 初期ロード・ページ遷移 | ボタン操作・短時間処理 | アップロード・長時間処理 |
| ARIA | `aria-busy="true"` | `role="status"` | `role="progressbar"` |

**禁止**: 全画面スピナーのみ（→Skeleton）/ 進捗率がわかるのに Loader（→Progress）。

### 4. 入力系（Text field / Dropdown / Radio / Checkbox / Toggle / Segmented button / Chip）

```
選択肢の数は？
├─ 2択(ON/OFF) → 即時反映=Toggle / フォーム送信=Checkbox 単体
├─ 3〜5択
│   ├─ 複数選択 → フォーム=Checkbox群/Form Chip / フィルター=Filter Chip
│   └─ 単一選択 → 表示モード切替=Segmented button / フォーム=Radio群/Form Chip / フィルター=Filter Chip
├─ 6択以上 → Dropdown(Select)
└─ 自由入力 → Text field
```

**禁止**: Toggle をフォーム送信型に（→Checkbox）/ 6択以上を Radio（→Dropdown）/ Segmented button をフォーム入力に（→Radio/Dropdown）。

### 5. ナビゲーション系（Side nav / Tabs / Breadcrumbs / Segmented button）

```
ナビゲーションのレベル？
├─ アプリ全体（グローバル）→ Side nav
├─ ページ内セクション切替 → Tabs
├─ 階層の現在位置（3段以上）→ Breadcrumbs
└─ 表示モード切替 → Segmented button
```

**禁止**: Tabs をグローバルナビに（→Side nav）/ Segmented button をページ遷移に（→Tabs）/ Breadcrumbs を Tabs 代替に。

### 6. フィードバック系（Toast / Alerts / Modals）

```
ユーザーの操作が必要？
├─ Yes（確認・入力）→ Modals
└─ No（通知のみ）→ 一時的(自動消去)=Toast / 持続的(画面内配置)=Alerts
```

| 観点 | Toast | Alerts | Modals |
|------|-------|--------|-------|
| 配置 | 画面隅にフロート | コンテンツ内インライン | 画面中央オーバーレイ |
| 消去 | 自動（Success: 3秒） | ユーザー操作/ページ遷移 | ユーザー操作 |
| 用途 | 結果通知 | 注意喚起・ヒント・エラー説明 | 確認・入力・警告 |

**禁止**: Toast でエラー詳細（→Alerts）/ Alerts を一時的成功通知に（→Toast）/ 確認不要なのに Modals（→Toast/Alerts）。

---

## 状態設計（Loading / Empty / Error / Complete）

画面の状態遷移を統一する。**全状態に次のアクション（CTA）を提示し、行き止まりを作らない**。エラーは叫ばず冷静に（Whisper）、完了時のみ温かい演出（Tactile）、全状態に適切な ARIA。

### State / Variant / Pattern の分類

| 概念 | 切替主体 | 本番に残る | 例 |
|------|---------|-----------|-----|
| State | ユーザー（UI操作） | 全て | タブ切替、グリッド/リスト |
| Variant | システム（データ条件） | 全て | 空状態、ローディング、エラー |
| Pattern | デザイナー（設計比較） | 1つだけ採用 | カード型 vs タイムライン型 |

> アプリ内にトグル UI があれば State、なければ Pattern。

### Loading

| 項目 | 内容 |
|------|------|
| パターン | Skeleton（コンテンツ領域）/ inline-spinner（ボタン操作後）/ ドットローダー（部分読み込み） |
| Skeleton 色 | 中立の沈め色（`color.surface.sunken` 相当）。色付き背景は禁止 |
| ARIA | `aria-busy="true"` + `role="status"` |

### Empty（Empty prompt）

データ0件・検索結果なし・初回利用時。**Primary CTA 必須**（次のアクションを明示）。

| 項目 | 内容 |
|------|------|
| コンテナ | 中央寄せ・最大幅 ~456px |
| アイコン | stroke スタイル `w-8 h-8`・`color.content.secondary`・**円形背景禁止** |
| 見出し / 説明 | `typography.scale.headline2` 相当 / `caption` 相当 |
| ボタン | Primary 必須・Secondary(Outlined) 任意 |
| ARIA | `role="status"` |

| バリエーション | アイコン | 見出し例 | ボタン |
|----------------|----------|----------|--------|
| データ0件 | inbox/folder | 「データがありません」 | Secondary+Primary |
| 検索結果なし | search | 「一致する結果がありません」 | Primary のみ |
| 初回利用 | sparkles/rocket | 「はじめましょう」 | Primary のみ |

> CTA 文言: 動詞始まり・ポジティブ・簡潔（2〜8文字）。否定表現を避ける。

### Error

冷静に「何が起きたか＋原因＋復帰手段」を伝える。**画面全体のエラー**と**フォーム送信後の BE エラー**で扱いが違う。

| 項目 | 画面全体のエラー（通信・404/500・権限不足） | フォーム内の BE エラー（送信後の検証・想定外） |
|------|------------------------------------|--------------------------------|
| 表示 | 画面中央の Error 状態 | フォーム上部に Alerts（`red`）を1つ集約 |
| アイコン | `color.semantic.error` 系・stroke スタイル | Alerts 標準（`AlertCircle`） |
| 見出し | 何が起きたか | 「〜できません」／「〜を確認してください」の2型 |
| 説明 | なぜ＋どうするか | 理由を1文（句点で終える） |
| CTA | Primary 必須（「再読み込み」「ホームに戻る」「ログイン/権限申請」） | 遷移先が実在する場合のみ。なければ文言のみで省略 |
| ARIA | `role="alert"` + `aria-live="assertive"` | 同じ |

> 文言の型（見出し／本文／対応方法の 3 要素）と Do / Don't は `components/base/display/Alerts.md` の Content Guidelines を参照。

### Complete

フォーム送信・登録・処理の完了。

| 項目 | 内容 |
|------|------|
| アイコン | `color.semantic.success` 系 |
| CTA | Secondary 推奨（「ホームに戻る」「一覧へ」） |
| アニメーション | アイコン scale → テキスト fadeIn（Tactile） |
| ARIA | `role="status"` + `aria-live="polite"` |

### 状態の使い分け早見

| シナリオ | 状態 | CTA |
|---------|------|-----|
| 初期表示（取得中） | Loading（Skeleton） | — |
| ボタン押下後の処理中 | Loading（inline-spinner） | — |
| データ0件 | Empty | 新規作成 |
| 検索結果なし | Empty | 検索条件を変更 |
| 通信エラー/404/権限 | Error（画面全体） | 再読み込み/ホーム/ログイン |
| フォーム送信後の BE 検証エラー | Error（Alerts `red`・フォーム上部） | 該当入力の修正 or 別操作へ誘導（遷移先がなければ文言のみ） |
| 送信・登録完了 | Complete | ホーム/一覧へ |

**禁止**: CTA なしの Empty/Error（行き止まり。ただしフォーム内 BE エラーは遷移先がなければ文言のみで可）/ Skeleton なしの真っ白ローディング / 赤背景のエラー画面（Whisper 違反）/ 常時回転スピナーのみの画面。

---

## Variant の扱い

- 既存 variant 一覧は各コンポーネント仕様ファイルを参照
- 仕様にない variant を追加する場合: コンポーネント仕様ファイルを先に更新してから実装
- Primary / Secondary / Destructive の 3 系統を基本とする

## State

各コンポーネントが持ちうる状態: `default` / `hover` / `active` / `disabled` / `focus`、`error` / `success` / `warning`（フォーム系）、`loading`（非同期）。
状態のスタイルはコンポーネント仕様に定義されたもののみ使用する。

## 内部スタイルの上書き

コンポーネントの内部構造を外部から直接スタイル上書きしない。状態・バリエーションは仕様に定義された variant / state / props で表現する。
（「直接上書き」の検出は詳細度の解釈を伴い静的判定だと誤検出が多いため、機械判定ではなく原則として守る）
