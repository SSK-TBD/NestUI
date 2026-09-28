# Foundations

> **このファイルの位置づけ**
> ここは「生成の方向づけ」を担う思想の層（何を選ぶか・なぜそうするか）。
> 機械的な禁則判定は、禁則ルール集の別レイヤーが担う（ここには持たない）。
> 両者は重複ではなく、生成側（方向づけ）と判定側（禁則）で同じ対象を別の役割で語る。
> 両者が同じ数値・閾値に触れる箇所は連動する。方針変更時は両方を見直すこと。
>
> **token 参照の記法**: token ファイルは **ファイル名のみ** を記す（すべて `docs/designsystem/tokens/` 配下、具体値は各 JSON が正）。

UI 全体を貫く **デザイン原則**（何を選ぶか・なぜそうするか）。対象プロダクトは **カナリークラウド**（不動産仲介会社向け CRM / 業務 SaaS）。

## 設計コンセプト

- **主ユーザー**: 不動産仲介会社の営業担当・管理者（IT リテラシーにばらつき）
- **利用状況**: 顧客管理・反響対応・追客の長時間/高頻度デスクトップ利用。紙・FAX・電話が残るアナログ産業で例外処理が多い
- **優先すること**: 情報の読み取り速度、誤操作の防止、視覚的疲労の軽減

## Design Principles

UI 構成の基本則（NestUI 基盤）とカナリー事業思想を統合した原則。

1. **Layered** — Background → Surface → Text/Object の3層で構成する（`color.surface` の base/raised/overlay）
2. **Contrast** — テキストは背景に対し WCAG AA（4.5:1 以上）。純黒は使わず `color.content.primary` を使う
3. **Semantic** — 色は用途で指定する（`color.surface`/`content`/`border`/`semantic` 経由。生のパレット直書き禁止）
4. **Minimal** — 1 View に使う色は3色まで（背景・アクセント・テキスト）。装飾を足すか迷ったら「業務の理解を助けるか？」で判断
5. **Grid** — スペーシングは 4 の倍数（`spacing`）。8 の倍数を推奨
6. **Pattern-First** — ページレイアウトは `layout_patterns/` の既定パターンを使う。自前で組まない
7. **オブジェクト型 UI** — 顧客・物件・反響などオブジェクトに対し、行いたいアクションに迷わずたどり着く。機能羅列型ナビは禁止
8. **周辺情報の閲覧性** — 関連情報を近くに配置しコンテキストを失わせない。モーダルの多重展開は禁止
9. **ノイズのカット** — デフォルト最小表示。Progressive Disclosure を徹底。入力を強制する UI（必須項目の乱立）は禁止
10. **システムによる自動化** — つながりを自動化し入力コストを下げる
11. **Inclusive** — 誰もが使えることは仕様でなく信念。キーボード/スクリーンリーダー対応を前提として設計（→ Accessibility）
12. **Machine-Readable** — トークンは機械可読 JSON が真実の源。実装コードが正

## States

通常状態だけで設計を終えない。**空・エラー・ローディング・完了の状態を必ず設計する**。各状態のコンポーネント選択と仕様は `COMPONENT_GUIDE.md`「状態設計」を参照。原則は **行き止まりを作らない（全状態に次のアクション CTA）**。ただし CTA は必ずしもボタンである必要はなく、次に取れる行動が文言で示せていれば行き止まりではない。

## Colors

セマンティックな役割で色を選ぶ。値は `color.json` を参照。

| Role | トークン | 用途 |
|---|---|---|
| 操作色 Primary | `color.primary.700` | CTA・アクティブ・主要インタラクション |
| ブランド | `color.brand.500` | ブランド表現（操作色として使わない） |
| Surface | `color.surface.*` | 背景3層（base/raised/overlay/sunken） |
| Content | `color.content.*` | テキスト（primary/secondary/disabled/inverse） |
| Border | `color.border.*` | 境界（default/subtle/strong/focus） |
| Status | `color.semantic.*` / `color.status.*` | error/warning/success/info |

原則:
- 生のカラーコード・生の Tailwind パレットを直書きせず、必ずトークン経由で参照する
- 同一ビューで操作色（Primary）を使う要素は最大 1〜2 個
- 全テキストで 4.5:1 以上のコントラストを維持。純黒（#000）は使わない
- 色だけで情報を伝えない（アイコン/テキストを必ず併用）

## Typography

UI フォントは **Noto Sans JP**。値は `typography.json` を参照。

