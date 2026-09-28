# Component Guideline: Table

> NestUI Table 仕様。Tailwind CSS。値は `tokens/`（色は `color.surface.*` 等）を正とし、以下の class はその適用例。

## 1. Component Identity
- **Name**: Table
- **Variants**: `compact`（標準。サブラベル + 値の2行セル）/ ヘッダーカラー（Gray / Orange / Green / Blue）/ `editable`（編集可能セル）/ `expandable`（親子展開行）
- **Responsibility**:
  - する: 構造化データを密度高く表示する。合計行・編集セル・親子展開・ページネーションをサポートする
  - しない: レイアウト目的の使用（`<div>` + Flexbox/Grid を使う）/ 単純なリスト（List を使う）/ ツリー構造（Tree view を使う）
- **Target Platform**: デスクトップ Web アプリ（業務 SaaS）

**原則**: 構造化データの表示に限定 / Compact セル構造を標準とする（セル内に「サブラベル 11px + 値 12px」の2行）/ 列区切りは絶対配置の縦線（`absolute w-px bg-[rgba(8,26,39,0.1)]`。`border-r` は使わない）/ ヘッダーには4種のカラーバリアントで列の意味を示す / 合計行は `bg-[#f7f9fb]` でデータ行（白）と区別 / 一覧テーブルには Pagination を必ず配置（件数テキストのみは不可。ページ番号 + prev/next を含める）。

---

## 2. Variants & States

**Variants**

| Variant | 用途 |
|---------|------|
| `compact`（標準） | 標準。サブラベル（列名の繰り返し）+ 値の密度の高い表示。固定請求・費用明細など |
| ヘッダーカラー | 列の意味で区別。Gray（既定）/ Orange（費用系）/ Green（収益系）/ Blue（情報系） |
| Total Row（合計行） | 末尾の集計行。`bg-[#f7f9fb]`。値のあるセルのみ表示し他は空スペーサー |
| `editable`（編集可能セル） | セル内に SelectBox / InputBox / IconButton 等を配置するフォームテーブル |
| `expandable`（展開行） | 親行クリックで子行を表示するアコーディオン型。1:N の親子関係。親行・子行は異なるカラム構成 |

**States（行）**: default（通常）/ hover（密度重視で行ホバーは標準では省略。編集可能セルがある場合のみ個別セルで hover を定義。展開行・クリック可能行は `hover:bg-[#f7f9fb]` / `hover:bg-[#f0f7fe]`）/ empty（空状態メッセージ）。

---

## 3. パーツ

| パーツ | 役割 | 必須 | class（適用例） |
|--------|------|:----:|------|
| Container | 外枠 | Yes | `border border-[#c4cdd9] rounded-[8px] overflow-clip`（`border-collapse` で内側セル管理） |
| Header Row | ヘッダー行 | Yes | `bg-[#f7f9fb] border-b border-[#dde3eb]`（thead tr） |
| TableHeader | ヘッダーセル | Yes | `<th scope="col">` `h-[30px] px-[6px] py-[4px] text-left text-[11px] font-medium text-[#5a6c7f] tracking-[0.22px] leading-[1.3] whitespace-nowrap border-b border-[#dde3eb] border-r border-r-[#dde3eb]`（最終列は border-r なし） |
| Data Row | データ行 | Yes | `bg-white border-b border-[#dde3eb]`（合計行: `bg-[#f7f9fb]`） |
| TableData_Compact | データセル | Yes | `relative h-[54px] px-[8px] py-[4px]` + `flex flex-col gap-[4px] h-full justify-center` + サブラベル（11px `#5a6c7f`）+ 値（12px `#081a27`。Math は `text-right`） |
| Column Divider | セル右端の縦線 | Yes | `absolute right-0 top-0 bottom-0 w-px bg-[rgba(8,26,39,0.1)]` + `aria-hidden="true"`（最終列を除く） |
| Total Row | 合計行 | No | `bg-[#f7f9fb]` + 値のあるセルのみ表示・他は空スペーサー（`aria-hidden="true"`） |

**セルコンテンツタイプ**: Text（左寄せ）/ Math（数値・金額・右寄せ）/ SelectBox / InputBox（単位付き可）/ IconButton（削除等）/ Button（行追加）/ Handle（ドラッグ）/ Null（空スペーサー）。

