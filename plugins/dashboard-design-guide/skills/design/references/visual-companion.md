# ビジュアルコンパニオン

Phase 2 の設計判断をブラウザで視覚化し、ユーザーが実際のチャートやレイアウトを見て選択できるようにする。

## スコープ

| Step | 用途 | 内容 |
|------|------|------|
| Step 2（チャート割り当て） | 個別指標のチャート選定 | 実データでChart.js描画。1指標ずつ2-3案を提示 |
| Step 3（インタラクション設計） | 探索型要素の視覚化 | フィルタ配置やドリルダウン構造のモックアップ |
| Step 4（レイアウト提案） | 全体配置の比較 | タブ切り替えで2-3案のレイアウトを比較。HTML出力のみ |

Phase 1（要件整理）、Phase 3（実装）、Phase 5（検証）ではブラウザを使わない。

非HTML出力（PowerPoint/Excel/Markdown）の場合: Step 2-3は使う（チャートの形の判断は出力形式に依存しない）。Step 4はHTMLのみ。

## 同意の取得

Phase 1 完了後、Phase 2 開始前に1回だけ提案する。このメッセージは単独で送り、他の内容と混ぜない:

> 「設計の判断をブラウザで視覚的に比較できるようにしますか？ チャートの形やレイアウトの選択肢を実際に見ながら決められます。（ローカルURLを開く必要があります）」

ユーザーが断った場合はテキストのみで Phase 2 を進める。

## サーバーの起動

Step 2 到達時にサーバーを起動する（同意直後ではない）。

```bash
SCRIPTS_DIR="{{このスキルのscriptsディレクトリの絶対パス}}"
"$SCRIPTS_DIR/start-server.sh" --project-dir "$PROJECT_DIR"
```

戻り値の `screen_dir` と `state_dir` を保持する。ユーザーにURLを伝えてブラウザで開いてもらう。

## Step 2: チャート選定の視覚化

### フロー

1. 情報一覧（Step 1）でリストアップした指標を1つ取る
2. その指標に対して2-3案の表示方法を提示する（Chart.jsで実データを描画）
3. ユーザーがクリックで選択、またはターミナルで追加パターンを要求
4. 追加パターン要求があれば生成して表示
5. 決定したら次の指標へ
6. KPIカードを含むすべての指標で実施する（スキップ禁止）

### 画面構成

各案を縦に積み、それぞれクリック選択可能にする。`.option` を使用。

```html
<h2>売上推移: どの表示が適切ですか？</h2>
<p class="subtitle">実際のデータで3パターンを描画しました。クリックして選んでください</p>

<div class="options">
  <div class="option" data-choice="a" onclick="toggleSelect(this)">
    <div class="letter">A</div>
    <div class="content">
      <h3>月次折れ線グラフ</h3>
      <p>連続的なトレンドの把握に最適。季節変動が見やすい</p>
      <div style="height:200px; margin-top:12px;">
        <canvas id="chart-a"></canvas>
      </div>
    </div>
  </div>

  <div class="option" data-choice="b" onclick="toggleSelect(this)">
    <div class="letter">B</div>
    <div class="content">
      <h3>四半期棒グラフ</h3>
      <p>期間ごとの比較に適する。前年同期比が直感的</p>
      <div style="height:200px; margin-top:12px;">
        <canvas id="chart-b"></canvas>
      </div>
    </div>
  </div>

  <div class="option" data-choice="c" onclick="toggleSelect(this)">
    <div class="letter">C</div>
    <div class="content">
      <h3>KPIカード + スパークライン</h3>
      <p>現在値を大きく表示し、推移は補助的に</p>
      <div style="margin-top:12px;">
        <div style="font-size:11px; color:#626264;">売上</div>
        <div style="font-size:28px; font-weight:700;">¥4,230万</div>
        <div style="font-size:11px; color:#197A4B;">▲ 12.3% 前月比</div>
        <div style="height:60px; margin-top:8px;">
          <canvas id="chart-c"></canvas>
        </div>
      </div>
    </div>
  </div>
</div>

<script>
// 実データを使ってChart.jsで描画
const salesData = [3800, 4100, 3950, 4230, ...]; // Phase 1で取得した実データ

new Chart(document.getElementById('chart-a'), {
  type: 'line',
  data: { labels: ['4月', '5月', ...], datasets: [{ data: salesData, borderColor: '#5C5C5C' }] },
  options: { plugins: { legend: { display: false } } }
});
// chart-b, chart-c も同様に描画
</script>
```

### チャートのカラー

モックアップ段階ではグレースケールパレットを使用する。本番のカラーパレットは Phase 3 で適用される:

- Series 1: `#5C5C5C`（CSS変数 `--wf-series-1`）
- Series 2: `#9E9E9E`（CSS変数 `--wf-series-2`）
- Series 3: `#C4C4C4`（CSS変数 `--wf-series-3`）

### KPIカードの比較

KPIカードの表示方法もユーザーに選択させる。「変化率を表示するか」「スパークラインを付けるか」「何と比較するか（前月比/前年比/目標比）」など、表示のバリエーションを見せる。

## Step 3: インタラクション設計の視覚化（探索型）

探索型ダッシュボードの場合、インタラクション要素（フィルタの配置、ドリルダウン構造）もモックアップで見せる。

ここでは `.mockup-body` スコープを使い、本番のダッシュボードクラスでワイヤーフレームを描画する:

