# Component Guideline: Progress

> NestUI Progress（リニアプログレスバー）仕様。Tailwind CSS。値は `tokens/`（色は `color.primary.*` / `color.status.*`）を正とし、以下の class はその適用例。

## 1. Component Identity
- **Name**: Progress
- **Variants**: `primary`（既定） / `success` / `warning` / `error`（+ 確定 / 不確定の表示形態）
- **Responsibility**:
  - する: 操作・プロセスの進行状況を 0〜100% のバーで視覚化する
  - しない: ローディング状態（→ Loader）/ ステップ進行（→ Steps）
- **Target Platform**: デスクトップ Web アプリ（業務 SaaS）

**原則**: 進捗の可視化 / 4色で意味を伝える / 不確定対応（完了時間が不明な場合はアニメーションループ）/ ラベル付き（進捗率テキストで正確な情報を補助）。

---

## 2. Variants & States

**Variants**（カラー）

| 種類 | フィル色 | 用途 |
|------|----------|------|
| `primary` | `bg-primary-500` | デフォルト。一般的な進捗 |
| `success` | `bg-emerald-600` | 完了・成功系（`color.status.success.fill`） |
| `warning` | `bg-amber-600` | 注意が必要（`color.status.warning.base`） |
| `error` | `bg-red-500` | エラー・危険レベル（`color.status.danger.base`） |

**表示形態 / States**

| 形態 | 説明 |
|------|------|
| 確定（default） | `style="width: N%"` で進捗値を指定 |
| 不確定（indeterminate） | CSS アニメーションでループ表示。`aria-valuenow` を省略 |

パーツ: Track（背景トラック・全体の範囲）/ Fill（進捗フィル）/ Label（上部テキスト・任意）/ Percentage（進捗率数値・任意）。

---

## 3. サイズ / Props

| プロパティ | 値 |
|-----------|-----|
| 高さ | `h-2`（8px） |
| 角丸 | `rounded-full` |
| アニメーション | `transition-all`（値変更時のスムーズ遷移） |
| Label | `text-sm font-medium text-slate-700` |

主な Props（参考）: `value`（0〜100、省略で indeterminate）/ `variant`（primary/success/warning/error）/ `showLabel`（boolean）/ `label`（説明テキスト）。

---

## 4. Composition Rules

許可: Label / Percentage の併置（props のみで制御）。
禁止: 計測不能な処理に確定 `value` を設定する（→ indeterminate を使う）/ プログレスバーのみで残り時間を伝える（テキスト併用）。
配置: ラベルは `flex justify-between items-center mb-1` で左にラベル・右に進捗率。

---

## 5. Layout & Spacing

- Track: `bg-slate-200 rounded-full h-2`、Fill: `rounded-full h-2 transition-all`
- ラベル-バー gap: `mb-1`（`spacing.1` 相当）
- 不確定時は Track に `overflow-hidden` を付与

### 不確定アニメーション CSS

```css
@keyframes indeterminate {
  0% { transform: translateX(-100%); }
  100% { transform: translateX(400%); }
}
.progress-indeterminate { animation: indeterminate 1.5s ease-in-out infinite; width: 25%; }
```

---

## 6. Token Mapping

| 用途 | トークン |
|------|---------|
| Track（背景） | `color.surface.sunken` 近傍（slate-200） |
| Fill（primary） | `color.primary.500` |
| Fill（success） | `color.status.success.fill`(#5EB62C) |
| Fill（warning） | `color.status.warning.base`(#EE8C29) |
| Fill（error） | `color.status.danger.base`(#E93766) |
| ラベルテキスト | `color.content.secondary` 近傍（slate-700） |
| 角丸 | `radius.full` |

---

## 7. Accessibility

| 属性 | 値 |
|------|-----|
| `role` | `"progressbar"` |
| `aria-valuenow` | 現在値（0〜100）。不確定時は省略 |
| `aria-valuemin` / `aria-valuemax` | `"0"` / `"100"` |
| `aria-label` | 進捗の説明テキスト |

- 色だけで状態を伝えない。バー色 vs トラック色 3:1 以上。`prefers-reduced-motion` で indeterminate アニメーション停止

## 8. Content Guidelines
- `label` はプロセスの名前（「ファイルをアップロード中」「ビルド中」）
- `showLabel` が true の場合は `value%` を表示

## 9. Usage Do / Don't

```html
<!-- Do: ラベル付き確定 -->
<div>
  <div class="flex justify-between items-center mb-1">
    <span class="text-sm font-medium text-slate-700">アップロード中</span>
    <span class="text-sm font-medium text-slate-700">65%</span>
  </div>
  <div class="bg-slate-200 rounded-full h-2" role="progressbar" aria-valuenow="65" aria-valuemin="0" aria-valuemax="100" aria-label="アップロード進捗">
    <div class="bg-primary-500 rounded-full h-2 transition-all" style="width: 65%"></div>
  </div>
</div>

<!-- Do: 不確定 -->
<div class="bg-slate-200 rounded-full h-2 overflow-hidden" role="progressbar" aria-label="読み込み中">
  <div class="bg-primary-500 rounded-full h-2 progress-indeterminate"></div>
</div>
```

**Don't**: `aria-valuenow` / `aria-valuemin` / `aria-valuemax` 省略 / `aria-label` 省略 / `prefers-reduced-motion` 未対応（indeterminate）/ プログレスバーのみで残り時間を伝える / 計測不能な処理に作り物の `value` を設定（→ indeterminate）。

---

## 10. Implementation Notes
- indeterminate は CSS `@keyframes` で実装（JS より軽量）
- ローディング状態は `Loader.md`、形状が分かるなら `Skeleton.md` を参照
