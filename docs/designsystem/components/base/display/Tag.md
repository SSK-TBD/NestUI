# Component Guideline: Tag

> NestUI Tag 仕様。Tailwind CSS。値は `tokens/`（ボーダーは `color.border.default`）を正とし、以下の class はその適用例。

## 1. Component Identity
- **Name**: Tag
- **Variants**: `Enable`（標準） / `Disable`（無効）
- **Responsibility**:
  - する: オブジェクトに付与されたメタデータ（物件名・部屋番号・ラベル等）を静的に表示する
  - しない: フォームの選択肢・フィルター条件のオン/オフ（→ Chip）/ ステータス表示（→ Status tag / Badge）/ 削除可能ラベル（→ Chip）
- **Target Platform**: デスクトップ Web アプリ（業務 SaaS）

**原則**: 静的ラベル（ユーザーが操作しない読み取り専用）/ ボーダーで存在感を出す（背景を塗らず区切り感を表現）/ 単一スタイル（カラーバリエーションを持たない。状態は Enable / Disable の2種のみ）。

---

## 2. Variants & States

| Variant | 背景 | ボーダー | テキスト | 用途 |
|---------|------|----------|----------|------|
| `Enable`（標準） | `bg-white`（透明） | `border border-[#c4cdd9]` | `text-[#3e5062]` | 物件名・部屋番号・ラベル等のメタデータ |
| `Disable`（無効） | `bg-[#edf0f3]` | `border border-[#c4cdd9]` | `text-[#a1afc0]` + `aria-disabled="true"` | 編集不可・非アクティブ |

共通フォント: `text-[11px] font-medium tracking-[0.22px] leading-[1.3]`。
静的表示のためクリック・ホバーは持たない。テキストは `whitespace-nowrap` で1行に収める。

---

## 3. サイズ / Props

| プロパティ | 値 |
|-----------|-----|
| 外枠 | `inline-flex items-center justify-center` |
| padding | `px-[6px] pt-[4px] pb-[5px]` |
| 角丸 | `rounded-[4px]` |
| フォント | `text-[11px] font-medium tracking-[0.22px] leading-[1.3]` |

サイズは1種固定（`text-[11px]`）。主な Props（参考）: `disabled`（boolean）/ `label`（テキスト）。

---

## 4. Composition Rules

許可: テキストラベルのみ。
禁止: 削除ボタン（×）の付与（→ Chip）/ 選択肢・フィルターでの使用（→ Chip）/ カラーバリエーション（背景の塗り分け）/ Badge（ステータス）との混同使用。
配置: 複数並べる場合は `role="list"` でグループ化、各タグは `role="listitem"`。

---

## 5. Layout & Spacing

- タググループ: `flex flex-wrap gap-1.5`
- 角丸は `rounded-[4px]` 固定（`rounded-full` 禁止）
- 折り返し不可（`whitespace-nowrap`）

---

## 6. Token Mapping

| 用途 | トークン |
|------|---------|
| ボーダー（共通） | `color.border.default`(#C4CDD9) |
| Enable テキスト | `#3e5062`（content 補助。`color.content.secondary` 近傍） |
| Disable 背景 | `color.surface.sunken`(#EDF0F3) |
| Disable テキスト | `color.content.disabled`(#A1AFC0) |
| 角丸 | `radius.sm`（4px） |
| padding | `spacing` 微調整値（6/4/5px） |

---

## 7. Accessibility

| 属性 | 値 | 対象 |
|------|-----|------|
| `role="list"` | — | タググループのコンテナ |
| `role="listitem"` | — | 各タグ |
| `aria-disabled="true"` | — | Disable 状態のタグ |

- 色だけで状態を伝えない（Disable はテキスト色 + 背景色の両方で区別）

## 8. Content Guidelines
- 物件名・部屋番号・カテゴリ等のメタデータを簡潔に
- `text-xs`（12px）以上のフォントは使わない（`text-[11px]` 固定）

## 9. Usage Do / Don't

```html
<!-- Do: Enable -->
<span class="inline-flex items-center justify-center px-[6px] pt-[4px] pb-[5px] border border-[#c4cdd9] rounded-[4px] text-[11px] font-medium tracking-[0.22px] leading-[1.3] text-[#3e5062] whitespace-nowrap">サンプルハイツ 304号室</span>

<!-- Do: Disable -->
<span class="inline-flex items-center justify-center px-[6px] pt-[4px] pb-[5px] border border-[#c4cdd9] rounded-[4px] bg-[#edf0f3] text-[11px] font-medium tracking-[0.22px] leading-[1.3] text-[#a1afc0] whitespace-nowrap" aria-disabled="true">サンプルハイツ 304号室</span>

<!-- Do: タググループ -->
<div role="list" class="flex flex-wrap gap-1.5">
  <span role="listitem" class="inline-flex items-center justify-center px-[6px] pt-[4px] pb-[5px] border border-[#c4cdd9] rounded-[4px] text-[11px] font-medium tracking-[0.22px] leading-[1.3] text-[#3e5062] whitespace-nowrap">サンプルハイツ 304号室</span>
  <span role="listitem" class="inline-flex items-center justify-center px-[6px] pt-[4px] pb-[5px] border border-[#c4cdd9] rounded-[4px] text-[11px] font-medium tracking-[0.22px] leading-[1.3] text-[#3e5062] whitespace-nowrap">サンプルコート 1201号室</span>
</div>
```

**Don't**: `rounded-full`（→ `rounded-[4px]`）/ カラーバリエーション（背景の塗り分け）/ `text-xs` 以上 / 削除ボタン付与（→ Chip）/ 選択肢・フィルターに使用（→ Chip）/ Badge との混同。

---

## 10. Implementation Notes
- ステータス表示は `Status tag.md`、フィルター/選択・削除可能ラベルは Chip を参照