```html
<h2>フィルタの配置はどちらが使いやすいですか？</h2>
<p class="subtitle">クリックして選んでください</p>

<div class="options">
  <div class="option" data-choice="a" onclick="toggleSelect(this)">
    <div class="letter">A</div>
    <div class="content">
      <h3>ヘッダー直下に横並び</h3>
      <p>コンパクト。チャート領域を最大化できる</p>
      <div class="mockup" style="margin-top:12px;">
        <div class="mockup-body">
          <div class="dashboard-grid">
            <div class="card chart-full" style="padding:8px;">
              <span class="filter-label">期間</span>
              <select class="filter-select"><option>2024年度</option></select>
              <span class="filter-label" style="margin-left:12px;">部門</span>
              <select class="filter-select"><option>全部門</option></select>
            </div>
            <div class="card kpi-card">売上</div>
            <div class="card kpi-card">利益</div>
            <div class="card kpi-card">顧客数</div>
            <div class="card chart-medium chart-container">月次推移チャート</div>
            <div class="card chart-medium chart-container">構成比チャート</div>
          </div>
        </div>
      </div>
    </div>
  </div>

  <div class="option" data-choice="b" onclick="toggleSelect(this)">
    <div class="letter">B</div>
    <div class="content">
      <h3>左サイドパネル</h3>
      <p>多数のフィルタを収容できる。探索的な操作に向く</p>
      <div class="mockup" style="margin-top:12px;">
        <div class="mockup-body">
          <!-- サイドパネルレイアウト -->
        </div>
      </div>
    </div>
  </div>
</div>
```

## Step 4: レイアウト全体の比較

全体レイアウトの比較にはタブ切り替えを使用する。HTML出力の場合のみ実施。

`.mockup-body` 内で本番のダッシュボードクラスを使い、Step 2で決定したチャートを配置する。

```html
<h2>どのレイアウトが目的に合っていますか？</h2>
<p class="subtitle">タブを切り替えて比較してください。選択はタブ下のボタンで</p>

<div class="tab-bar">
  <button class="active" onclick="switchTab('tab-a', this)">A: KPI横並び型</button>
  <button onclick="switchTab('tab-b', this)">B: サイドフィルタ型</button>
  <button onclick="switchTab('tab-c', this)">C: 2段構成型</button>
</div>

<div id="tab-a" class="tab-panel active">
  <div class="mockup">
    <div class="mockup-header">案A: KPI横並び + フルワイドチャート</div>
    <div class="mockup-body">
      <div class="dashboard-grid">
        <div class="card kpi-card">
          <div class="kpi-label">売上</div>
          <div class="kpi-value">¥4,230万</div>
          <div class="kpi-change positive">▲ 12.3%</div>
        </div>
        <div class="card kpi-card">
          <div class="kpi-label">利益</div>
          <div class="kpi-value">¥890万</div>
          <div class="kpi-change negative">▼ 3.1%</div>
        </div>
        <div class="card kpi-card">
          <div class="kpi-label">顧客数</div>
          <div class="kpi-value">1,247</div>
          <div class="kpi-change positive">▲ 5.8%</div>
        </div>
        <div class="card chart-container chart-full">
          <div class="card-title">月次売上推移</div>
          <canvas id="layout-a-chart"></canvas>
        </div>
      </div>
    </div>
  </div>
  <div style="margin-top:1rem;">
    <div class="option" data-choice="a" onclick="toggleSelect(this)">
      <div class="letter">A</div>
      <div class="content">
        <h3>この案を選ぶ</h3>
        <p>KPIを一目で把握。メインチャートで詳細確認。シンプルな提示型に最適</p>
      </div>
    </div>
  </div>
</div>

<div id="tab-b" class="tab-panel">
  <!-- 案B のモックアップと選択ボタン -->
</div>

<div id="tab-c" class="tab-panel">
  <!-- 案C のモックアップと選択ボタン -->
</div>

<script>
function switchTab(tabId, btn) {
  document.querySelectorAll('.tab-panel').forEach(p => p.classList.remove('active'));
  document.querySelectorAll('.tab-bar button').forEach(b => b.classList.remove('active'));
  document.getElementById(tabId).classList.add('active');
  btn.classList.add('active');
}
</script>
```

## ファイル命名

- `chart-{指標名}.html` — Step 2 の個別チャート比較（例: `chart-sales-trend.html`）
- `interaction-{要素名}.html` — Step 3 のインタラクション比較
- `layout-proposals.html` — Step 4 のレイアウト比較
- 修正版はバージョンサフィックス: `chart-sales-trend-v2.html`
- ファイル名は再利用しない（サーバーは最新ファイルを表示する）

## イベントの読み取り

ユーザーがブラウザでクリックした選択は `$STATE_DIR/events` に記録される:

```jsonl
{"type":"click","choice":"a","text":"A 月次折れ線グラフ","timestamp":1706000101}
```

ターミナルでのユーザー発言と合わせて判断する。ターミナル発言が主、イベントが補助。

ユーザーが「他のパターンも見たい」とターミナルで言った場合は、追加パターンを含む新しいHTMLを生成して表示する。

## 待機画面

Step 間の移行時や、テキスト作業に戻るときにブラウザの古い内容をクリアする:

```html
<!-- filename: waiting.html -->
<div style="display:flex;align-items:center;justify-content:center;min-height:60vh">
  <p class="subtitle">ターミナルで作業を続けています...</p>
</div>
```

## サーバーの停止

Phase 2 完了後（設計承認ゲート通過後）に停止する:

```bash
"$SCRIPTS_DIR/stop-server.sh" "$SESSION_DIR"
```

モックアップファイルは `$PROJECT_DIR/.superpowers/brainstorm/` に残る。
