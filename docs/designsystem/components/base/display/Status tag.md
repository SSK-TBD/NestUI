# Component Guideline: Status tag

> NestUI Status tag / MenuTag 仕様。Tailwind CSS。値は `tokens/`（色は `color.status.*` / `color.semantic.*`）を正とし、以下の class はその適用例。色・アイコン・命名の判断ルールは **11 章を正**とする。

## 1. Component Identity
- **Name**: Status tag
- **Variants**: `Circle`（ドット付き／進行中の状態） / `Check`（チェック付き／完了） / `Edit`（編集アイコン付き／変更） / `Attribute`（アイコンなし／属性） / `MenuTag`（選択トリガー）
- **Responsibility**:
  - する: オブジェクトの状態（ステータス・進捗）と属性を色付きで表示する。MenuTag はステータス変更ドロップダウンを開く
  - しない: メタデータの表示（→ Tag）/ 数値カウント・汎用ラベル（→ Badge）/ フィルター・選択肢（→ Chip）
- **Target Platform**: デスクトップ Web アプリ（業務 SaaS）

**原則**: 色のみで伝達しない（テキストを必ず併記）/ アイコンの有無で状態と属性を分ける（状態＝あり、属性＝なし。11.2）/ 1画面のカラーグループは7色以内 / MenuTag はドロップダウンとセットで使用（単独使用しない）。

---

## 2. Variants & States

| 種別 | 概要 | 用途 |
|------|------|------|
| `Circle` | 色付きドット + テキスト | 進行中の状態（未対応・申込中・契約中） |
| `Check` | チェックアイコン + テキスト | 完了。**ラベルが「完了」のときだけ**使う（11.5） |
| `Edit` | Pencil アイコン + テキスト | 変更。色は Blue 固定（`#d8f0f5` / `#2550a8`） |
| `Attribute` | テキストのみ（アイコンなし） | 属性（住居・オーナー・必須）。色は識別用で意味を持たない（11.4） |
| `MenuTag` | 色付き背景 + テキスト + ChevronDown | ステータス変更ドロップダウンのトリガー |

**カラートークン**

| カラーグループ | 意味カテゴリ（状態） | 背景（Container） | テキスト | ドット色（Circle） | 用途例 |
|----------------|----------------------|-------------------|----------|----------------------|--------|
| Rose | 対応が必要 | `#fee5e9` | `#b51b4e` | `#e93766` | 未対応 / エラー / 空室 / 契約終了 |
| Orange | まだ後戻りできる | `#fdf1d7` | `#b95415` | `#ee8c29` | 未入金 / 申込中 / 更新手続中 |
| Turquoise | 後戻りできないものができた。まだ完了していない | `#d0f7f4` | `#176a6e` | `#21a9ab` | 一部入金 / 契約開始前 / 更新予約 |
| Green | 完了した | `#d5f6e6` | `#376f1c` | `#5eb62c` | 契約中 / 在室 / 入金済 / 完了 |
| Blue | 下書き | `#d8f0f5` | `#2550a8` | `#2661cf` | 下書き |
| Purple | 解約系の進行中 | `#f4ecfb` | `#6d3792` | `#a35ed9` | 解約手続中 / 解約予約 |
| Gray | 軸を降りた | `#dde3eb` | `#081a27` | `#5a6c7f` | 解約済 / キャンセル / アーカイブ / 非公開 |

> 「意味カテゴリ」列は**状態（アイコンあり）にのみ適用**。属性（アイコンなし）は同じパレットを使うが色に意味を持たせず、11.4 の順で機械的に振る。
> `Edit`（変更）は 11.3 の軸に載らず、**Blue 固定**で運用する（`bg #d8f0f5` / テキスト・アイコン `#2550a8`。11.6）。

**States**: Circle / Check / Edit / Attribute は静的（インタラクションなし）。MenuTag は hover（`hover:brightness-95`）/ open（`aria-expanded="true"`）。MenuTag のトリガー: クリック/Enter/Space で開閉、Escape・外部クリックで閉じる。

---

## 3. サイズ / Props