---

## 4. 展開行（Expandable Row）

親行をクリックすると子行が表示されるアコーディオン型テーブル。**1:N の親子関係**を持つデータ全般に使用。親行・子行は**異なるカラム構成**を持つ。

| パーツ | 役割 | 必須 |
|--------|------|:----:|
| 親行（Parent Row） | 親エンティティのデータ行。左端に展開トグル + 子件数 | Yes |
| 展開トグル | filled triangle SVG（▼/▲）。`base/layout/Accordion.md` と同じ 10×6px SVG | Yes |
| 子件数ラベル | `"N件"` | Yes |
| 子ヘッダー行 | 子行用カラムヘッダー（親と異なる構成）。展開時に親行直下にインライン表示 | Yes |
| 子データ行 | 個々の子レコード | Yes |
| 追加ボタン行 | `"＋ 子項目を追加"` | No |

**バリアント**: A（案件 → 工事修繕）/ B（建物 → 物件。子行クリックでドロワーを開く `hover:bg-[#f0f7fe] cursor-pointer`、契約者・オーナーは TextLink + ExternalLink、管理区分は Tag）。

**制約**:
- 展開は `<tbody>` 単位で管理（親行1 + 子ヘッダー + 子行N + 追加行を1つの `<tbody>` にまとめる）
- ネストは**最大1階層**（親→子のみ。子→孫は不可）
- 展開トグルアイコンは Accordion と同じ filled triangle 10×6px。閉→▼（0deg）、開→▲（`rotate(180deg)`）+ `transition: transform 0.2s`
- 子行コンテナ `<tr>` は `colspan` を親テーブルの全列数に揃える。子テーブルは `margin-left:88px;width:calc(100% - 88px)` で展開トグル列幅分インデント
- 複数の親行を同時展開可能（単一展開モードは不要）。子行0件時は子ヘッダー + 追加ボタン行のみ表示

---

## 5. Layout & Spacing

| プロパティ | トークン | 値 |
|-----------|---------|-----|
| Container radius | `radius.*`（8px） | `rounded-[8px]` |
| ヘッダー height / padding | — | 30px / `px-[6px] py-[4px]` |
| データセル height / padding | — | 54px / `px-[8px] py-[4px]` |
| セル内 gap | `spacing.1` | `gap-[4px]` |
| 列区切り幅 | — | 1px |
| 子テーブルインデント | — | `margin-left:88px`（展開トグル列幅分） |

---

## 6. Token Mapping

| 用途 | トークン |
|------|---------|
| Container border | `#c4cdd9`（`color.border.default`） |
| ヘッダー背景 / 合計行背景 | `#f7f9fb`（`color.surface.base`） |
| データ行背景 | `#ffffff`（`color.surface.raised`） |
| ヘッダー / 子行 border | `#dde3eb`（`color.border.subtle`） |
| ヘッダー / サブラベルテキスト | `#5a6c7f`（`color.content.secondary`） |
| 値テキスト | `#081a27`（`color.content.primary`） |
| 列区切り線 | `rgba(8,26,39,0.1)`（`color.border` 半透明派生） |
| ヘッダー Orange 背景 / テキスト | `#fdf1d7` / `#b95415`（`color.status.warning` 派生） |
| ヘッダー Green 背景 / テキスト | `#d5f6e6` / `#376f1c`（`color.status.success` 派生） |
| ヘッダー Blue 背景 / テキスト | `#d8f0f5` / `#2550a8`（`color.primary.800` 派生） |
| 空状態テキスト | `#a1afc0`（`color.content.disabled`） |
| Container radius | `radius.*`（8px） |

> カラー列の区切り border は `border-[rgba(8,26,39,0.1)]` を使う。

---

## 7. Accessibility

