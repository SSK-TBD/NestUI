# Component Guideline: Icon

## 1. Component Identity
- **Name**: Icon
- **Variants**: `default`（SVGアイコンラッパー）
- **Responsibility**:
  - する: SVGアイコンを統一したサイズ・カラー・アクセシビリティ設定でレンダリングする
  - する: アイコンは **Lucide（https://lucide.dev/icons/ / `lucide-react`）** を基本セットとし、`name` は Lucide のアイコン名（kebab-case。例 `triangle-alert` / `trash-2` / `bell` / `search` / `circle-check`）に一致させる
  - しない: アイコンの定義・SVGの管理（アイコンライブラリで管理）・クリックハンドラ（ButtonやIconButton で包む）
  - しない: **絵文字・dingbats・幾何学記号・矢印グリフ・`&#NNNN;`（HTML 数値文字参照）をアイコン代わりに使う**（アイコンは必ず本コンポーネント＝Lucide で表現する）
- **Target Platform**: デスクトップ Web アプリ

---

## 2. Variants & States

**Variants**

| Variant | 用途 |
|---------|------|
| `default` | 標準的なSVGアイコンラッパー |

**States**

| State | 変化するプロパティ |
|-------|-----------------|
| default | 親要素の `color` を継承 |
| （Icon 単体は hover / focus を持たない。Button 等で包んで制御） | — |

---

## 3. Props / API

| Prop | Type | Default | Required | Description |
|------|------|---------|----------|-------------|
| `as` | `ReactElement` | — | ✓ | 表示するアイコンコンポーネント |
| `size` | `'xs' \| 'sm' \| 'md' \| 'lg' \| 'xl'` | `'md'` | — | サイズ |
| `color` | `string` | `'currentColor'` | — | アイコン色（省略時は親の color を継承） |
| `aria-label` | `string` | — | — | 意味のあるアイコンの場合に必須 |
| `aria-hidden` | `boolean` | `true` | — | 装飾用アイコンは true（デフォルト） |

**Event Handlers**
- なし（クリック等は `<Button>` や `<IconButton>` で包む）

---

## 4. Composition Rules

**許可する子要素**
- なし（`as` prop でアイコンを渡す）

**Anti-patterns**
- NG: `<Icon>` に直接 `onClick` を設定する → `<Button variant="ghost">` で包む
- NG: 意味のあるアイコン（ボタンのラベル代替）に `aria-hidden="true"` を設定する → `aria-label` を付ける
- NG: 絵文字・記号グリフ（⚠ ▲ → ★ 等）や `&#9888;` 等の HTML 数値文字参照をアイコン代わりに使う → Lucide のアイコン（例 `triangle-alert`）に置換する

---

## 5. Layout & Spacing

| Size | px |
|------|----|
| `xs` | 12×12px |
| `sm` | 16×16px |
| `md` | 20×20px |
| `lg` | 24×24px |
| `xl` | 32×32px |

- SVG の `viewBox` は `0 0 24 24` に統一
- `fill="none"` + `stroke="currentColor"` または `fill="currentColor"` を統一する

---

## 6. Token Mapping

| 用途 | トークン | 値 |
|------|---------|-----|
| デフォルト色（テキストと同色） | `currentColor` | 親要素の color を継承 |
| サブテキスト色のアイコン | `color.content.secondary` | — |
| 無効アイコン | `color.content.disabled` | — |
| primary アクション | `color.brand.primary` | — |

---

## 8. Accessibility

- **Role**: なし（`aria-hidden="true"` が原則）
- **必須ARIA属性**:
  - 装飾用（ラベルと一緒に表示）: `aria-hidden="true"`（デフォルト）
  - 単体で意味を持つ（ラベルなし）: `aria-hidden="false"` + `aria-label="[説明]"`
- **コントラスト比**: 意味のあるアイコン 4.5:1 以上（非テキストコントラスト 3:1 以上）

---

## 9. Content Guidelines

- `aria-label` はアイコンが表す「アクション」または「状態」を簡潔に（「削除」「警告」「読み込み中」）
- アイコン単体を UI 要素として使う場合、Tooltip と組み合わせて補足する

---

## 10. Usage Do / Don't

**Do**
```tsx
// 装飾用（ラベルと一緒に使う）
<Button leftIcon={<Icon as={SaveIcon} />}>保存</Button>

// 意味のあるアイコン単体（aria-label 必須）
<Icon as={WarningIcon} size="md" aria-hidden={false} aria-label="警告" />

// クリック可能なアイコン（Button で包む）
<Button variant="ghost" accessibilityLabel="削除">
  <Icon as={TrashIcon} size="md" />
</Button>
```

**Don't**
```tsx
// NG: onClick を Icon に直接付ける
<Icon as={TrashIcon} onClick={handleDelete} />

// NG: 意味のあるアイコンに aria-hidden="true"
<Icon as={ErrorIcon} aria-hidden={true} />  // ← エラー状態を伝えられない
```

---

## 11. Implementation Notes

- アイコンライブラリは **Lucide（`lucide-react` / https://lucide.dev/icons/）に統一する**。アイコン名は Lucide のリガチャ名（kebab-case: `triangle-alert` / `trash-2` / `bell` / `search` / `circle-check` 等）に一致させる
- 絵文字・dingbats・幾何学記号・矢印グリフ・`&#NNNN;` をアイコンに使わない（禁則。severity: error）
- SVG は `width` / `height` を明示せず、親コンテナのサイズに追従する形で `size` prop でコントロール
- テストチェックリスト:
  - [ ] 5 サイズの表示確認
  - [ ] `currentColor` 継承の確認
  - [ ] aria-label が設定されているアイコンの読み上げ確認

---

## 12. Change Log & Deprecation

| Version | 変更内容 | 対応期限 |
|---------|---------|---------|
| v1.0 | 初期リリース | — |
