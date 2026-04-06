# Dashboard Design Guide

デジタル庁「ダッシュボードデザインの実践ガイドブック」に基づくClaude Codeプラグイン。対話的にダッシュボードを設計・構築する。

## インストール

```bash
# マーケットプレースを追加
/plugin marketplace add owner/dashboard-design-guide

# プラグインをインストール
/plugin install dashboard-design-guide@dashboard-design-guide
```

## 使い方

スキルは以下のキーワードで自動トリガーされる:
- ダッシュボード / dashboard
- データ可視化 / KPI表示
- チャート設計 / 指標の見える化

または明示的に呼び出す:
```
/dashboard-design-guide:design
```

## 機能

- **Phase 1**: 要件の対話的整理（1問ずつ確認）
- **Phase 2**: 情報設計と提案（ビジュアルコンパニオンでチャート・レイアウトをブラウザ比較）
- **Phase 3**: 段階的な実装（ビルダーエージェントによる分割構築）
- **Phase 4**: フィードバックと改善
- **Phase 5**: チェックリスト検証

## ビジュアルコンパニオン

Phase 2でチャートの形やレイアウトの選択肢をブラウザで視覚的に比較できる（オプション）。

- 実データでChart.jsチャートを描画し、1指標ずつ2-3案を提示
- レイアウト全体をタブ切り替えで比較
- Node.js が必要

## ライセンス

MIT

ビジュアルコンパニオンのサーバーコンポーネント（`skills/design/scripts/`）は [superpowers](https://github.com/obra/superpowers) から派生（MIT License, Jesse Vincent）。
