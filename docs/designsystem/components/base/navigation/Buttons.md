# Component Guideline: Buttons

> NestUI Button 仕様。Tailwind CSS。値は `tokens/`（色は `color.primary.*` 等）を正とし、以下の class はその適用例。

## 1. Component Identity
- **Name**: Button
- **Variants**: `contained` / `outlined` / `neutral` / `lighted` / `danger` / `subtle`（+ アイコンボタン）
- **Responsibility**:
  - する: ユーザーアクションをトリガーする（送信・保存・削除・ダイアログ開閉）
  - しない: ページ遷移ナビゲーション（`<a>` を使う）、レイアウト容器
- **Target Platform**: デスクトップ Web アプリ（業務 SaaS）

**原則**: 3階層ピラミッド（重要度が高いほど目立たせ配置数を絞る）/ 1画面1CTA（Contained は最小限）/ 色だけで状態を伝えない（ボーダー・ウェイト併用）/ ラベルは結果を予測可能に。

---

## 2. Variants & States

**Variants**（重要度の高い順）

| Variant | 用途 | class（Medium） |
|---------|------|------|
| `contained`（CTA） | 画面内で最重要な1アクション。1画面1〜2個 | `bg-primary-700 text-white rounded hover:bg-primary-600 active:bg-primary-800` |
| `outlined` | 強めのセカンダリ・状態トグル | `bg-white text-primary-700 border border-primary-700 rounded hover:bg-primary-50 active:bg-primary-100` |
| `neutral` | 非主要（戻る・詳細）。**唯一、隣接複数配置可** | `bg-[#f7f9fb] text-[#3e5062] rounded hover:bg-[#edf0f3] active:bg-primary-100`（ボーダーなし） |
| `lighted` | 状態変化後のアクティブ表示。**Neutral とペア・単独禁止** | `bg-primary-100 text-primary-700 rounded hover:bg-primary-200` |
| `danger` | 破壊的操作の最終確認。**アウトライン型**（フィル `bg-red-*` 禁止） | `bg-white text-[#e93766] border border-[#e93766] rounded hover:bg-[#fef2f4] active:bg-[#fccfd9]` |
| `subtle` | 付随セカンダリ（キャンセル・閉じる）。背景なし | `text-[#3e5062] rounded hover:bg-[#edf0f3] active:bg-[#c4cdd9]` |

全 variant 共通: `inline-flex items-center justify-center gap-1 h-9 px-3 text-[14px] font-medium tracking-[0.28px] focus:outline-none focus-visible:ring-2 focus-visible:ring-primary-700 focus-visible:ring-offset-1 transition-colors`

**States**

| State | 変化 |
|-------|------|
| hover | 背景1段階（`hover:bg-primary-600` 等） |
| active | 背景2段階（`active:bg-primary-800`） |
| focus | `focus-visible:ring-2 ring-primary-700 ring-offset-1`（高さ不変。アイコンボタンは絶対配置リング） |
| disabled | `text-[#a1afc0] bg-[#edf0f3] cursor-not-allowed`（透明度ではなく専用配色） |
| loading | ラベル保持 + `.inline-spinner` 追加・`disabled opacity-75`（ラベルを消さない） |
| isSelected | アイコンボタン専用: `bg-[#ddedfc]` + `aria-pressed="true"`（Primary は `border-[#2661cf]`） |

---

## 3. サイズ / アイコンボタン

| サイズ | 高さ | class | font | gap | radius | アイコン |
|--------|------|-------|------|-----|--------|---------|
| Large | 44px | `h-[44px] px-4 text-[14px]` | 14px | `gap-1` | `rounded` | `w-[18px] h-[18px]` |
| Medium（既定） | 36px | `h-9 px-3 text-[14px]` | 14px | `gap-1` | `rounded` | `w-4 h-4` |
| Small | 28px | `h-7 px-3 text-[12px]` | 12px | `gap-0.5` | `rounded-sm` | `w-[14px] h-[14px]` |

