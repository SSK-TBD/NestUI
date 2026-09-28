# Component Guideline: Steps

> NestUI Steps（Stepper）仕様。Tailwind CSS。値は `tokens/`（操作色は `color.primary.700` 等）を正とし、以下の class はその適用例。

## 1. Component Identity
- **Name**: Steps（Stepper）
- **Variants**: `horizontal`（水平）/ `vertical`（垂直）
- **Responsibility**:
  - する: マルチステップのウィザード・フローの全体ステップ数と現在位置・進捗を示す
  - しない: 単純な進捗バー（→ `Progress`）、タブ切り替え（→ `Tabs`）、ステップ間のナビゲーション（移動はフォーム内ボタンで制御）
- **Target Platform**: デスクトップ Web アプリ（業務 SaaS）

**原則**: プロセス可視化（全体ステップ数と現在位置を常に把握できる）/ 3状態を色＋ボーダーで明確に区分（色だけに頼らない）/ クリック不可が基本（進捗表示であってナビゲーションではない）/ 全状態でステップ番号を表示（チェックアイコンは使用しない）。

---

## 2. Variants & States

**Variants（方向）**

| Variant | 用途 | レイアウト |
|---------|------|------|
| `horizontal`（既定） | ウィザード・チェックアウト。4ステップ以下に適する | ステップを横に並べる |
| `vertical` | サイドバー内・長い説明付き。5ステップ以上 | ステップを縦に並べる |

**States（各ステップ）**

| State | 説明 | Indicator（Medium） | ラベル色 |
|-------|------|----------------------|----------|
| `completed` | 完了済み | `border-2 border-[#dde3eb]` + 番号（`text-[#081a27]`） | `text-[#3e5062]` |
| `current` | 現在のステップ | `bg-primary-700` + 番号（`text-white`）+ `aria-current="step"` | `text-[#081a27]` |
| `upcoming` | 未着手 | `bg-[#edf0f3]` + 番号（`text-[#a1afc0]`） | `text-[#a1afc0]` |
| `disabled` | 非活性（スキップ不可） | `bg-[#edf0f3]` + 番号（`text-[#a1afc0]`） | `text-[#a1afc0]` |

> 全状態でステップ番号を表示する。`completed` をチェックアイコンで代替しない。

---

## 3. サイズ / パーツ

**サイズ**

| サイズ | Indicator | 番号 | 用途 |
|--------|-----------|------|------|
| Medium（既定） | `w-6 h-6`（24px） | あり | 通常のウィザード |
| Small | `w-4 h-4`（16px） | なし | 狭いスペース（ラベルなしでも可） |

**Indicator 共通 class**: `rounded-full inline-flex items-center justify-center flex-shrink-0`（Medium は `+ text-[13px] font-bold`）

**パーツ**

| パーツ | 役割 | 必須 |
|--------|------|------|
| Container | Steps 全体のラッパー（`role="list"`） | Yes |
| Step | 個々のステップ = Indicator + Label（`role="listitem"`） | Yes |
| Indicator | 状態を示す円形インジケーター（番号） | Yes |
| Label | ステップ名称（`text-[14px] font-medium tracking-[0.28px]`） | Yes |
| Description | 補足説明（vertical のみ・`text-xs text-[#a1afc0] mt-0.5`） | No |
| Connector | ステップ間の接続線。端は `opacity-0` で非表示 | Yes（最初・最後以外） |

---

## 4. Composition Rules

許可: props（ステップ定義）のみ。Indicator + Label（+ vertical の Description）。
禁止: ウィザードのコンテンツを Steps 内に格納すること（Steps はナビゲーション表示のみ。コンテンツはページ/別コンポーネントで管理）/ チェックアイコンでの番号代替 / `w-4 h-4`（Small）未満の Indicator / Connector への `mx-*` 余白付与（端は `opacity-0` で対応）。
配置: ステップ数が 8 個を超える場合は複数ページ・サブステップに分割する。Connector は各ステップ内に左右2本持ち、全区間 `bg-[#dde3eb]` で統一する。

