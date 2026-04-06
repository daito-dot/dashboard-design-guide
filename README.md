# Dashboard Design Guide

デジタル庁「[ダッシュボードデザインの実践ガイドブック](https://www.digital.go.jp/resources/dashboard-guidebook)」に基づくClaude Codeプラグイン。

対話的にダッシュボードを設計・構築する。要件のヒアリングからチャート選定、レイアウト設計、実装、品質検証まで、5つのPhaseで段階的に進める。

## インストール

```bash
# マーケットプレースを追加
/plugin marketplace add daito-dot/dashboard-design-guide

# プラグインをインストール
/plugin install dashboard-design-guide@dashboard-design-guide
```

開発テスト用:
```bash
claude --plugin-dir ./dashboard-design-guide
```

## 使い方

スキルは以下のキーワードで自動トリガーされる:

- ダッシュボード / dashboard / データ可視化
- KPI表示 / チャート設計 / 指標の見える化
- モニタリング画面

または明示的に呼び出す:

```
/dashboard-design-guide:design
```

## 設計フロー

```
Phase 1  要件の対話的整理（1問ずつ確認、提示型/探索型の判定）
   ↓
        ビジュアルコンパニオン提案（オプション）
   ↓
Phase 2  情報設計と提案
         Step 1: 情報の一覧化
         Step 2: チャート割り当て（実データで描画して比較）
         Step 3: インタラクション設計（探索型の場合）
         Step 4: レイアウト提案（タブ切り替えで比較）
   ↓
        設計承認ゲート
   ↓
Phase 3  段階的な実装（ビルダーエージェントによる3Step構築）
   ↓
Phase 4  フィードバックと改善
   ↓
Phase 5  チェックリスト検証
```

## ビジュアルコンパニオン

Phase 2でチャートの形やレイアウトの選択肢をブラウザで視覚的に比較できる機能（オプション）。

- 1指標ずつ実データでChart.jsチャートを描画し、2-3案を提示
- KPIカードの見せ方（変化率、スパークライン、比較基準）も選択可能
- 探索型ではフィルタ配置やドリルダウン構造もモックアップで比較
- レイアウト全体をタブ切り替えで比較（HTML出力のみ）
- Node.jsが必要

## 出力形式

- **HTML** -- Chart.js + デジタル庁デザインシステム（7カラーファミリー）
- **PowerPoint** -- レイアウトと配色の指針
- **Excel** -- 条件付き書式とチャート設定
- **Markdown** -- テーブルとテキストベースの可視化

## 設計思想

- **教育ではなく示唆**: AIが既に知っている一般的な設計知識は書かない。ガイドブック固有の仕様と、目的から逆算するための原則だけを提供する
- **テンプレートは材料であり完成品ではない**: デザインシステムのアトミックなパーツを提供し、AIが目的に応じて組み立てる
- **避けるべき2つの失敗**: 哲学のないテンプレート適用（形だけ整えて品質がない）と、ノウハウのない独自の工夫（仕様を無視した自己流）

## リファレンスファイル

| ファイル | 内容 |
|---------|------|
| `references/requirements-worksheet.md` | 要件定義ワークシート |
| `references/design-principles.md` | 情報選定・ダッシュボード設計・グラフ設計の原則 |
| `references/chart-selection.md` | チャートタイプ選定ガイド |
| `references/interaction-patterns.md` | 探索型インタラクション設計 |
| `references/visual-companion.md` | ビジュアルコンパニオン操作ガイド |
| `references/builder-prompts.md` | ビルダーエージェントのプロンプトテンプレート |
| `references/layout-grid.md` | グリッドシステムとレイアウトパターン |
| `references/color-palette.md` | デジタル庁カラーパレット全仕様 |
| `references/accessibility.md` | アクセシビリティ要件 |
| `references/output-templates.md` | 出力テンプレートのインデックス |
| `references/checklist.md` | ダッシュボード品質チェックリスト |
| `references/summary-text-format.md` | 要約テキストフォーマット |

## 原典

デジタル庁「[ダッシュボードデザインの実践ガイドブック](https://www.digital.go.jp/resources/dashboard-guidebook)」（2024年公開、2025年3月改訂）

このプラグインはガイドブックの内容をClaude Codeスキルとして再構成したものであり、ガイドブックそのものは含まれていない。

## ライセンス

MIT License -- 詳細は [LICENSE](LICENSE) を参照。

ビジュアルコンパニオンのサーバーコンポーネント（`skills/design/scripts/`）は [superpowers](https://github.com/obra/superpowers) から派生（MIT License, Copyright (c) 2025 Jesse Vincent）。詳細は [skills/design/scripts/LICENSE](skills/design/scripts/LICENSE) を参照。
