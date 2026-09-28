# パターン HTML テンプレート

各パターン HTML ファイルの構造ガイド。Design System（`docs/designsystem/`）に準拠した、単体で開ける自己完結 HTML として生成する。

スタイルは **`docs/designsystem/layout_patterns/pane-*.html`（レイアウトテンプレート）** をベースにする。これらは `docs/designsystem/tokens/` のトークンを `:root` の CSS 変数として持ち、App Shell（Side nav + Navbar + Content Area）のグリッド構造を備えている。`layout_patterns/layout-index.md` でパターンに最も近いテンプレートを 1 つ選び、その構造・トークン使いを踏襲して `zone--main`（必要なら `zone--sub` / `zone--helper`）に中身を実装するのが基本フロー。

---

## 作り方（推奨フロー）

1. パターンのレイアウトに対応する `pane-*.html` を 1 つ選ぶ（`pane-1` / `pane-2l` / `pane-2r` / `pane-3lr` 等）
2. その `<head>` の `:root`（デザイントークン）と App Shell 構造をそのまま引き継ぐ
3. 先頭にメタコメントを追加する
4. Side nav は当該画面のみ active にし、他項目はグレーアウトのダミーにする
5. Main / Helper ゾーンに、`component-index.md` のコンポーネント仕様に沿って中身を実装する
6. 色・余白・角丸・z-index・モーションは **すべて `var(--...)` トークン参照**（直書き禁止）

---

## ファイル構造（最小骨格）

```html
<!--
  PATTERN: [パターン名]
  TARGET:  [ターゲットユーザー状態]
  INTERACTION: [インタラクションモデル]
  DENSITY: [コンパクト/標準/ゆったり]
  FOCUS:   [フォーカス]
  LAYOUT:  [使用した App Shell テンプレート（例: pane-2r）]
  PRO:     [得意なこと。1文で]
  CON:     [不得意なこと。1文で]
-->
<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>[パターン名] — [spec_title]</title>
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link href="https://fonts.googleapis.com/css2?family=Noto+Sans+JP:wght@400;500;700&display=swap" rel="stylesheet">
  <style>
    /* === Design Tokens ===
       docs/designsystem/tokens/ の値を :root に展開する。
       選んだ pane-*.html の :root をそのままコピーするのが最も安全。
       例（pane-2l.html / pane-2r.html の :root より抜粋）: */
    :root {
      --color-brand-primary:     #2661CF;  /* color.primary.700 */
      --color-surface-base:      #F7F9FB;  /* surface.base（ページ背景） */
      --color-surface-raised:    #ffffff;  /* surface.raised（カード/パネル） */
      --color-content-primary:   #081A27;
      --color-content-secondary: #5A6C7F;
      --color-border-default:    #C4CDD9;
      --color-border-subtle:     #DDE3EB;

      --spacing-1:4px; --spacing-2:8px; --spacing-3:12px; --spacing-4:16px;
      --spacing-6:24px; --spacing-8:32px; --spacing-10:40px; --spacing-12:48px;

      --layout-sidenav-width:188px; --layout-navbar-height:68px; --layout-content-padding:32px;

      --font-family-base:"Noto Sans JP", system-ui, -apple-system, sans-serif;
      --font-size-xs:11px; --font-size-sm:12px; --font-size-md:14px;
      --font-size-lg:16px; --font-size-xl:21px;
      --font-weight-regular:400; --font-weight-medium:500; --font-weight-bold:700;
      --line-height-tight:1.4; --line-height-normal:1.5;

      --radius-sm:4px; --radius-md:8px; --radius-lg:12px; --radius-full:9999px;

      --z-dropdown:100; --z-drawer:200; --z-modal:300; --z-toast:400;
      --shadow-sm:0 1px 3px rgba(0,0,0,0.08);
      --shadow-md:0 4px 12px rgba(0,0,0,0.12);

      --duration-normal:200ms; --easing-default:cubic-bezier(0.2, 0, 0, 1);
    }
    *, *::before, *::after { box-sizing:border-box; margin:0; padding:0; }
    body {
      font-family:var(--font-family-base);
      font-size:var(--font-size-md);
      color:var(--color-content-primary);
      background-color:var(--color-surface-base);
      min-height:100vh;
    }
    /* === App Shell ===
       選んだ pane-*.html の .app-shell / .sidenav / .navbar / .main / .zone--* をコピーする */
    /* === パターン固有のスタイルのみここに追記 === */
  </style>
</head>
<body>

  <!-- App Shell：選んだ pane-*.html の構造を継承する -->
  <div class="app-shell">

    <!-- ───── Side nav（コンテキスト提示用・最小限） ───── -->
    <!-- 当該画面のみ active、他項目はグレーアウトのダミー -->
    <aside class="sidenav">
      <div class="sidenav__header">[サービス名]</div>
      <nav aria-label="メインナビゲーション">
        <a href="#" aria-current="page">[画面名]</a>
        <span>顧客</span>  <!-- ダミー（グレーアウト） -->
        <span>物件</span>  <!-- ダミー（グレーアウト） -->
      </nav>
      <!-- アカウントカード等は pane-*.html の footer 構造を流用 -->
    </aside>

    <!-- ───── Main（+ 必要なら Helper） ───── -->
    <main class="main">
      <!-- PageHeader -->
      <header class="page-header">
        <h1>[画面名]</h1>
        <div class="page-header__actions"><!-- 例: 新規登録ボタン --></div>
      </header>

      <!-- コンテンツエリア：INTERACTION モデルに応じて実装（下記参照） -->
    </main>

  </div>

  <script>
    // パターン固有のインタラクション（Drawer 開閉・Modal・バルク選択など）
  </script>
</body>
</html>
```