---

## 5. Layout & Spacing

- Indicator サイズ: Medium `w-6 h-6`(24px) / Small `w-4 h-4`(16px)
- Indicator-Label gap（horizontal）: `gap-2`(8px)
- Indicator-Description 間（vertical）: `gap-4`(16px)、Description は `mt-0.5`
- Connector 太さ: `h-[2px]`（horizontal）/ `w-[2px]`（vertical）。`flex-1` で区間を埋める
- ラベル font-size: `text-[14px]`（`typography.fontSize.sm` 近傍）、font-weight `font-medium`、`tracking-[0.28px]`
- 外側 `margin` は持たない。ステップ間の距離は `flex-1` の Connector / 親レイアウトの `gap` で確保する

**Connector（horizontal）**: `flex-1 h-[2px] bg-[#dde3eb]`（全区間同色）。最初のステップの Left と最後のステップの Right に `opacity-0`。
**Connector（vertical）**: `w-[2px] flex-1 mt-2 bg-[#dde3eb]`。最後のステップにはコネクター不要。

---

## 6. Token Mapping

| 用途 | トークン |
|------|---------|
| current Indicator 背景 | `color.primary.700`（#2661cf・`bg-primary-700`） |
| current Indicator 文字 | `color.content.inverse`（white・`text-white`） |
| current ラベル | `color.content.primary`（#081a27・`text-[#081a27]`） |
| completed Indicator ボーダー | `color.border.default`（#dde3eb・`border-[#dde3eb]`） |
| completed Indicator 文字 | `color.content.primary`（#081a27） |
| completed ラベル | `color.content.secondary`（#3e5062・`text-[#3e5062]`） |
| upcoming / disabled Indicator 背景 | `color.surface.sunken`（#edf0f3・`bg-[#edf0f3]`） |
| upcoming / disabled 文字・ラベル | `color.content.disabled`（#a1afc0・`text-[#a1afc0]`） |
| Connector（全区間） | `color.border.default`（#dde3eb・`bg-[#dde3eb]`） |
| Description（vertical） | `color.content.disabled`（#a1afc0） |

> Connector はステップ状態にかかわらず全区間 `#dde3eb` 固定。

---

## 7. Accessibility

| 項目 | 要件 |
|------|------|
| Container | `role="list"` を付与（`<ol>` で実装可） |
| Step | `role="listitem"` を付与 |
| current ステップ | `aria-current="step"` を付与（必須） |
| completed Indicator | 番号に `<span class="sr-only">（完了）</span>` を追加 |
| Small（番号なし） | Indicator に `aria-label="ステップN（完了/進行中/未着手）"` を付与 |

- コントラスト: テキスト 4.5:1 以上。色だけでステップ状態を区別しない（ボーダー・番号を併用）
- クリック可（完了済みステップに戻れる仕様）にする場合のみ、対象 Step に `role="button"` / `tabIndex={0}` を付与し Tab → Enter/Space で移動

## 8. Content Guidelines
- ラベルは短く（3単語以内）、完了形の名詞で（「基本情報」「確認」「完了」）
- Description は任意で補足説明を1行（vertical のみ）

## 9. Usage Do / Don't