- `<table>`, `<thead>`, `<tbody>`, `<th>`, `<td>` の正しいセマンティクスを使用する
- `<th>` には `scope="col"` を付与（合計行の `<th>` は `scope="row"`）。省略禁止
- 列区切りの縦線要素・空スペーサーセル（Null）には `aria-hidden="true"` を付与
- 展開行: 展開トグルの `<button>` に `aria-expanded="true|false"`。子行を含む `<tbody>` / `<tr>` に `id` を付与しトグルの `aria-controls` で参照
- インタラクティブテーブルは `role="grid"`、読み取り専用は `role="table"` + `aria-label`。ソート可能列は `aria-sort`、選択行は `aria-selected`

---

## 8. Content Guidelines
- ヘッダー title はデータの内容を示す名詞（「ステータス」「支払金額」）
- 数値・金額は右寄せ（Math）。アクションセルは右端の列に配置
- 空状態はメッセージを表示（`py-10 text-center text-[14px] text-[#a1afc0]`。空白のまま放置しない）

---

## 9. Usage Do / Don't

### 基本 Compact テーブル

```html
<div class="border border-[#c4cdd9] rounded-[8px] overflow-clip">
  <table class="w-full border-collapse">
    <thead>
      <tr class="bg-[#f7f9fb] border-b border-[#dde3eb]">
        <th scope="col" class="h-[30px] px-[6px] py-[4px] text-left text-[11px] font-medium text-[#5a6c7f] tracking-[0.22px] leading-[1.3] whitespace-nowrap border-b border-[#dde3eb] border-r border-r-[#dde3eb]">賃料項目</th>
        <th scope="col" class="h-[30px] px-[6px] py-[4px] text-left text-[11px] font-medium text-[#5a6c7f] tracking-[0.22px] leading-[1.3] whitespace-nowrap border-b border-[#dde3eb]">支払金額</th>
      </tr>
    </thead>
    <tbody>
      <tr class="bg-white border-b border-[#dde3eb]">
        <!-- Text セル（列区切り線あり） -->
        <td class="relative h-[54px] px-[8px] py-[4px] align-top">
          <div class="flex flex-col gap-[4px] h-full justify-center">
            <span class="text-[11px] font-medium text-[#5a6c7f] tracking-[0.22px] leading-[1.3] whitespace-nowrap">賃料項目</span>
            <span class="text-[12px] font-medium text-[#081a27] tracking-[0.24px] leading-[1.3]">管理費</span>
          </div>
          <div class="absolute right-0 top-0 bottom-0 w-px bg-[rgba(8,26,39,0.1)]" aria-hidden="true"></div>
        </td>
        <!-- Math セル（右寄せ、最終列は縦線なし） -->
        <td class="relative h-[54px] px-[8px] py-[4px] align-top">
          <div class="flex flex-col gap-[4px] h-full justify-center">
            <span class="text-[11px] font-medium text-[#5a6c7f] tracking-[0.22px] leading-[1.3] whitespace-nowrap">支払金額</span>
            <span class="text-[12px] font-medium text-[#081a27] tracking-[0.24px] leading-[1.3] text-right w-full">15,000</span>
          </div>
        </td>
      </tr>
    </tbody>
  </table>
</div>
```

### ヘッダーカラーバリアント

```html
<th scope="col" class="h-[30px] px-[6px] py-[4px] text-left text-[11px] font-medium bg-[#fdf1d7] text-[#b95415] tracking-[0.22px] leading-[1.3] whitespace-nowrap border-b border-[rgba(8,26,39,0.1)] border-r border-r-[rgba(8,26,39,0.1)]">費用ラベル（Orange）</th>
<th scope="col" class="h-[30px] px-[6px] py-[4px] text-left text-[11px] font-medium bg-[#d5f6e6] text-[#376f1c] ...">収益ラベル（Green）</th>
<th scope="col" class="h-[30px] px-[6px] py-[4px] text-left text-[11px] font-medium bg-[#d8f0f5] text-[#2550a8] ...">情報ラベル（Blue）</th>
```

### 展開行（バリアント A: 案件 → 工事修繕）