- ウェイトは 3 種（`regular` 400 / `medium` 500 / `bold` 700）。**見出しは bold**（semibold 600 は使わない）、`font-light`(300) 禁止
- 字間（letterSpacing）: Title 3% / Label 2% / Body 0%。`tracking-tight` は日本語可読性を損なうため禁止
- 本文は行間ゆったり（`lineHeight.relaxed`）。最小フォントは 11px
- 役割は `typography.scale.*`（headline / body / label / caption）から選ぶ

## Spacing

4px グリッドを基準とする。値は `spacing.json`、参照ルート（margin/padding 使い分け・コンポーネントは padding のみ持つ・例外時の直参照）は `TOKEN_GUIDE.md`。

用途別の標準値（揺れを抑えるため原則この対応で割り当てる）:

- App Shell の主要ゾーン間: 32px（`spacing.8`）
- セクション間: 24px（`spacing.6`）
- 関連サブセクション間: 20px（`spacing.5`）
- カード/フォーム内のフィールド間: 16px（`spacing.4`）
- ラベルと入力の隙間など細部: 12 / 8 / 4px
- 例外（Hero・空状態など広い余白）: 40 / 48px
- レイアウト固定寸法（サイドバー幅 188px・ヘッダー 68px 等）は `spacing.layout.*`

## Border Radius

値は `radius.json`。

- 同一ビューで角丸と角張った角を混在させない
- 標準は `radius.sm`(4px) / `md`(8px) / `lg`(12px) / `full`。任意値を直書きしない

## Elevation

値は `elevation.json`。

- Card はエレベーション（影）を持たせず、境界線（`color.border.default`）と背景コントラストで区別する
- Modal・Drawer・Popover など浮き上がる要素のみ shadow を使用。`shadow-lg`/`shadow-2xl` 相当の強い影は避ける
- z-index は `elevation.z.*` のみ（dropdown=20 / sticky=30 / overlay=40 / modal=50）

## Motion / Emotional Feedback

値は `motion.json`。**データ更新・状態変化にのみ**アニメーションを使い、装飾アニメーションは使わない。

- フィードバックは派手さでなく確実さ。**Success 時のみ温かい演出**（fade-in + check scale）、**Error は叫ばない**（控えめな shake 最大3px + Danger ボーダー、テキスト併用）、Warning/Info は穏やかな fade-in
- 持続時間は 150〜300ms。`duration-500` 以上は使わない（Progress は例外）。常時ループ・bounce・spin は禁止
- `prefers-reduced-motion` を尊重し、動きのみ除去（色変化・テキストは維持）

## Accessibility

- **コントラスト**: 通常テキスト 4.5:1 / 大テキスト・UI 要素 3:1 以上（AA）
- **キーボード**: 全インタラクティブ要素を Tab/Enter/Space/Escape で操作可能に。フォーカスインジケーターを必ず可視化（`focus-visible:ring`）。`tabindex` の乱用禁止（DOM 順）
- **フォーカストラップ**: モーダル内を循環し、閉じたらトリガーへ戻す
- **ARIA**: モーダル `role="dialog" aria-modal`、アイコンボタン `aria-label`、エラー `aria-invalid`/`aria-describedby`、トグル `aria-pressed`、動的更新 `aria-live`、Active ナビ/ステップ `aria-current`
- **ラベル**: フォーム入力に必ず `<label>`（プレースホルダーで代替しない）
- **色＋テキスト併用**: 状態を色だけで伝えない
- **タップ領域**: 最小 44×44px。ボタン高は S `h-8` / M `h-10` / L `h-12`（インラインリンクは例外）
- **テキスト拡大**: 200% 拡大でクリッピング・破綻しない

## UI リファレンス（優先順位）

判断に迷ったら上から参照する。

| Tier | DS | 範囲 |
|------|-----|------|
| 1 ベース | Atlassian Design System | コンポーネント設計・パターン・トークン体系・業務 SaaS の構造 |
| 2 原則 | HIG / Material Design | 操作性・インタラクション原則・レスポンシブ |

## Avoid（思想レベルの境界線）

| NG | 理由 |
|---|---|
| 装飾過多・グラデーション祭り | ノイズのカットに反する。業務 SaaS の装飾は信頼を毀損 |
| 機能の羅列型ナビゲーション | オブジェクト型 UI の対極 |
| モーダルの多重展開 | コンテキストを破壊する |
| 入力を強制する UI（必須項目の乱立） | 自動化の思想に反する |
| 差別化のための独自 UI | 業務ソフトは王道パターンが最優先 |
| 回遊させるナビゲーション | 業務 SaaS は最短で目的に到達させる |
| 感情に訴える装飾・コピー | 業務フローのテンポを崩す |
| UI がコンテンツより目立つ装飾 | ユーザーの時間を返す設計に反する |

**テーマ**: ライトモードのみ（ダークモードは OFF）。