> Side nav・PageHeader・各ゾーンの正確なクラス名と寸法は、ベースにした `pane-*.html` に揃えること。上記は構造の目印であり、実体はテンプレートを正とする。

---

## サンプルデータのガイドライン

### primary_object 別のリアルデータ例

**案件:**
```
エアコン故障修理 / サンプルレジデンス渋谷 301号室 / 2026-04-08
給排水設備の点検 / サンプルハイツ新宿 / 2026-04-07
共用部照明交換 / サンプルタワー品川 / 2026-04-06
```

**顧客:**
```
サンプル 花子 / 090-xxxx-xxxx / hanako.sample@example.com
サンプル 一郎 / 03-xxxx-xxxx / ichiro.sample@example.com
サンプル 美咲 / 080-xxxx-xxxx / misaki.sample@example.com
```

**物件:**
```
サンプルレジデンス渋谷 / 東京都渋谷区 / 1LDK 全24室
サンプルハイツ新宿 / 東京都新宿区 / 2LDK 全36室
サンプルタワー品川 / 東京都品川区 / 3LDK 全48室
```

**ステータス（案件）:**
```
受付中 / 確認中 / 手配中 / 完了 / クローズ
```

ステータス表示は `components/base/display/Status tag.md`、件数表示は `Badge.md` のトークン（`color.status.*`）に従う。

---

## パターン別の実装ポイント

### Drawer 型（一覧 + サイド詳細）
- ベーステンプレート: `pane-2r.html`（Main + Helper 右）
- 一覧は Main（伸縮）、詳細は Helper（Drawer）。closed 時は幅 0、open 時は固定幅
- 開閉は `transition` を `var(--duration-normal) var(--easing-default)` で行う
- z-index は `var(--z-drawer)`。参照: `components/base/layout/Drawer.md`、`components/base/table/Table.md`

### Modal 型（軽量な確認・更新）
- ベーステンプレート: `pane-1.html` または `pane-2l.html` + オーバーレイ
- z-index は `var(--z-modal)`。Modal の上に Drawer/Modal を重ねない
- 参照: `components/base/layout/Modals.md`

### コンパクト型
- テーブル行高・フォントサイズを 1 段小さく（`--font-size-sm` 基本）
- 余白は `--spacing-2`〜`--spacing-3` を基調にスキャン重視
- 参照: `components/base/table/Table.md`（compact）

### バルク操作型
- 各行に Checkbox（`components/base/form/Checkbox.md`、Indeterminate 対応）
- 1件以上選択時に「バルクアクションバー」を下部固定表示（`--color-brand-primary` 背景、`--z-drawer` 程度）
- アクションは `components/base/navigation/Buttons.md`

### インラインエディット型
- セルクリックで `<input>` / `<select>` に切り替え（`components/base/table/Table.md` editable、`components/base/form/Text field.md`）
- 確定: Enter / フォーカスアウト、キャンセル: Escape
- 編集中セル: `box-shadow: 0 0 0 2px var(--color-brand-primary)` 等、focus トークンで示す