```html
<div class="overflow-x-auto">
  <table class="w-full border-collapse text-left">
    <thead class="sticky top-0 bg-white z-10">
      <tr class="bg-[#f7f9fb] border-b border-[#dde3eb]">
        <th scope="col" class="h-[30px] px-3 py-1 text-[11px] font-medium text-[#5a6c7f] tracking-[0.22px] whitespace-nowrap w-[88px]">工事</th>
        <th scope="col" class="h-[30px] px-3 py-1 text-[11px] font-medium text-[#5a6c7f] tracking-[0.22px] whitespace-nowrap border-r border-[#dde3eb]">案件タイトル</th>
        <!-- ...他の親列... -->
      </tr>
    </thead>
    <!-- 展開可能グループ（<tbody> 単位） -->
    <tbody>
      <!-- 親行 -->
      <tr class="bg-white border-b border-[#dde3eb] hover:bg-[#f7f9fb] cursor-pointer" onclick="toggleExpandableRow(this)">
        <td class="py-2.5 px-3 whitespace-nowrap">
          <div class="flex items-center gap-1.5">
            <button type="button" aria-expanded="true" aria-controls="children-1" class="w-5 h-5 inline-flex items-center justify-center rounded-sm flex-shrink-0">
              <svg style="width:10px;height:6px;transition:transform 0.2s;transform:rotate(180deg)" viewBox="0 0 10 6" fill="currentColor" aria-hidden="true"><path d="M0 0L10 0L5 6Z"/></svg>
            </button>
            <span class="text-[12px] font-medium text-[#5a6c7f] tracking-[0.24px]">03件</span>
          </div>
        </td>
        <td class="py-2.5 px-3 border-r border-[#dde3eb]"><!-- 案件タイトル等 --></td>
      </tr>
      <!-- 子行グループ（colspan = 親全列数、子テーブルは ml-[88px]） -->
      <tr id="children-1" class="bg-white border-b border-[#dde3eb]">
        <td colspan="7" class="p-0">
          <table class="w-full border-collapse text-left" style="margin-left:88px;width:calc(100% - 88px)">
            <thead><tr class="bg-[#f7f9fb] border-b border-[#dde3eb]"><!-- 子ヘッダー（親と異なる構成） --></tr></thead>
            <tbody>
              <tr class="bg-white border-b border-[#dde3eb]"><!-- 子データ行 --></tr>
              <tr class="bg-white"><td colspan="9" class="py-2 px-3">
                <button type="button" class="inline-flex items-center gap-1 text-[12px] font-medium text-[#3e5062] tracking-[0.24px] hover:text-primary-700 cursor-pointer">＋ 工事修繕を追加</button>
              </td></tr>
            </tbody>
          </table>
        </td>
      </tr>
    </tbody>
    <!-- 閉じたグループは子行 <tr> に hidden を付与 -->
  </table>
</div>
<script>
function toggleExpandableRow(parentRow) {
  const toggle = parentRow.querySelector('[aria-expanded]');
  const expanded = toggle.getAttribute('aria-expanded') === 'true';
  const childRow = document.getElementById(toggle.getAttribute('aria-controls'));
  const svg = toggle.querySelector('svg');
  toggle.setAttribute('aria-expanded', String(!expanded));
  svg.style.transform = expanded ? '' : 'rotate(180deg)';
  childRow.classList.toggle('hidden');
}
</script>
```

> バリアント B（建物 → 物件）では展開トグルが親行クリックと競合しないよう `event.stopPropagation()` を使う（展開トグルのクリックハンドラ側で止める）。子行クリックでドロワーを開く。

**Don't**: `<th>` の `scope="col"` 省略 / 空状態のメッセージを表示しない / `rounded-xl`（→ `rounded-[8px]`）/ `border-slate-200`（→ `border-[#c4cdd9]`）/ `bg-gray-50` のヘッダー背景（→ `bg-[#f7f9fb]`）/ レイアウト目的の使用 / 一覧テーブルで Pagination を省略 / 列区切りを `border-r` で実装（→ 絶対配置の縦線）。

---

## 10. Implementation Notes
- 大量件数（100件以上）は仮想スクロールを検討（`@tanstack/react-virtual` 等）
- `stickyHeader` は `position: sticky; top: 0; z-index: 1`
- 展開トグルの triangle SVG は Accordion と共有（`base/layout/Accordion.md`）。一覧テーブル下部の Pagination は Pagination 仕様を参照
- 思想は `FOUNDATIONS.md`、使い分けは `COMPONENT_GUIDE.md`