```html
<!-- Do: 水平（3ステップ、Step 2 が current）-->
<div role="list" class="flex items-start">
  <!-- Step 1: completed -->
  <div role="listitem" class="flex flex-col items-center gap-2 flex-1">
    <div class="flex items-center w-full">
      <div class="flex-1 h-[2px] bg-[#dde3eb] opacity-0"></div>
      <div class="w-6 h-6 rounded-full border-2 border-[#dde3eb] inline-flex items-center justify-center flex-shrink-0 text-[13px] font-bold text-[#081a27]">
        <span class="sr-only">（完了）</span>1
      </div>
      <div class="flex-1 h-[2px] bg-[#dde3eb]"></div>
    </div>
    <span class="text-[14px] font-medium tracking-[0.28px] text-[#3e5062] text-center w-full leading-normal">アカウント</span>
  </div>
  <!-- Step 2: current -->
  <div role="listitem" class="flex flex-col items-center gap-2 flex-1" aria-current="step">
    <div class="flex items-center w-full">
      <div class="flex-1 h-[2px] bg-[#dde3eb]"></div>
      <div class="w-6 h-6 rounded-full bg-primary-700 inline-flex items-center justify-center flex-shrink-0 text-[13px] font-bold text-white">2</div>
      <div class="flex-1 h-[2px] bg-[#dde3eb]"></div>
    </div>
    <span class="text-[14px] font-medium tracking-[0.28px] text-[#081a27] text-center w-full leading-normal">プロフィール</span>
  </div>
  <!-- Step 3: upcoming -->
  <div role="listitem" class="flex flex-col items-center gap-2 flex-1">
    <div class="flex items-center w-full">
      <div class="flex-1 h-[2px] bg-[#dde3eb]"></div>
      <div class="w-6 h-6 rounded-full bg-[#edf0f3] inline-flex items-center justify-center flex-shrink-0 text-[13px] font-bold text-[#a1afc0]">3</div>
      <div class="flex-1 h-[2px] bg-[#dde3eb] opacity-0"></div>
    </div>
    <span class="text-[14px] font-medium tracking-[0.28px] text-[#a1afc0] text-center w-full leading-normal">確認</span>
  </div>
</div>

<!-- Do: 垂直（ラベル + 説明付き、Step 2 current の一部）-->
<div role="list">
  <div role="listitem" class="flex gap-4" aria-current="step">
    <div class="flex flex-col items-center">
      <div class="w-6 h-6 rounded-full bg-primary-700 inline-flex items-center justify-center flex-shrink-0 text-[13px] font-bold text-white">2</div>
      <div class="w-[2px] flex-1 mt-2 bg-[#dde3eb]"></div>
    </div>
    <div class="pb-6">
      <p class="text-[14px] font-medium tracking-[0.28px] text-[#081a27]">プロフィール設定</p>
      <p class="text-xs text-[#a1afc0] mt-0.5">名前とアバターを設定してください</p>
    </div>
  </div>
</div>

<!-- Do: Small（水平・ラベルなし、aria-label 必須）-->
<div role="list" class="flex items-center">
  <div role="listitem" class="flex items-center flex-1" aria-current="step">
    <div class="flex-1 h-[2px] bg-[#dde3eb]"></div>
    <div class="w-4 h-4 rounded-full bg-primary-700 flex-shrink-0" aria-label="ステップ2（進行中）"></div>
    <div class="flex-1 h-[2px] bg-[#dde3eb]"></div>
  </div>
</div>
```

**Don't**: チェックアイコンで完了ステップの番号を代替（→ 全状態で番号表示）/ 色だけでステップ状態を区別 / `aria-current="step"` を current ステップに付与しない / `w-4 h-4`（Small）未満の Indicator / Connector の色をステップ状態で変える（→ 全区間 `bg-[#dde3eb]` 固定）/ Connector に `mx-*` 余白を設ける（→ 端は `opacity-0`）/ コンテンツを Steps 内に格納（→ Steps はナビ表示のみ）/ ステップ数 9 個以上（→ 分割）。

---

## 10. Implementation Notes
- `current` 変更時は対応するフォームコンテンツをページ/コンポーネントで切り替える（Steps は進捗表示のみ）
- ステップ間の移動はフォーム内の `navigation/Buttons.md` のボタンで制御する
- `error` 等のバリデーション失敗表示が要る場合も色だけに頼らず番号・テキストを併用する
- テストチェックリスト: completed / current / upcoming / disabled の各状態 / `aria-current="step"` が current に付くこと / Connector の端 `opacity-0` / クリック可時のキーボード操作
- 思想は `FOUNDATIONS.md`、トークン参照は `guidelines/TOKEN_GUIDE.md`、使い分けは `COMPONENT_GUIDE.md`