### ステッパー型（ウィザード）
- ステップ進捗は `components/base/display/Steps.md`
- 3 ステップ以上の明確な手順がある場合に採用

---

## index.html（比較ビュー）の構造

トークンは `:root` の CSS 変数として参照する（直書き禁止）。骨格の例:

```html
<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8">
  <link href="https://fonts.googleapis.com/css2?family=Noto+Sans+JP:wght@400;500;700&display=swap" rel="stylesheet">
  <title>[spec_title] — デザインパターン比較</title>
  <style>
    :root {
      --color-surface-base:#F7F9FB; --color-surface-raised:#ffffff;
      --color-content-primary:#081A27; --color-content-secondary:#5A6C7F;
      --color-border-subtle:#DDE3EB; --color-brand-primary:#2661CF;
      --color-success-text:#376F1C;
      --spacing-2:8px; --spacing-3:12px; --spacing-4:16px; --spacing-6:24px;
      --radius-md:8px; --radius-lg:12px; --radius-full:9999px;
      --shadow-sm:0 1px 3px rgba(0,0,0,0.08); --shadow-md:0 4px 12px rgba(0,0,0,0.12);
      --font-family-base:"Noto Sans JP", system-ui, sans-serif;
    }
    *, *::before, *::after { box-sizing:border-box; margin:0; padding:0; }
    body { font-family:var(--font-family-base); background:var(--color-surface-base);
           color:var(--color-content-primary); min-height:100vh; }
    /* カードグリッド・メモ欄のスタイルもトークン参照で記述する */
  </style>
</head>
<body>
  <div style="max-width:1120px; margin:0 auto; padding:48px 32px;">

    <!-- ヘッダー -->
    <header style="margin-bottom:40px;">
      <h1 style="font-size:24px; font-weight:700;">[spec_title]</h1>
      <p style="margin-top:8px; color:var(--color-content-secondary);">UIデザインパターン比較 — [N]案</p>
    </header>

    <!-- パターンカードグリッド -->
    <div style="display:grid; grid-template-columns:repeat(auto-fill, minmax(280px,1fr)); gap:24px; margin-bottom:48px;">
      <a href="pattern-a.html" target="_blank"
         style="display:block; background:var(--color-surface-raised); border:1px solid var(--color-border-subtle);
                border-radius:var(--radius-lg); padding:24px; box-shadow:var(--shadow-sm); text-decoration:none; color:inherit;">
        <span style="font-size:12px; color:var(--color-brand-primary); background:#F0F7FE;
                     padding:2px 8px; border-radius:var(--radius-full);">パターンA</span>
        <h2 style="margin-top:8px; font-size:16px; font-weight:700;">[パターン名]</h2>
        <p style="margin-top:4px; font-size:14px; color:var(--color-content-secondary);">[ターゲット]</p>
        <div style="margin-top:16px; font-size:14px;">
          <p><span style="color:var(--color-success-text); font-weight:500;">✓ 得意:</span> [Pro]</p>
          <p style="color:var(--color-content-secondary);"><span style="font-weight:500;">△ 課題:</span> [Con]</p>
        </div>
        <p style="margin-top:16px; font-size:14px; color:var(--color-brand-primary); font-weight:500;">開いて確認する →</p>
      </a>
      <!-- pattern-b, pattern-c ... -->
    </div>

    <!-- 選定メモ -->
    <section style="background:var(--color-surface-raised); border:1px solid var(--color-border-subtle);
                    border-radius:var(--radius-lg); padding:24px; box-shadow:var(--shadow-sm);">
      <h2 style="font-size:16px; font-weight:700; margin-bottom:12px;">選定メモ</h2>
      <textarea id="selection-memo" placeholder="採用するパターンと理由を記録する..."
        style="width:100%; height:128px; padding:8px 12px; font-size:14px;
               border:1px solid var(--color-border-subtle); border-radius:var(--radius-md); resize:none;"></textarea>
    </section>

  </div>

  <script>
    const memo = document.getElementById('selection-memo');
    const key = 'ui-ideation-memo-[spec_id]';
    memo.value = localStorage.getItem(key) || '';
    memo.addEventListener('input', () => localStorage.setItem(key, memo.value));
  </script>
</body>
</html>
```
