# Component Guideline: Indicators

## 1. Component Identity
- **Name**: Indicators（KPI Metric）
- **Variants**: `default` / `positive` / `negative` / `neutral`
- **Responsibility**:
  - する: KPI・メトリクスの数値とトレンドを表示する
  - しない: 時系列の推移グラフ（Chartを使う）・進捗バー（Progressを使う）
- **Target Platform**: デスクトップ Web アプリ

---

## 2. Variants & States

**Variants**

| Variant | 用途 |
|---------|------|
| `default` | トレンドなし。数値のみ表示 |
| `positive` | トレンド上昇（改善方向） |
| `negative` | トレンド下降（悪化方向） |
| `neutral` | 横ばい |

**States**

| State | 変化するプロパティ |
|-------|-----------------|
| default | 通常表示 |
| loading | Skeleton で代替 |

---

## 3. Props / API

| Prop | Type | Default | Required | Description |
|------|------|---------|----------|-------------|
| `value` | `string \| number` | — | ✓ | 表示する数値または文字列 |
| `label` | `string` | — | ✓ | メトリクスのラベル |
| `variant` | `'default' \| 'positive' \| 'negative' \| 'neutral'` | `'default'` | — | トレンド種別 |
| `change` | `string` | — | — | 変化量（例: `+12.3%`, `-5件`） |
| `changePeriod` | `string` | — | — | 比較期間（例: `前週比`, `前月比`） |
| `unit` | `string` | — | — | 数値の単位（例: `件`, `ms`, `%`） |
| `isLoading` | `boolean` | `false` | — | ローディング状態 |

**Event Handlers**
- なし（非インタラクティブ）

---

## 4. Composition Rules

**許可する子要素**
- なし（props のみで制御）

**Anti-patterns**
- NG: `value` に長い文字列を入れる（数値・短い文字列のみ）
- NG: トレンドの方向（positive/negative）とビジネス意味を混同する → 「エラー数が減少＝positive」なのでドメインに合わせて variant を設定

---

## 5. Layout & Spacing

| プロパティ | トークン | 値 |
|-----------|---------|-----|
| value font-size | `typography.fontSize.3xl` | 24px |
| value font-weight | `typography.fontWeight.bold` | 700 |
| label font-size | `typography.fontSize.sm` | 12px |
| value-label gap | `spacing.1` | 4px |
| change font-size | `typography.fontSize.sm` | 12px |
| change-changePeriod gap | `spacing.1` | 4px |

---

## 6. Token Mapping

| 用途 | トークン | 値 |
|------|---------|-----|
| value テキスト | `color.content.primary` | — |
| label テキスト | `color.content.secondary` | — |
| positive トレンド | `color.semantic.success` | — |
| negative トレンド | `color.semantic.error` | — |
| neutral トレンド | `color.content.secondary` | — |
| unit | `color.content.secondary` | — |

---

## 8. Accessibility

- **Role**: `status`（動的に更新される場合）
- **必須ARIA属性**:
  - `aria-label="[label]: [value][unit]"` で数値の意味を補足
  - トレンドアイコン: `aria-label="[上昇/下降/横ばい]"` + テキストでも色以外で表現
- **コントラスト比**: value テキスト 4.5:1 以上

---

## 9. Content Guidelines

- `value` は右詰め整形（数値は 3 桁カンマ区切り）
- `change` は必ず符号を付ける（`+12%` / `-5件`）
- `changePeriod` は短く（「前週比」「前月比」「前日比」）

---

## 10. Usage Do / Don't

**Do**
```tsx
<Indicators
  label="API エラー数"
  value="23"
  unit="件"
  variant="positive"  // エラーが減少＝改善方向
  change="-8件"
  changePeriod="前週比"
/>
```

**Don't**
```tsx
// NG: ビジネス意味と variant を逆にする
<Indicators
  label="API エラー数"
  value="23"
  variant="negative"  // エラー増加のみ negative。文脈次第
/>

// NG: value に長い文字列
<Indicators value="接続済み (3/5)" label="..." />
```

---

## 11. Implementation Notes

- ダッシュボードの KPI エリアでは `<Card>` 内に配置
- ローディング中は `<Skeleton variant="rect" width="120px" height="60px" />`
- テストチェックリスト:
  - [ ] 4 variant の見た目確認
  - [ ] トレンドアイコンの `aria-label` 確認

---

## 12. Change Log & Deprecation

| Version | 変更内容 | 対応期限 |
|---------|---------|---------|
| v1.0 | 初期リリース | — |
