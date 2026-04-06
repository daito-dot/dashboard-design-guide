# ビルダーエージェント プロンプトテンプレート

Phase 3でHTML/PowerPoint/Excel/Markdownを構築する際に使用する。メインエージェントはオーケストレーターとして、設計仕様をテンプレートに埋め、各Stepのビルダーエージェントを起動する。自分では出力ファイルを書かない。

## 使い方

1. Phase 1-2で決まった設計仕様を「設計仕様テンプレート」に埋める
2. 出力形式に応じた「読み込みファイル対応表」を確認する
3. Step 1ビルダーを起動し、出力をユーザーに見せる
4. ユーザー確認後、Step 2ビルダーを起動する（Step 1の出力を渡す）
5. ユーザー確認後、Step 3ビルダーを起動する（Step 2の出力を渡す）
6. ユーザー確認後、Phase 4に進む

**ビルダーへの渡し方**: 設計仕様テンプレート（埋め済み）＋各Stepのプロンプトテンプレートをサブエージェントのpromptに含める。リファレンスファイルの内容は渡さない — ビルダーが自分で読む。

---

## 設計仕様テンプレート

Phase 1-2の成果物。メインエージェントがこれを埋めてビルダーに渡す。

```
## 設計仕様
- ダッシュボードタイトル: {{タイトル}}
- 類型: {{提示型 / 探索型}}
- 出力形式: {{HTML / PowerPoint / Excel / Markdown}}
- キーカラー: {{Blue / Light Blue / Cyan / Green / Orange / Red / Solid Gray}}
- ターゲットデバイス: {{PC / タブレット / プロジェクター}}

## 情報一覧
| 情報 | 表現手段 | チャートタイプ | データソース |
|------|---------|-------------|------------|
| {{指標A}} | {{KPIカード}} | — | {{値}} |
| {{トレンドB}} | {{チャート}} | {{折れ線}} | {{データパスまたは値}} |
| ... | ... | ... | ... |

## レイアウト
{{承認されたレイアウトの記述 — グリッド配置、ページ構成、タブ構成}}

## インタラクション（探索型の場合）
{{ドリルダウン、クロスフィルタ、タブナビゲーション等の設計}}

## データ
{{実データのファイルパス、または値のリスト/JSON}}
```

---

## 読み込みファイル対応表

ビルダーは出力形式に応じて以下のファイルを自分で読む。パスはスキルディレクトリからの相対パス。

### HTML出力

| Step | 読むファイル |
|------|-----------|
| Step 1 | `references/output-templates/design-system.md`, `references/output-templates/layout-patterns.md` |
| Step 2 | `references/output-templates/chart-defaults.md`, `references/output-templates/components.md`, `references/chart-selection.md` |
| Step 2（探索型） | 上記に加えて `references/interaction-patterns.md` |
| Step 3 | `references/color-palette.md`, `references/accessibility.md` |

### PowerPoint / Excel / Markdown出力

| Step | 読むファイル |
|------|-----------|
| Step 1 | `references/output-templates/non-html-guides.md`, `references/color-palette.md` |
| Step 2 | `references/chart-selection.md`, `references/output-templates/non-html-guides.md` |
| Step 3 | `references/color-palette.md` |

---

## Step 1ビルダー: 骨格レイアウト

```
Agent tool (general-purpose):
  description: "Dashboard Step 1: 骨格レイアウト構築"
  prompt: |
    あなたはダッシュボードの骨格レイアウトを構築するビルダーです。

    ## 設計仕様

    {{メインが埋めた設計仕様テンプレートをここに貼る}}

    ## あなたの仕事

    1. 以下のリファレンスファイルを読む（必ず全文を読むこと）:
       {{出力形式に応じたStep 1のファイルパスを列挙}}

    2. リファレンスの材料を使って骨格を組む:
       - 出力形式がHTMLの場合:
         - design-system.mdのCSS変数を`:root`に定義する
         - layout-patterns.mdの共通HTMLシェルを使用する
         - 設計仕様のレイアウトに従ってグリッド配置とセクション構造を組む
       - 出力形式がPowerPoint/Excel/Markdownの場合:
         - non-html-guides.mdの構造ガイドに従う

    3. この段階では:
       - データはダミー値を使う
       - 色はモノクロ（CSS変数は定義するが装飾的な色は付けない）
       - 構造の良し悪しをこの段階で判断できるようにする

    4. リファレンスに定義されたクラス名、CSS変数名、HTML構造をそのまま使う。
       独自のクラス名やインラインスタイルで代替しない。

    ## 出力

    完成した骨格ファイルの全文を出力する。
    ファイルパス: {{出力ファイルパス}}
```

