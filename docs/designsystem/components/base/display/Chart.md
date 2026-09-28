# Component Guideline: Chart

## 1. Component Identity
- **Name**: Chart
- **Variants**: `line` / `bar` / `area` / `donut`
- **Responsibility**:
  - する: 時系列・比較・割合データを視覚化するコンテナを提供する
  - しない: 生データのテーブル表示（Tableを使う）・KPI単体の数値表示（Indicatorsを使う）
- **Target Platform**: デスクトップ Web アプリ

---

## 2. Variants & States

**Variants**

| Variant | 用途 |
|---------|------|
| `line` | 時系列トレンド。複数系列の比較 |
| `bar` | カテゴリ間の比較。horizontal/vertical |
| `area` | 累積・ボリューム変化の可視化 |
| `donut` | 割合・構成比（パーセント）の表示 |

**States**

| State | 変化するプロパティ |
|-------|-----------------|
| default | 通常表示 |
| loading | Skeleton で代替（Chart 内部は空） |
| empty | Empty prompt コンポーネントを表示 |
| hover (data point) | ツールチップ表示、ポイント拡大 |

---

## 3. Props / API

| Prop | Type | Default | Required | Description |
|------|------|---------|----------|-------------|
| `type` | `'line' \| 'bar' \| 'area' \| 'donut'` | `'line'` | ✓ | チャートタイプ |
| `data` | `ChartDataset[]` | — | ✓ | データセット |
| `labels` | `string[]` | — | ✓ | X軸ラベル（line/bar/area）または凡例（donut） |
| `height` | `number` | `240` | — | チャートの高さ（px） |
| `showLegend` | `boolean` | `true` | — | 凡例を表示するか |
| `showTooltip` | `boolean` | `true` | — | ツールチップを表示するか |
| `isLoading` | `boolean` | `false` | — | ローディング状態 |
| `accessibilityLabel` | `string` | — | ✓ | チャート全体の説明（スクリーンリーダー用） |

**ChartDataset 型**
```ts
type ChartDataset = {
  label: string
  data: number[]
  color?: string  // 省略時はデザインシステムのカラーパレットから自動割り当て
}
```

**Event Handlers**
- `onDataPointClick(datasetIndex, dataIndex)`: データポイントクリック時（任意）

---

## 4. Composition Rules

**許可する子要素**
- なし（props のみで制御）

**Anti-patterns**
- NG: `accessibilityLabel` なしで Chart を使う → スクリーンリーダーが内容を認識できない
- NG: 1つのチャートに10系列以上表示する → 視覚的に区別不可能。データを絞る

---

## 5. Layout & Spacing

| プロパティ | トークン | 値 |
|-----------|---------|-----|
| コンテナ padding | `spacing.4` | 16px |
| 凡例との gap | `spacing.3` | 12px |
| 最小高さ | — | 120px |
| 幅 | — | 100%（レスポンシブ） |

---

## 6. Token Mapping

| 用途 | トークン | 値 |
|------|---------|-----|
| グリッド線 | `color.border.subtle` | — |
| 軸テキスト | `color.content.secondary` | — |
| ツールチップ背景 | `color.surface.overlay` | — |
| データカラー 1 | `color.brand.primary` | — |
| データカラー 2 | `color.semantic.success` | — |
| データカラー 3 | `color.semantic.warning` | — |
| データカラー 4 | `color.semantic.info` | — |

---

## 8. Accessibility

- **Role**: `img`（チャートコンテナ）
- **必須ARIA属性**:
  - `role="img"`, `aria-label="[accessibilityLabel]"`
  - データテーブルフォールバック: `<details>` 内に `<table>` でデータを提供（視覚的に非表示）
- **コントラスト比**: データカラー同士の区別は色だけでなく線種・マーカー形状でも区別

---

## 9. Content Guidelines

- `accessibilityLabel` は「〇〇の推移（2026年1月〜3月）」のように期間・内容を含める
- 凡例ラベルは短く（10文字以内）
- 軸ラベルは単位を含める（「リクエスト数 / 時間」）

---

## 10. Usage Do / Don't

**Do**
```tsx
<Chart
  type="line"
  accessibilityLabel="API リクエスト数の推移（過去7日間）"
  data={[
    { label: 'GET /api/v1', data: [120, 145, 132, ...] },
    { label: 'POST /api/v1', data: [45, 52, 48, ...] },
  ]}
  labels={['月', '火', '水', '木', '金', '土', '日']}
/>
```

**Don't**
```tsx
// NG: accessibilityLabel なし
<Chart type="bar" data={data} labels={labels} />

// NG: 10系列以上表示
<Chart data={Array(12).fill(...)} />
```

---

## 11. Implementation Notes

- チャートライブラリ: Recharts または Chart.js（プロジェクトの依存関係に準ずる）
- ローディング中は `<Skeleton variant="rect" height={240} />` で代替
- テストチェックリスト:
  - [ ] 4 variant の見た目確認
  - [ ] ローディング状態（Skeleton）の確認
  - [ ] データが空の場合の Empty prompt 表示確認
  - [ ] `aria-label` の確認

---

## 12. Change Log & Deprecation

| Version | 変更内容 | 対応期限 |
|---------|---------|---------|
| v1.0 | 初期リリース | — |
