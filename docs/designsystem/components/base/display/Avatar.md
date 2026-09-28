# Component Guideline: Avatar

> NestUI Avatar 仕様。Tailwind CSS。値は `tokens/`（イニシャル背景は `color.primary.50`）を正とし、以下の class はその適用例。

## 1. Component Identity
- **Name**: Avatar
- **Variants**: 画像（image） / イニシャル（initials） / グループ（group）（+ ステータスドット）
- **Responsibility**:
  - する: ユーザーのプロフィール画像またはイニシャルを表示し、本人を識別させる
  - しない: クリック操作のトリガー単体（→ ボタン/メニューと組み合わせる）/ 装飾アイコン（→ Icon）
- **Target Platform**: デスクトップ Web アプリ（業務 SaaS）

**原則**: ユーザー識別 / フォールバック（画像がなければイニシャル）/ ステータス表示（オンライン/離席/オフラインをドットで）/ グループ対応（複数アバターを左→右に重ねる）。

---

## 2. Variants & States

**Variants**（コンテンツ）

| 種類 | 説明 | class |
|------|------|-------|
| 画像 | `<img>` でプロフィール画像 | `rounded-full object-cover` + `alt` |
| イニシャル | 画像がない場合の代替 | `rounded-full bg-primary-50 flex items-center justify-center font-medium text-primary-500` + `role="img"` `aria-label` |
| グループ | 複数アバターを重ねる | `flex -space-x-2` + 各 `border-2 border-white`。残数は `bg-slate-100 text-body`「+N」 |

**ステータスドット**（任意）

| 状態 | 色 | aria-label |
|------|-----|-----------|
| Online | `bg-emerald-500` | `"オンライン"` |
| Away | `bg-amber-400` | `"離席中"` |
| Offline | `bg-slate-300` | `"オフライン"` |

共通: `absolute bottom-0 right-0 w-3 h-3 rounded-full border-2 border-white`。

静的表示のためインタラクション状態（hover/active）は持たない。

---

## 3. サイズ / Props

| サイズ | class | px | イニシャル文字 |
|--------|-------|-----|---------------|
| Small | `w-8 h-8` | 32px | `text-xs` |
| Medium（既定） | `w-10 h-10` | 40px | `text-sm` |
| Large | `w-12 h-12` | 48px | `text-base` |

主な Props（参考）: `src`/`alt`（画像）/ `name`（イニシャル生成）/ `size`（sm/md/lg）/ `status`（online/away/offline）。

---

## 4. Composition Rules

許可: `<img>` または イニシャルテキスト / ステータスドット（`<span>`）。
禁止: 画像なし・イニシャルなしの空アバター / `w-10 h-10` 以外をデフォルトにする（S/M/L の3段階固定）/ グループの重なり方向を右→左にする（左→右・`-space-x-2`）。
配置: ステータスドット付きは `relative inline-block` で包む。

---

## 5. Layout & Spacing

- 角丸は `rounded-full` 固定
- グループ重なりは `-space-x-2`（左→右）、各アバターに `border-2 border-white` で区切る
- ステータスドット: 右下 `bottom-0 right-0`、`w-3 h-3`、白ボーダー `border-2 border-white`

---

## 6. Token Mapping

| 用途 | トークン |
|------|---------|
| イニシャル背景 | `color.primary.50` |
| イニシャル文字 | `color.primary.500` |
| グループ残数背景 | `color.surface.sunken` 近傍（slate-100） |
| ステータス online | `color.status.success.fill` 近傍（emerald-500） |
| ステータス away | `color.status.warning.base` 近傍（amber-400） |
| ステータス offline | `color.content.disabled` 近傍（slate-300） |
| ドット白ボーダー | `color.surface.raised`（white） |
| 角丸 | `radius.full` |

---

## 7. Accessibility

| 属性 | 値 |
|------|-----|
| `alt` | 画像に必ず代替テキスト（装飾的な場合は `alt=""`） |
| `role="img"` + `aria-label` | イニシャルアバターにユーザー名 |
| `aria-label` | ステータスドットに状態（「オンライン」等） |
| グループ | 各アバターに個別の `alt` |

- 色だけでステータスを伝えない（`aria-label` 併用）

## 8. Content Guidelines
- イニシャルは姓名の頭文字（2文字目安）
- ステータスは状態を明示するラベルを付与

## 9. Usage Do / Don't

```html
<!-- Do: 画像アバター（M） -->
<img src="..." alt="サンプル 太郎" class="w-10 h-10 rounded-full object-cover">

<!-- Do: イニシャルアバター -->
<div role="img" aria-label="サンプル 太郎" class="w-10 h-10 rounded-full bg-primary-50 flex items-center justify-center text-sm font-medium text-primary-500">TK</div>

<!-- Do: ステータスドット付き -->
<div class="relative inline-block">
  <img src="..." alt="サンプル 太郎" class="w-10 h-10 rounded-full object-cover">
  <span class="absolute bottom-0 right-0 w-3 h-3 rounded-full border-2 border-white bg-emerald-500" aria-label="オンライン"></span>
</div>

<!-- Do: アバターグループ（左→右に重ねる） -->
<div class="flex -space-x-2">
  <img src="..." alt="ユーザー 1" class="w-10 h-10 rounded-full border-2 border-white object-cover">
  <img src="..." alt="ユーザー 2" class="w-10 h-10 rounded-full border-2 border-white object-cover">
  <div class="w-10 h-10 rounded-full border-2 border-white bg-slate-100 flex items-center justify-center text-xs font-medium text-body">+3</div>
</div>
```

**Don't**: `aria-label` 省略（`role="img"` と併用必須）/ 画像なし・イニシャルなしの空アバター / `w-10 h-10` 以外をデフォルト / グループ重なりを右→左にする。

---

## 10. Implementation Notes
- 画像読み込み失敗時はイニシャルにフォールバック（`onerror`）
- サイドバーのアカウントカードでは `w-[32px] h-[32px] rounded-full bg-[#ee8c29] text-white`（オレンジ）パターンも使われる
- コメント・アクティビティ内の小アバターは `w-6 h-6`（`Activity.md` 参照）