---

## Step 2ビルダー: チャートとデータの配置

```
Agent tool (general-purpose):
  description: "Dashboard Step 2: チャートとデータの配置"
  prompt: |
    あなたはダッシュボードにチャートとデータを配置するビルダーです。

    ## 設計仕様

    {{メインが埋めた設計仕様テンプレートをここに貼る}}

    ## Step 1の出力

    以下のファイルがStep 1で生成されています。まずこれを読んでください:
    {{Step 1出力ファイルのパス}}

    ## あなたの仕事

    1. 以下のリファレンスファイルを読む（必ず全文を読むこと）:
       {{出力形式に応じたStep 2のファイルパスを列挙}}

    2. Step 1の骨格にチャートとデータを配置する:
       - 出力形式がHTMLの場合:
         - chart-defaults.mdの`Chart.defaults`設定コードを`<script>`内の最初に配置する。
           省略しない。このコードがガイドブックのDo/Don'tルールをチャートに自動適用する
         - chart-defaults.mdの`getCSSVar()`ヘルパーを使ってチャートの色を設定する
         - components.mdのクラスを使ってKPIカード、テーブル等を構築する。
           インラインスタイルで代替しない
         - chart-selection.mdのルールに従ってチャートを構成する
         - KPIカードには数値＋変化率を含める（変化率が存在しないデータの場合は
           補足情報で代替する）
       - 出力形式がPowerPoint/Excel/Markdownの場合:
         - non-html-guides.mdの実装方法に従う

    3. 設計仕様のデータセクションに記載されたデータを流し込む

    4. 探索型の場合:
       - interaction-patterns.mdを読み、設計仕様のインタラクション設計に従って
         コンポーネントを配置する

    ## 出力

    完成したファイルの全文を出力する。
    ファイルパス: {{出力ファイルパス}}
```

---

## Step 3ビルダー: 仕上げ

```
Agent tool (general-purpose):
  description: "Dashboard Step 3: 仕上げ"
  prompt: |
    あなたはダッシュボードの仕上げを行うビルダーです。

    ## 設計仕様

    {{メインが埋めた設計仕様テンプレートをここに貼る}}

    ## Step 2の出力

    以下のファイルがStep 2で生成されています。まずこれを読んでください:
    {{Step 2出力ファイルのパス}}

    ## あなたの仕事

    1. 以下のリファレンスファイルを読む（必ず全文を読むこと）:
       {{出力形式に応じたStep 3のファイルパスを列挙}}

    2. 品質を確認し、不足を補う:
       - color-palette.mdのカラー仕様が正しく適用されているか確認する
       - 出力形式がHTMLの場合:
         - accessibility.mdの要件を確認する:
           - 全チャートにaria-labelが設定されているか
           - キーボードナビゲーションが機能するか（タブ等）
           - 色だけで情報を識別していないか
           - コントラスト比が確保されているか
         - フッターに出典とデータ基準日が記載されているか
         - CSS変数を使わずにハードコードされたhex値がないか

    3. 問題が見つかった場合は修正する

    ## ユーザーフィードバック（ある場合）

    {{前のStepでユーザーから受けたフィードバックがあればここに記載}}

    ## 出力

    完成したファイルの全文を出力する。
    ファイルパス: {{出力ファイルパス}}
```
