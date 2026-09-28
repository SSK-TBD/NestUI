# Component Guideline: Empty prompt

## 1. Component Identity
- **Name**: Empty prompt
- **Variants**: `default` / `compact`
- **Responsibility**:
  - する: データが空の状態（0件・未設定）を一貫したレイアウトで表示し、次のアクションに誘導する
  - しない: エラー状態の表示（Alertsを使う）・ローディング中のプレースホルダー（Skeletonを使う）
- **Target Platform**: デスクトップ Web アプリ

---

## 2. Variants & States

**Variants**

| Variant | 用途 |
|---------|------|
| `default` | ページ・カード内のメインコンテンツ領域が空の場合 |
| `compact` | テーブルの空行やパネル内など、スペースが限られる場合 |

**States**

| State | 変化するプロパティ |
|-------|-----------------|
| default | 通常表示 |

---

## 3. Props / API

| Prop | Type | Default | Required | Description |
|------|------|---------|----------|-------------|
| `icon` | `ReactElement` | — | — | イラスト/アイコン（任意。省略時はデフォルトアイコン） |
| `title` | `string` | — | ✓ | 空状態のタイトル |
| `description` | `string` | — | — | 補足説明（任意） |
| `action` | `ReactElement` | — | — | CTAボタン（任意） |
| `variant` | `'default' \| 'compact'` | `'default'` | — | バリアント |

**Event Handlers**
- action 内の `<Button>` に設定

---

## 4. Composition Rules

**許可する子要素**
- なし（props のみ）

**Anti-patterns**
- NG: 空状態に独自レイアウトを作る → 必ず Empty prompt を使う（一貫した体験のため）
- NG: `title` を省略する（空の状態が何かユーザーに伝えること）

---

## 5. Layout & Spacing

| プロパティ | トークン | 値（default） |
|-----------|---------|-------------|
| コンテナ padding | `spacing.12` | 48px |
| アイコンサイズ | — | 48px（Icon xl） |
| アイコン-タイトル gap | `spacing.4` | 16px |
| タイトル-説明 gap | `spacing.2` | 8px |
| 説明-アクション gap | `spacing.6` | 24px |
| max-width | — | 400px（中央寄せ） |

| プロパティ | 値（compact） |
|-----------|-------------|
| コンテナ padding | `spacing.8` 32px |
| アイコンサイズ | 32px（Icon lg） |

---

## 6. Token Mapping

| 用途 | トークン | 値 |
|------|---------|-----|
| アイコン色 | `color.content.disabled` | — |
| タイトル | `color.content.primary` | — |
| 説明テキスト | `color.content.secondary` | — |

---

## 8. Accessibility

- **Role**: `status`（空状態の動的通知）
- **必須ARIA属性**:
  - `role="status"`, `aria-live="polite"`（データが動的に空になった場合）
- **コントラスト比**: タイトル 4.5:1、説明 4.5:1 以上

---

## 9. Content Guidelines

- `title` はユーザーに「何が空か」を伝える（「プロジェクトがまだありません」「結果が見つかりません」）
- `description` は理由または次のアクションを示す（「プロジェクトを作成して始めましょう」）
- `action` の Button ラベルは具体的（「プロジェクトを作成」「フィルターをリセット」）

---

## 10. Usage Do / Don't

**Do**
```tsx
// データが 0 件
<EmptyPrompt
  icon={<FolderIcon />}
  title="プロジェクトがまだありません"
  description="新しいプロジェクトを作成して、作業を始めましょう。"
  action={
    <Button variant="primary" onClick={openCreateModal}>
      プロジェクトを作成
    </Button>
  }
/>

// 検索結果が 0 件
<EmptyPrompt
  title="「{query}」に一致する結果はありません"
  description="検索キーワードを変えるか、フィルターを解除してください。"
  action={<Button variant="secondary" onClick={clearFilters}>フィルターをリセット</Button>}
/>
```

**Don't**
```tsx
// NG: 独自の空状態レイアウトを作る
<div className="empty-state">
  <img src="/empty.svg" />
  <p>データがありません</p>
</div>

// NG: title を省略する
<EmptyPrompt description="データがありません" />
```

---

## 11. Implementation Notes

- コンポーネントは常にコンテナの中央（`text-align: center`, `display: flex`, `flex-direction: column`, `align-items: center`）に配置
- テストチェックリスト:
  - [ ] icon / action ありなしの見た目確認
  - [ ] compact variant の確認
  - [ ] `role="status"` の確認

---

## 12. Change Log & Deprecation

| Version | 変更内容 | 対応期限 |
|---------|---------|---------|
| v1.0 | 初期リリース | — |