letter-spacing: md/lg `tracking-[0.28px]`、sm `tracking-[0.24px]`（フォントの2%）。

**アイコンボタン**（Middle / Small の2サイズ）: `relative inline-flex items-center justify-center rounded-sm cursor-pointer` + `aria-label` 必須。
- Middle `w-7 h-7` / Small `w-5 h-5`（アイコンは `w-4 h-4`）
- Type Secondary（ボーダーなし）/ Primary（`border border-[#c4cdd9]`）
- フォーカスは絶対配置の FocusRing 要素で実装

---

## 4. Composition Rules

許可: テキストラベル / Leading・Trailing アイコン（`<svg>`）。
禁止: `<button>` の入れ子 / `<a>`（ナビは `<a>` の責務）/ フォーム要素。
配置: 同じ variant を横並びにしない（Neutral のみ例外）。階層が高いボタンを上/左、ダイアログフッターは右に最重要（例: Subtle → Contained）。

---

## 5. Layout & Spacing

- 最小高さ: Large 44px / Medium 36px / Small 28px（`py-0.5` 等の極小パディング禁止）
- タッチターゲット最小 44×44px（インラインリンク除く）
- アイコン + テキストは `gap-1`、アイコン `w-4 h-4`

---

## 6. Token Mapping

| 用途 | トークン |
|------|---------|
| contained 背景 / hover / active | `color.primary.700` / `.600` / `.800` |
| contained テキスト | `color.content.inverse`（white） |
| outlined テキスト・ボーダー | `color.primary.700` |
| neutral 背景 / hover | `#f7f9fb`(`color.surface.base`) / `#edf0f3`(`color.surface.sunken`) |
| neutral テキスト | `#3e5062`（content 補助。`color.content.secondary` 近傍） |
| danger テキスト・ボーダー / container | `color.status.danger.base`(#e93766) / `.container`(#fef2f4) |
| disabled テキスト / 背景 | `color.content.disabled`(#a1afc0) / `color.surface.sunken` |
| focus ring | `color.border.focus`(= primary-700) |

---

## 7. Accessibility
- ネイティブ `<button>` を使用（role 明示不要）
- アイコンのみ: `aria-label` 必須 / loading: `aria-busy="true"` + 操作不可 / トグル: `aria-pressed`
- キーボード: Tab 移動・Enter/Space 実行。Disabled はキーボードからも操作不可
- コントラスト: テキスト 4.5:1・アイコン 3:1 以上。フォーカスリングを必ず可視化

## 8. Content Guidelines
- 動詞始まり（「保存する」「削除する」「詳細を見る」）・結果が予測可能・最大3単語目安
- 「はい/いいえ」より具体的なラベル

## 9. Usage Do / Don't

```html
<!-- Do: CTA は contained を1つ -->
<button class="inline-flex items-center justify-center gap-1 h-9 px-3 text-[14px] font-medium tracking-[0.28px] text-white bg-primary-700 rounded hover:bg-primary-600 active:bg-primary-800 focus:outline-none focus-visible:ring-2 focus-visible:ring-primary-700 focus-visible:ring-offset-1 transition-colors">保存する</button>

<!-- Do: ダイアログフッター（Subtle → Contained） -->
<div class="flex justify-end gap-2">
  <button class="... text-[#3e5062] rounded hover:bg-[#edf0f3] ...">キャンセル</button>
  <button class="... text-white bg-primary-700 rounded hover:bg-primary-600 ...">確認する</button>
</div>
```

**Don't**: `bg-red-500` のフィル型 Danger（→アウトライン型）/ `focus:ring-primary-500/50`（→`ring-primary-700 ring-offset-1`）/ Loading でラベル非表示 / Lighted 単独使用 / アイコンボタンの `aria-label` 省略 / 同 variant の横並び（Neutral 除く）。

---

## 10. Implementation Notes
- スピナーは `.inline-spinner`（`COMPONENT_GUIDE.md` 状態設計）を再利用。アイコンは Lucide
- 思想は `FOUNDATIONS.md`、使い分けは `COMPONENT_GUIDE.md`
