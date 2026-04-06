# 出力テンプレート

Phase 3で出力形式に応じて参照する。テンプレートは `output-templates/` ディレクトリに分割されている。

テンプレートはデザインシステムの材料を提供する。哲学（目的から逆算した設計判断）とノウハウ（ガイドブック固有の仕様）の両方を備えた上で、これらの材料を組み立てる。避けるべきは2つ：テンプレートを脳死で適用すること（一貫性はあるが品質がない）と、テンプレートを無視して独自に作ること（品質を狙って一貫性を失う）。

## HTML出力

以下の順序で読み込む：

1. **`output-templates/design-system.md`** — キーカラーテーマシステム。7ファミリーのCSS変数セット。最初に読む。
2. **`output-templates/chart-defaults.md`** — Chart.jsグローバル設定。Do側の仕様を自動適用する。
3. **`output-templates/components.md`** — 9種のコンポーネント（KPIカード、フィルター、チャートコンテナ、テーブル等）のHTML+CSS。
4. **`output-templates/layout-patterns.md`** — 4種のレイアウトパターンのHTML構造。共通ボイラープレート含む。

## 非HTML出力

**`output-templates/non-html-guides.md`** — PowerPoint、Excel、Markdown/Wordの各形式向けガイド。