| 種別 | 外枠 | テキスト | アイコン |
|------|------|----------|---------|
| Circle | `inline-flex items-center rounded-[4px] gap-[6px] px-[6px]` | `text-[11px] font-normal leading-[1.75] whitespace-nowrap` | ドット `w-2 h-2 rounded-full shrink-0` |
| Check | `inline-flex items-center rounded-[4px] gap-[4px] pl-[4px] pr-[6px]` | `text-[11px] font-normal leading-[1.75] whitespace-nowrap` | チェック SVG `w-3 h-3 shrink-0` |
| Edit | `inline-flex items-center rounded-[4px] gap-[4px] pl-[4px] pr-[6px]` | `text-[11px] font-normal leading-[1.75] whitespace-nowrap` | Lucide `pencil` `w-3 h-3 shrink-0` |
| Attribute | `inline-flex items-center rounded-[4px] px-[6px]` | `text-[11px] font-normal leading-[1.75] whitespace-nowrap` | なし |
| MenuTag | `inline-flex items-center gap-[4px] h-[28px] pl-[12px] pr-[8px] rounded-[2px] cursor-pointer hover:brightness-95 transition-all` | `text-[12px] font-medium tracking-[0.24px] leading-[1.3] whitespace-nowrap` | ChevronDown `w-4 h-4 flex-shrink-0`（aria-hidden） |

主な Props（参考）: `type`（circle/check/edit/attribute/menu）/ `color`（rose/orange/turquoise/green/blue/purple/gray）/ `label`。

---

## 4. Composition Rules

許可: ドット / チェック SVG / Pencil SVG + テキスト（MenuTag は + ChevronDown）。属性はテキストのみ。
禁止: テキストを省略した「色のみ」表示 / 状態タグをアイコンなしで出す / 属性タグにアイコンを付ける / ラベルが「完了」以外で Check を使う / 属性に Rose を使う（必須を除く）/ MenuTag を静的ラベルとして使用（開かないなら Circle）/ 8色以上のカラーグループを1画面に混在 / Tailwind の `bg-rose-*` 等汎用クラスをそのまま使用（トークンの HEX を指定）。
配置: MenuTag はステータス変更ドロップダウン（`role="menu"`）と必ずセット。

---

## 5. Layout & Spacing

- Circle / Check / Edit / Attribute 角丸 `rounded-[4px]`、MenuTag 角丸 `rounded-[2px]`
- MenuTag 高さ `h-[28px]`、padding `pl-[12px] pr-[8px]`
- カラーグループは1画面7色以内

---

## 6. Token Mapping

