# Component Index

コンポーネント選択用の軽量インデックス。詳細仕様が必要な場合のみ各ファイルを参照。
パスは `docs/designsystem/components/`（base/ または specific/）からの相対。
値は NestUI の実体。判断基準は `guidelines/COMPONENT_GUIDE.md` / `FOUNDATIONS.md`。

## Navigation

| Component | When to use | File |
|---|---|---|
| Buttons | 主要アクション・CTA・フォーム送信 | `base/navigation/Buttons.md` |
| Tabs | 同一コンテキスト内のビュー切り替え | `base/navigation/Tabs.md` |
| Side nav | アプリ全体のグローバルナビ（~188px / コンパクト ~56px） | `base/navigation/Side nav.md` |
| Navbars | ページ上部ヘッダー（Page Header） | `base/navigation/Navbars.md` |
| Breadcrumbs | 階層が3段以上ある画面の現在地表示 | `base/navigation/Breadcrumbs.md` |
| Pagination | 一覧データのページ分割 | `base/navigation/Pagination.md` |
| List Group | アイテムをリスト形式で表示・選択 | `base/navigation/List Group.md` |
| Tree view | 階層構造をツリー表示（Tree Connector） | `base/navigation/Tree view.md` |
| Text link | インライン/別タブ/ダウンロードのテキストリンク | `base/navigation/Text link.md` |
| Segmented button | 表示モード切替（2〜6項目の単一選択） | `base/navigation/Segmented button.md` |

## Form

| Component | When to use | File |
|---|---|---|
| Text field | 自由テキスト入力（TextField / CopyTextField / NumberInput 等） | `base/form/Text field.md` |
| Dropdown | 6択以上の単一選択（Select） | `base/form/Dropdown.md` |
| Checkbox | 複数選択・ON/OFF（単一/グループ/Indeterminate） | `base/form/Checkbox.md` |
| Radio | 3〜5択の単一選択（相互排他） | `base/form/Radio.md` |
| Toggle | 即時反映する ON/OFF 設定 | `base/form/Toggle.md` |
| Date Picker | 日付選択（カスタムカレンダー） | `base/form/Date Picker.md` |
| Chip | 選択のオン/オフ（Form / Filter / File Chip） | `base/form/Chip.md` |
| Copy button | テキストのコピー操作 | `base/form/Copy button.md` |
| Form | フォーム全体のレイアウト | `base/form/Form.md` |
| Search Bar | キーワード検索入力 | `base/form/Search Bar.md` |

## Display

| Component | When to use | File |
|---|---|---|
| Card | コンテンツをグループ化（static / link） | `base/display/Card.md` |
| Badge | 件数・ステータスをコンパクト表示 | `base/display/Badge.md` |
| Tag | 読み取り専用メタデータラベル | `base/display/Tag.md` |
| Status tag | オブジェクト状態（Circle / Check / MenuTag） | `base/display/Status tag.md` |
| Avatar | ユーザー/組織のアバター（画像/イニシャル/グループ） | `base/display/Avatar.md` |
| Alerts | インラインのエラー・警告・成功メッセージ | `base/display/Alerts.md` |
| Toast | 一時的なフィードバック通知 | `base/display/Toast.md` |
| Tooltip | ホバーで補足情報（top/bottom/left/right） | `base/display/Tooltip.md` |
| Skeleton | コンテンツ形状を予告するローディング | `base/display/Skeleton.md` |
| Loader | 不定形のローディング（ds-spinner / inline-spinner） | `base/display/Loader.md` |
| Progress | タスク・ファイル処理の進捗 | `base/display/Progress.md` |
| Steps | マルチステップの進捗（Stepper） | `base/display/Steps.md` |
| Activity | アクティビティフィード・コメント・ログ | `base/display/Activity.md` |
| Empty prompt | 空状態（状態設計は COMPONENT_GUIDE） | `base/display/Empty prompt.md` |
| Icon | アイコン単体（基本セット = **Lucide** / https://lucide.dev/icons/。絵文字・記号グリフをアイコンに使わない） | `base/display/Icon.md` |
| Indicators | KPI/メトリクス表示 | `base/display/Indicators.md` |
| Range Sliders | 数値範囲の選択 | `base/display/Range Sliders.md` |
| Chart | データ可視化グラフ | `base/display/Chart.md` |

## Layout

| Component | When to use | File |
|---|---|---|
| Modals | 確認・警告・完結タスク（ページ遷移なし） | `base/layout/Modals.md` |
| Drawer | 補助情報・編集フォーム（サイドから出現） | `base/layout/Drawer.md` |
| Popover | コンテキスト補足（クリックで開く・操作要素可） | `base/layout/Popover.md` |
| Dropdown menu | アクションメニュー（選択肢リスト） | `base/layout/Dropdown menu.md` |
| Accordion | 折りたたみ可能なセクション | `base/layout/Accordion.md` |
| Divider | 区切り線（水平/テキスト付き/垂直） | `base/layout/Divider.md` |

## Table

| Component | When to use | File |
|---|---|---|
| Table | 構造化データの一覧（compact / カラー / editable / expandable） | `base/table/Table.md` |