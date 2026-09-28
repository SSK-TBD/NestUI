# Component Guideline: List Group

> NestUI List 仕様。Tailwind CSS。値は `tokens/`（色は `color.surface.*` 等）を正とし、以下の class はその適用例。

## 1. Component Identity
- **Name**: List Group
- **Variants**: `Link List`（遷移リスト）/ `Information List`（情報リスト）/ `Action List`（アクションリスト）/ `Overview List`（プレビュー）/ `Toggle List`（設定）/ `Selection List`（選択）/ `Accordion List`（折りたたみ）
- **Responsibility**:
  - する: 同種アイテムを縦並びで表示する。遷移・情報表示・アクション起動・選択に使う。高さはコンテンツに追従する可変高
  - しない: 複数列のデータ表示（Table を使う）、フォーム内のオプション選択（Dropdown / Radio を使う）
- **Target Platform**: デスクトップ Web アプリ（業務 SaaS）

**原則**: 左から右への情報フロー（主要コンテンツ左・補助要素右）/ 左アイコンは「対象の種類」、右アイコンは「対象へのアクション」/ ボーダーは情報の区切りを示す（装飾でなく断絶度の可視化）/ 見出しと本文のサイズ比は120〜150%。

---

## 2. Variants & States

**Variants**（用途別パターン）

| Variant | 用途 |
|---------|------|
| Link List | アイテム内容が遷移先タイトルと一致。メニューに使用（右に Chevron） |
| Information List | 遷移/アクションなしの自己完結型（ステータス・優先度・作成日 等） |
| Action List | ラベルがアクション名。左アイコン=種類、右アイコン=アクション |
| Overview List | 遷移先コンテンツのプレビュー（タイトル + 1行抜粋） |
| Toggle List | 即時反映の ON/OFF 設定 |
| Selection List | 選択/非選択を保持（色だけでなくボーダー太さ・背景で表現） |
| Accordion List | 展開/折りたたみ（スペース節約） |

**States**

| 状態 | 視覚変化 | class |
|------|---------|------|
| Default | 標準表示 | — |
| Hover | 背景が薄く変化 | `hover:bg-gray-50` |
| Active | さらに濃い背景 | クリック中 |
| Focus | フォーカスリング | キーボード操作時 |
| Selected | ボーダー強調 + 背景変化 | 色だけでなくボーダー太さも変更 |
| Visited | テキスト色変化 | テキストリンクのみ |

---

## 3. Parts / 構成

| パーツ | 役割 | 必須 |
|--------|------|:----:|
| Container | リストの外枠（`bg-white rounded-xl border border-slate-200 divide-y divide-slate-100 overflow-hidden`） | Yes |
| Primary Text | メイン情報（遷移先タイトル / アクション名） | Yes |
| Secondary Text | 補足情報（日付・説明・メタデータ） | No |
| Left Icon | 対象の種類を示すアイコン | No |
| Left Image | サムネイル（画像が主体の場合） | No |
| Right Icon | アクション/遷移を示すアイコン（矢印・展開） | No |
| Action | トグル・ボタン・チェックボックス | No |
| Border | アイテム間の区切り（`divide-y divide-slate-100`） | 条件付き |

---

## 4. Composition Rules

- 許可: 左アイコン（`<svg>`）、右アイコン、Badge / Status tag、テキスト（ラベル・説明）、Toggle
- 禁止: 複数列データを List で表現（→Table）、List を Table の代替に使う、クリッカブル項目で `hover` 状態を省略、区切り線の省略
- アイテム数 0 のときは List を描画せず Empty prompt を表示
- 破壊的アクション項目は `text-[#e93766] hover:bg-[#fef2f4]`（`text-red-500` 禁止）

**Anti-patterns**
- NG: 左端に色付きの縦アクセントバー（border-left 等）で selected/active を示す → 選択は背景変化 + ボーダー太さ（全周）で示す（左端のみのカラーバーは使わない）

---

## 5. Layout & Spacing

- アイテムは `px-4 py-3`（左右16px・上下12px）、`flex items-center justify-between`
- 高さは固定せずコンテンツに追従（可変高）
- 区切りは `divide-y divide-slate-100`、外枠角丸 `rounded-xl`

---

## 6. Token Mapping

| 用途 | class | トークン |
|------|-------|---------|
| 外枠背景 | `bg-white` | `color.surface.raised` |
| 外枠ボーダー | `border-slate-200` | `color.border.subtle` |
| アイテム区切り | `divide-slate-100` | `color.border.subtle` |
| hover 背景 | `hover:bg-gray-50` | `color.surface.sunken` |
| ラベル | `text-slate-900` | `color.content.primary` |
| 説明テキスト | `text-slate-500` | `color.content.secondary` |
| 右アイコン | `text-slate-500` | `color.content.secondary` |
| 破壊的アクション | `text-[#e93766]` / `hover:bg-[#fef2f4]` | `color.status.danger.base` / `.container` |

---

## 7. Accessibility
- スワイプ/ドラッグ操作にはキーボード・ボタンの代替手段を必ず提供する
- クリッカブル項目には適切な要素/ロールを付与（`<a>` / `<button>`、メニューは `role="menuitem"`）
- 装飾以外のアイコン（選択状態・展開状態のインジケーター）には代替テキストを付与
- 追加・削除・状態変更は `aria-live` でスクリーンリーダーに通知
- 選択/非選択を色だけで伝えない（ボーダー太さ・背景を併用）
- テキスト 4.5:1・アイコン 3:1 以上のコントラスト。200%拡大でクリッピングしない

## 8. Content Guidelines
- ラベルは簡潔に（1行以内）、押した結果・遷移先を予測可能な文言にする
- 説明テキストは補足のみ。メインラベルで意味が完結するようにする

## 9. Usage Do / Don't

```html
<!-- Do: Link List（ナビゲーション・右 Chevron） -->
<nav>
  <ul class="bg-white rounded-xl border border-slate-200 divide-y divide-slate-100 overflow-hidden">
    <li>
      <a href="#" class="flex items-center justify-between px-4 py-3 hover:bg-gray-50 transition-colors">
        <span class="text-sm font-medium text-slate-900">カンバンボード</span>
        <svg class="w-4 h-4 text-slate-500" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 5l7 7-7 7"/></svg>
      </a>
    </li>
  </ul>
</nav>

<!-- Do: Action List の破壊的アクション -->
<button class="w-full flex items-center gap-3 px-4 py-3 text-left text-[#e93766] hover:bg-[#fef2f4] transition-colors">
  <svg class="w-4 h-4">…</svg><span class="text-sm font-medium">削除する</span>
</button>
```

**Don't**: インタラクティブ項目の `hover` 状態省略 / 区切り線（`divide-y` / `border-b`）の省略 / 複数列データを List で表現（→Table）/ アイテム数 0 で描画（→Empty prompt）/ `text-red-500`（→`text-[#e93766] hover:bg-[#fef2f4]`）。

---

## 10. Implementation Notes
- 大量アイテム（100件以上）は仮想スクロールを検討。長いリストは Pagination または無限スクロールと併用
- Accordion List は展開/折りたたみアニメーション付き（初期は `hidden`、展開で外す）。アイコンは Lucide