| カラー | 背景トークン | テキストトークン |
|--------|------|---------|
| Rose | NestUI 固有(#fee5e9) | NestUI 固有(#b51b4e) |
| Orange | `color.status.warning.container` 近傍(#fdf1d7) | `color.status.warning.text`(#b95415) |
| Turquoise | NestUI 固有(#d0f7f4) | NestUI 固有(#176a6e) |
| Green | NestUI 固有(#d5f6e6) | `color.status.success.text`(#376f1c) |
| Blue | `color.brand.100` 近傍(#d8f0f5) | `color.primary.800`(#2550a8) |
| Purple | NestUI 固有(#f4ecfb) | NestUI 固有(#6d3792) |
| Gray | `color.border.subtle`(#dde3eb) | `color.content.primary`(#081a27) |
| 角丸 Circle/Check/Edit/Attribute | `radius.sm`(4px) |
| 角丸 MenuTag | `radius.sm`→2px（NestUI 固有） |

> Rose/Turquoise/Purple/Green の一部は標準 semantic に無い NestUI 固有色。HEX を直接指定する（Tailwind 汎用クラスは使わない）。

---

## 7. Accessibility

| 属性 | 値 | 対象 |
|------|-----|------|
| `aria-haspopup="true"` | — | MenuTag ボタン |
| `aria-expanded` | `"false"` / `"true"` | MenuTag ボタン（開閉状態） |
| `role="menu"` | — | ドロップダウンコンテナ |
| `role="menuitem"` | — | 各ステータス選択肢 |

- Circle / Check / Edit / Attribute は `role` 属性不要（静的テキスト）
- 色だけで伝えない（テキストを必ず併記）。アイコンは色の補強で、単独で意味を持たせない

## 8. Content Guidelines
- ステータス名は体言止め（「契約中」「解約手続中」「完了」）
- MenuTag のラベルは現在のステータスを表示
- バリアント名は `<対象>_<値>`、後半は画面に出るラベルと一致させる（11.6）

## 9. Usage Do / Don't

```html
<!-- Do: Circle（Green / 契約中） -->
<div class="inline-flex items-center rounded-[4px] bg-[#d5f6e6] gap-[6px] px-[6px]">
  <span class="w-2 h-2 rounded-full bg-[#5eb62c] shrink-0"></span>
  <span class="text-[11px] font-normal text-[#376f1c] leading-[1.75] whitespace-nowrap">契約中</span>
</div>

<!-- Do: Check（Green / 完了）── ラベルが「完了」のときだけ Check -->
<div class="inline-flex items-center rounded-[4px] bg-[#d5f6e6] gap-[4px] pl-[4px] pr-[6px]">
  <svg class="w-3 h-3 shrink-0 text-[#376f1c]" viewBox="0 0 12 12" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true"><polyline points="1.5,6 4.5,9 10.5,3"/></svg>
  <span class="text-[11px] font-normal text-[#376f1c] leading-[1.75] whitespace-nowrap">完了</span>
</div>

<!-- Do: Edit（Blue / 変更）── Lucide pencil -->
<div class="inline-flex items-center rounded-[4px] bg-[#d8f0f5] gap-[4px] pl-[4px] pr-[6px]">
  <svg class="w-3 h-3 shrink-0 text-[#2550a8]" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true"><path d="M21.174 6.812a1 1 0 0 0-3.986-3.987L3.842 16.174a2 2 0 0 0-.5.83l-1.321 4.352a.5.5 0 0 0 .623.622l4.353-1.32a2 2 0 0 0 .83-.497z"/><path d="m15 5 4 4"/></svg>
  <span class="text-[11px] font-normal text-[#2550a8] leading-[1.75] whitespace-nowrap">変更</span>
</div>

<!-- Do: Attribute（Orange / 住居）── 属性はアイコンなし -->
<div class="inline-flex items-center rounded-[4px] bg-[#fdf1d7] px-[6px]">
  <span class="text-[11px] font-normal text-[#b95415] leading-[1.75] whitespace-nowrap">住居</span>
</div>

<!-- Do: MenuTag（Rose / 未対応・ドロップダウンと連動） -->
<button type="button" aria-haspopup="true" aria-expanded="false"
  class="inline-flex items-center gap-[4px] h-[28px] pl-[12px] pr-[8px] rounded-[2px] bg-[#fee5e9] text-[#b51b4e] cursor-pointer hover:brightness-95 transition-all">
  <span class="text-[12px] font-medium tracking-[0.24px] leading-[1.3] whitespace-nowrap">未対応</span>
  <svg class="w-4 h-4 flex-shrink-0" viewBox="0 0 16 16" fill="none" aria-hidden="true"><path d="M4 6l4 4 4-4" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/></svg>
</button>
```

**Don't**: テキストを省略した「色のみ」/ 状態タグをアイコンなしで出す / 属性タグにアイコンを付ける / 「完了」以外のラベルで Check / 属性に Rose（必須を除く）/ MenuTag を静的ラベルに使用（→ Circle）/ 8色以上のカラーグループを1画面に混在 / `bg-rose-*` 等の汎用クラスをそのまま使用（→ トークンの HEX）。

---

## 10. Implementation Notes
- MenuTag のドロップダウン（メニューコンテナ・メニューアイテム）は共通メニューパターンに準拠
- メタデータ表示は `Tag.md`、汎用ステータスラベル/カウントは `Badge.md` を参照
- Tailwind 汎用カラーではなくトークンの HEX を直接指定すること
- 色名は `Green` を正とする（旧 `LightGreen` / `lightGreen` は使わない）
- `Edit` のアイコンは Lucide `pencil`（12×12・`pl-[4px]` `gap-[4px]` `pr-[6px]`・タグ高 19px）

---

## 11. 色 / アイコン / 命名ルール（全タグ共通）

> プロダクト全体のステータス表示（Status tag）で、①どの色を使うか ②アイコンを付けるか ③バリアントをどう命名するか、を判断するための汎用ルール。契約ステータスだけでなく、案件・請求・入出金など**あらゆるタグに適用**する。
> **例外は設けない。** 実装側の色定義（組織ごとのマスタ・独自の色 enum など）が本ルールと食い違う場合は、**本ルールを正として実装を寄せる**。

### 11.1 大原則（3つ）

1. **好みで選ばない。ただし決め方は状態と属性で違う** — 状態は意味で決まる（11.3）。属性は色に意味を持たせず、順番で機械的に振る（11.4）。ただし Excel＝緑のように世の中で定着した対応があるものは、それを優先する（11.4 の 6）。
2. **色だけで伝えない** — 必ずテキストを併記。アイコンは色の補強であって単独で意味を持たせない。
3. **7色で収める** — Rose / Orange / Turquoise / Green / Blue / Purple / Gray。

### 11.2 アイコンで4種類に分かれる

`Status / Tag` の **ShowIcon（true/false）** と **Type（Circle / Check / Edit）** の組み合わせで、そのタグが何を表しているかが決まる。

| ShowIcon | Type | 意味 | 例 |
| --- | --- | --- | --- |
| true | Circle | **進行中の状態** | 未対応 / 申込中 / 契約中 |
| true | Check | **完了した状態** | 完了 |
| true | Edit | **変更** | 変更 |
| false | — | **属性** | 住居 / オーナー / 必須 |

- 状態＝時間とともに進んでいくもの（未対応 → 対応中 → 完了）
- 属性＝進まないもの（住居 / オーナー / 必須）

**アイコンの有無が「状態か属性か」を表す。** 状態には必ずアイコンを付け、属性には付けない。

### 11.3 状態の色は意味を持つ

状態（ShowIcon=true）の色は意味で固定する。**Orange → Turquoise → Green は「どこまで実現したか」の1本の軸。**

| 色 | 意味 | 例 |
| --- | --- | --- |
| **Rose** | 対応が必要 | 未対応 / エラー / 空室 |
| **Orange** | まだ後戻りできる | 未入金 / 申込中 / 更新手続中 |
| **Turquoise** | 後戻りできないものができた。まだ完了していない | 一部入金 / 契約開始前 / 更新予約 |
| **Green** | 完了した | 契約中 / 入金済 / 完了 |
| **Blue** | 下書き | 下書き |
| **Purple** | 解約系の進行中 | 解約手続中 / 解約予約 |
| **Gray** | 軸を降りた | 解約済 / キャンセル / アーカイブ |

迷ったら「**もうやり直せないことが起きたか**」で判定する。起きていない → Orange ／ 起きたが終わっていない → Turquoise ／ 終わった → Green。

**同じグループ内に同じ色が並んでよい。** 状態は色が意味を持つので、意味が同じなら同色になるのが正しい（例：現況の「申込中」と「家主調整中」はどちらも Orange）。「グループ内で色を重複させない」は次項＝属性のルールであって、状態には適用しない。

> **例は代表例だけで、増やさない。** ラベルと色の全対応は、プロダクト側で対応表として管理する。新しいラベルを足すときは対応表に1行足す。

### 11.4 属性の色は識別のためだけ

属性（ShowIcon=false）の色には意味がない。並びを見分けるための採番として扱う。

| # | ルール |
| --- | --- |
| 1 | 色に意味を持たせない |
| 2 | 対応が必要なものは Rose（必須） |
| 3 | 「その他」「未指定」は Gray |
| 4 | 以降 Orange → Green → Turquoise → Blue → Purple の順 |
| 5 | 同じグループの中で同じ色を使わない |
| 6 | 一般に定着した色があるものは 4 より優先（XLSX=Green / PDF=Orange、個人=Blue / 法人=Purple） |

並び順：`Orange → Green → Turquoise → Blue → Purple` ＋ その他は `Gray`

### 11.5 Check にするかの判定

**ラベルが「完了」のときだけ Check。**

### 11.6 Edit の扱い

**`Type=Edit`（変更）は色の軸に載らず Blue 固定。** アイコンは Lucide `pencil`（12×12）。

### 11.7 新しいタグを足すときの命名

| # | ルール |
| --- | --- |
| 1 | `<対象>_<値>` で作る。前半は**そのタグが付く対象**の名前にする（例：`募集状況_公開`） |
| 2 | 「ステータス」「区分」など**型を表す語は接頭辞に入れない** |
| 3 | 後半は**画面に出るラベルと一致**させる |

### 11.8 Do / Don't

**Do**

- **状態**は同じ意味に同じ色を使う（Green＝完了した、Gray＝軸を降りた…）。
- 状態にはアイコンを付け、属性には付けない（＝アイコンで両者を区別する）。
- 状態の区別は**色＋テキスト**で行う。
- 属性の色は 11.4 の 3〜6 の順で機械的に振る。
- 新しいラベルを足したら**台帳に1行足す**（11.3 の注記）。

**Don't**

- 色だけで状態を伝える（テキスト必須）。
- 属性に Rose を使う（対応が必要なものを除く）。
- 11.3 の例を増やして台帳の代わりにする（例は代表例だけ）。

### 11.9 適用例：賃貸借契約ステータス（11件）

全て ShowIcon=true / Circle。

| ステータス | 色 | 意味 |
| --- | --- | --- |
| 下書き | Blue | 下書き |
| 申込中 | Orange | まだ後戻りできる |
| 契約開始前 | Turquoise | 後戻りできないものができた |
| 契約中 | Green | 完了した |
| 更新手続中 | Orange | まだ後戻りできる（更新が未締結） |
| 更新予約 | Turquoise | 後戻りできないものができた（更新は締結済み・発効前） |
| 解約手続中 | Purple | 解約系の進行中 |
| 解約予約 | Purple | 解約系の進行中 |
| 解約済 | Gray | 軸を降りた |
| 契約終了 | Rose | 対応が必要（部屋が空くので募集が必要） |
| キャンセル | Gray | 軸を降りた |

`更新手続中 → 更新予約 → 契約中` は、初回の `申込中 → 契約開始前 → 契約中` と同じ形になる。
