# Component Guideline: Range Sliders

## 1. Component Identity
- **Name**: Range Sliders
- **Variants**: `single`（単一値）/ `range`（2ハンドル）
- **Responsibility**:
  - する: 連続した数値範囲の中から値または範囲を直感的に選択させる
  - しない: 厳密な数値入力（Input Numberを使う）・進捗表示（Progressを使う）
- **Target Platform**: デスクトップ Web アプリ

---

## 2. Variants & States

**Variants**

| Variant | 用途 |
|---------|------|
| `single` | 単一値の選択（ボリューム・しきい値・タイムアウト） |
| `range` | 範囲選択（価格帯・日数範囲・スコア範囲） |

**States**

| State | 変化するプロパティ |
|-------|-----------------|
| default | トラック: `color.surface.raised`、フィル: `color.brand.primary`、ハンドル: white |
| hover (ハンドル) | ハンドルに shadow 追加、`transition: 100ms` |
| focus (ハンドル) | `outline: 2px solid color.border.focus` (offset 2px) |
| active (drag) | ハンドルサイズを拡大（20px→24px）、ツールチップ表示 |
| disabled | `opacity: 0.4`、`cursor: not-allowed` |

---

## 3. Props / API

| Prop | Type | Default | Required | Description |
|------|------|---------|----------|-------------|
| `value` | `number` | — | — | 現在値（single 制御） |
| `range` | `[number, number]` | — | — | 現在範囲（range 制御） |
| `min` | `number` | `0` | — | 最小値 |
| `max` | `number` | `100` | — | 最大値 |
| `step` | `number` | `1` | — | ステップ幅 |
| `variant` | `'single' \| 'range'` | `'single'` | — | バリアント |
| `isDisabled` | `boolean` | `false` | — | 非活性 |
| `showTooltip` | `boolean` | `true` | — | ドラッグ中のツールチップ表示 |
| `label` | `string` | — | — | スライダーのラベル |
| `formatValue` | `(v: number) => string` | `v => String(v)` | — | 値のフォーマット関数 |
| `onChange` | `(value: number) => void` | — | — | 値変更（single） |
| `onRangeChange` | `(range: [number, number]) => void` | — | — | 範囲変更（range） |

**Event Handlers**
- `onChange(value: number)`: 値変更（single）
- `onRangeChange(range: [number, number])`: 範囲変更（range）

---

## 4. Composition Rules

**許可する子要素**
- なし（props のみ）

**Anti-patterns**
- NG: `step` を細かくしすぎる（0.001 など）→ ドラッグ精度の問題。数値入力（Input Number）と組み合わせる
- NG: `min` / `max` を設定しない

---

## 5. Layout & Spacing

| プロパティ | トークン | 値 |
|-----------|---------|-----|
| トラック高さ | — | 4px |
| ハンドル直径（default） | — | 20px |
| ハンドル直径（active） | — | 24px |
| ハンドル border-radius | `radius.full` | 9999px |
| ラベル-スライダー gap | `spacing.2` | 8px |
| ツールチップ offset | — | 8px |

---

## 6. Token Mapping

| 用途 | トークン | 値 |
|------|---------|-----|
| トラック（未選択） | `color.surface.raised` | — |
| フィル（選択済み） | `color.brand.primary` | — |
| ハンドル | white | — |
| ハンドル border | `color.brand.primary` | — |
| focus ring | `color.border.focus` | — |

---

## 8. Accessibility

- **Role**: `slider`
- **必須ARIA属性**:
  - `role="slider"`, `aria-valuenow`, `aria-valuemin`, `aria-valuemax`
  - `aria-label="[label]"` または `aria-labelledby`
  - range: 2つの `slider` それぞれに `aria-label="最小値"` / `aria-label="最大値"`
- **キーボード操作**:
  - `←` / `↓`: 値を -step
  - `→` / `↑`: 値を +step
  - `Page Down` / `Page Up`: 値を -(step×10) / +(step×10)
  - `Home` / `End`: min / max
- **コントラスト比**: ハンドル vs トラック 3:1 以上

---

## 9. Content Guidelines

- `formatValue` で単位を付ける（`v => v + 'ms'`、`v => '¥' + v.toLocaleString()`）
- ツールチップに現在値を表示

---

## 10. Usage Do / Don't

**Do**
```tsx
// 単一値
<RangeSlider
  label="タイムアウト"
  value={timeout}
  min={100}
  max={30000}
  step={100}
  formatValue={v => `${v}ms`}
  onChange={setTimeout}
/>

// 範囲選択
<RangeSlider
  variant="range"
  label="ポート範囲"
  range={portRange}
  min={0}
  max={65535}
  onRangeChange={setPortRange}
/>
```

**Don't**
```tsx
// NG: step が非常に細かい（精度の問題）
<RangeSlider step={0.001} min={0} max={1} />
// → Input Number と組み合わせて精度を確保

// NG: min/max なし
<RangeSlider value={val} onChange={setVal} />
```

---

## 11. Implementation Notes

- ドラッグ操作は `onMouseDown` / `onTouchStart` → `mousemove` / `touchmove` → `mouseup` / `touchend` のシーケンスで制御
- range variant: 2つのハンドルが交差しないようにバリデーション
- テストチェックリスト:
  - [ ] キーボードの矢印キーでの値変更確認
  - [ ] `aria-valuenow` の更新確認
  - [ ] range variant でハンドルが交差しないこと

---

## 12. Change Log & Deprecation

| Version | 変更内容 | 対応期限 |
|---------|---------|---------|
| v1.0 | 初期リリース | — |
