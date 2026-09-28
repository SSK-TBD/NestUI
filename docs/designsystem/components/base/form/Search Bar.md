# Component Guideline: Search Bar

## 1. Component Identity
- **Name**: Search Bar
- **Variants**: `default` / `inline`
- **Responsibility**:
  - する: テキストキーワードによる検索・フィルタリングの入力を提供する
  - しない: フォームの一般的なテキスト入力（Inputを使う）・フィルタチップの管理（Filter Groupを使う）
- **Target Platform**: デスクトップ Web アプリ

---

## 2. Variants & States

**Variants**

| Variant | 用途 |
|---------|------|
| `default` | Navbar や検索専用エリアに配置。フルボーダー付き |
| `inline` | テーブルやリストの上部に配置。コンパクトな見た目 |

**States**

| State | 変化するプロパティ |
|-------|-----------------|
| default (empty) | 虫眼鏡アイコン + placeholder 表示 |
| hover | ボーダー: `color.content.secondary` |
| focus | ボーダー: `color.border.focus`、`outline: 2px solid color.border.focus` |
| has-value | クリアボタン（×）表示 |
| loading | スピナー表示（検索処理中） |
| disabled | `opacity: 0.4`、`cursor: not-allowed` |

---

## 3. Props / API

| Prop | Type | Default | Required | Description |
|------|------|---------|----------|-------------|
| `value` | `string` | — | — | 入力値（制御） |
| `defaultValue` | `string` | `''` | — | 初期値（非制御） |
| `placeholder` | `string` | `'検索...'` | — | プレースホルダー |
| `variant` | `'default' \| 'inline'` | `'default'` | — | バリアント |
| `isLoading` | `boolean` | `false` | — | 検索処理中のローディング |
| `isDisabled` | `boolean` | `false` | — | 非活性 |
| `debounceMs` | `number` | `300` | — | debounce 遅延（ms）。0 で無効 |
| `onChange` | `(value: string) => void` | — | — | 入力変更（debounce済み） |
| `onSearch` | `(value: string) => void` | — | — | Enter キーで即時実行 |
| `onClear` | `() => void` | — | — | クリアボタンクリック |

**Event Handlers**
- `onChange(value: string)`: debounce 後の値変更
- `onSearch(value: string)`: Enter キーでの即時検索
- `onClear()`: クリアボタン

---

## 4. Composition Rules

**許可する子要素**
- なし（props のみで制御）

**Anti-patterns**
- NG: `debounceMs=0` にして毎キーストロークで API コールする → サーバー負荷。300ms 以上を推奨
- NG: Search Bar をフォームの一般的な入力フィールドとして使う → Input を使う

---

## 5. Layout & Spacing

| プロパティ | トークン | 値 |
|-----------|---------|-----|
| padding (vertical) | `spacing.2` | 8px |
| padding (horizontal) | `spacing.3` | 12px |
| 左アイコン幅 | `spacing.4` + icon | 32px |
| 右クリアボタン幅 | `spacing.4` + icon | 32px |
| border-radius | `radius.md` | 8px |

- 最小幅: 200px
- 最大幅: コンテナに追従（Navbar 内では max-width: 480px 推奨）

---

## 6. Token Mapping

| 用途 | トークン | 値 |
|------|---------|-----|
| 背景 | `color.surface.input` | — |
| ボーダー | `color.border.default` | — |
| フォーカスボーダー | `color.border.focus` | — |
| placeholder | `color.content.disabled` | — |
| テキスト | `color.content.primary` | — |
| 虫眼鏡アイコン | `color.content.secondary` | — |
| クリアボタン hover | `color.content.primary` | — |

---

## 8. Accessibility

- **Role**: `searchbox`（または `role="search"` の `<form>` 内に配置）
- **必須ARIA属性**:
  - `role="searchbox"` または `type="search"`
  - `aria-label="検索"` または `aria-labelledby`
  - クリアボタン: `aria-label="検索をクリア"`
- **キーボード操作**:
  - `Enter`: `onSearch` を即時実行
  - `Escape`: 入力をクリアしてフォーカスを外す
  - `Tab`: フォーカス移動
- **コントラスト比**: テキスト・プレースホルダー 4.5:1 / 3:1 以上

---

## 9. Content Guidelines

- placeholder は「検索...」または「[対象]を検索...」（例: 「プロジェクトを検索...」）
- 検索対象が明確でない場合は placeholder で示す

---

## 10. Usage Do / Don't

**Do**
```tsx
// debounce 付き（デフォルト 300ms）
<SearchBar
  value={query}
  onChange={setQuery}
  onSearch={handleSearch}
  placeholder="プロジェクトを検索..."
/>

// ローディング状態
<SearchBar isLoading={isSearching} value={query} onChange={setQuery} />
```

**Don't**
```tsx
// NG: debounce なしで毎回 API コール
<SearchBar debounceMs={0} onChange={fetchResults} />

// NG: 一般的なフォーム入力に Search Bar を使う
<SearchBar placeholder="ユーザー名" onChange={setUsername} />
```

---

## 11. Implementation Notes

- debounce は内部で `useCallback` + `setTimeout` で実装（外部ライブラリ不要）
- `onClear` 後は input にフォーカスを戻す
- テストチェックリスト:
  - [ ] debounce の動作確認（300ms 後に onChange が呼ばれること）
  - [ ] Enter キーで onSearch が即時呼ばれること
  - [ ] クリアボタンの表示/非表示切り替え確認

---

## 12. Change Log & Deprecation

| Version | 変更内容 | 対応期限 |
|---------|---------|---------|
| v1.0 | 初期リリース | — |
