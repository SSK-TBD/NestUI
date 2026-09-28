# Component Guideline: Form

## 1. Component Identity
- **Name**: Form / FormField
- **Variants**: `vertical`（デフォルト）/ `horizontal`
- **Responsibility**:
  - する: フォームフィールドのラベル・バリデーション状態・エラーメッセージ・必須/任意表示を統一管理する
  - しない: 個々の入力コントロール（各 Input / Checkbox / Select が責任を持つ）
- **Target Platform**: デスクトップ Web アプリ

---

## 2. Variants & States

**Variants**

| Variant | 用途 |
|---------|------|
| `vertical` | ラベルが上、フィールドが下（デフォルト）。フォーム全体に統一感 |
| `horizontal` | ラベルが左、フィールドが右。設定画面・詳細編集など |

**States**

| State | 変化するプロパティ |
|-------|-----------------|
| default | 通常表示 |
| error | フィールドボーダー: `color.semantic.error`、エラーテキスト表示 |
| success | フィールドボーダー: `color.semantic.success`、成功テキスト表示 |
| warning | フィールドボーダー: `color.semantic.warning`、警告テキスト表示 |
| disabled | フィールドとラベルの `opacity: 0.4` |

---

## 3. Props / API

**Form**

| Prop | Type | Default | Required | Description |
|------|------|---------|----------|-------------|
| `onSubmit` | `(values: Record<string, any>) => void` | — | — | フォーム送信ハンドラ |
| `layout` | `'vertical' \| 'horizontal'` | `'vertical'` | — | レイアウト方向 |

**FormField**

| Prop | Type | Default | Required | Description |
|------|------|---------|----------|-------------|
| `label` | `string` | — | ✓ | フィールドラベル |
| `name` | `string` | — | ✓ | フィールド名（フォーム値のキー） |
| `isRequired` | `boolean` | `false` | — | 必須フィールド |
| `isOptional` | `boolean` | `false` | — | 任意ラベルを表示 |
| `helperText` | `string` | — | — | 補足テキスト（フィールド下部） |
| `errorText` | `string` | — | — | エラーメッセージ |
| `successText` | `string` | — | — | 成功メッセージ |
| `warningText` | `string` | — | — | 警告メッセージ |
| `status` | `'default' \| 'error' \| 'success' \| 'warning'` | `'default'` | — | バリデーション状態 |

**Event Handlers**
- `onSubmit(values)`: フォーム送信

---

## 4. Composition Rules

**許可する子要素**（FormField 内）
- `<Input>`, `<InputNumber>`, `<Dropdown>`, `<Checkbox>`, `<Radio>`, `<Toggle>`, `<DatePicker>`, `<SearchBar>`

**禁止する子要素**
- `<FormField>` 内に別の `<FormField>` を入れる（フィールドの入れ子）

**Anti-patterns**
- NG: 独自のラベルとエラー表示を各コンポーネントで実装する → FormField を必ず使う（統一感）
- NG: 必須マーク（`*`）を FormField を使わずにラベルに直接書く
- NG: `isRequired` と `isOptional` を同時に指定する

---

## 5. Layout & Spacing

| プロパティ | トークン | 値 |
|-----------|---------|-----|
| フィールド間の gap (vertical) | `spacing.4` | 16px |
| ラベル-フィールド gap (vertical) | `spacing.1` | 4px |
| ラベル幅 (horizontal) | — | 160px（固定） |
| helperText / errorText の padding-top | `spacing.1` | 4px |
| フォーム padding | `spacing.6` | 24px |

---

## 6. Token Mapping

| 用途 | トークン | 値 |
|------|---------|-----|
| ラベルテキスト | `color.content.primary` | — |
| 必須マーク (`*`) | `color.semantic.error` | — |
| 任意テキスト | `color.content.secondary` | — |
| helperText | `color.content.secondary` | — |
| エラーテキスト | `color.semantic.error` | — |
| 成功テキスト | `color.semantic.success` | — |
| 警告テキスト | `color.semantic.warning` | — |

---

## 8. Accessibility

- **Role**: `form`（フォームコンテナ）
- **必須ARIA属性**:
  - フォーム: `aria-label="[フォーム名]"` または `aria-labelledby`
  - FormField: `<label for="[input-id]">` で input と紐付け
  - エラー時: input に `aria-invalid="true"`, `aria-describedby="[error-id]"`
  - 必須フィールド: `aria-required="true"`
- **キーボード操作**:
  - `Tab`: フィールド間フォーカス移動
  - `Enter`（フォーカスがボタン以外の時）: フォーム送信を防ぐ（`type="submit"` のボタンのみ送信）
- **コントラスト比**: ラベル・エラーテキスト 4.5:1 以上

---

## 9. Content Guidelines

- 必須フィールドは `isRequired` で `*` マークを自動付与（ラベルに直書きしない）
- すべてのフィールドが必須の場合、「すべて必須」をフォームの冒頭に記載する方が読みやすい
- エラーテキストはユーザーが修正できる具体的な内容にする（「必須項目です」より「プロジェクト名を入力してください」）

---

## 10. Usage Do / Don't

**Do**
```tsx
<Form onSubmit={handleSubmit} layout="vertical">
  <FormField
    label="プロジェクト名"
    name="projectName"
    isRequired
    status={errors.projectName ? 'error' : 'default'}
    errorText={errors.projectName}
  >
    <Input placeholder="例: my-api-service" />
  </FormField>

  <FormField
    label="説明"
    name="description"
    isOptional
    helperText="プロジェクトの目的を簡潔に記述してください"
  >
    <Textarea />
  </FormField>
</Form>
```

**Don't**
```tsx
// NG: FormField を使わず独自ラベル・エラー表示を実装する
<div>
  <label>プロジェクト名 <span style={{ color: 'red' }}>*</span></label>
  <Input />
  <span style={{ color: 'red' }}>必須です</span>
</div>
```

---

## 11. Implementation Notes

- バリデーションライブラリ（react-hook-form, zod など）と組み合わせる場合、`status` と `errorText` を外部から制御
- `onSubmit` 前に HTML5 バリデーション（`required` 属性）を防いで独自バリデーションを走らせる
- テストチェックリスト:
  - [ ] 必須フィールドの `aria-required` 確認
  - [ ] エラー時の `aria-invalid` と `aria-describedby` 確認
  - [ ] Tab キーでのフィールド間移動確認
  - [ ] エラーメッセージが視覚的に明確であること

---

## 12. Change Log & Deprecation

| Version | 変更内容 | 対応期限 |
|---------|---------|---------|
| v1.0 | 初期リリース | — |
