# レイアウトパターン

Phase 2でレイアウト案を提示する際の基本パターン。
6カラムグリッドとコンポーネントクラスを使用して構成する。

ガイドブック pp.24-25 に基づく4パターンを収録。

これらのパターンは出発点であり、制約ではない。ユーザーの目的に最も合うパターンを選び、必要に応じてパターンを組み合わせたり、要素の配置を変更する。パターンに無理に当てはめるのではなく、パターンが提供する構造的な知恵を活かす。

## 共通構造

すべてのパターンで共有するHTMLシェルとCSS。各パターンでは `<div class="dashboard-grid">` 内のみを記載する。

```html
<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>ダッシュボード</title>
  <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
  <style>
    /* ===== リセット ===== */
    *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
    body {
      font-family: "Noto Sans JP", sans-serif;
      background: var(--color-bg-standard);
      color: var(--color-text-black);
    }

    /* ===== CSS変数（Blue デフォルト） ===== */
    :root {
      --color-text-black: #000000;
      --color-text-white: #FFFFFF;
      --color-text-label: #626264;
      --color-text-link: #0017C1;
      --color-bg-standard: #F8F8FB;
      --color-bg-card: #FFFFFF;
      --color-bg-highlight: #0017C1;
      --color-bg-control: #F1F1F4;
      --color-bg-border: #D2D2D8;
      --color-chart-primary-1200: #000060;
      --color-chart-primary-900: #0017C1;
      --color-chart-primary-600: #3460FB;
      --color-chart-primary-400: #7096F8;
      --color-chart-primary-200: #C5D7FB;
      --color-chart-primary-50: #D9E6FF;
      --color-chart-secondary-1200: #A58000;
      --color-chart-secondary-900: #D2A400;
      --color-chart-secondary-600: #FFC700;
      --color-chart-neutral-900: #3D3D3F;
      --color-chart-neutral-600: #626264;
      --color-chart-neutral-400: #93939B;
      --color-chart-neutral-200: #D2D2D8;
      --color-semantic-positive: #3460FB;
      --color-semantic-positive-200: #C5D7FB;
      --color-semantic-positive-50: #D9E6FF;
      --color-semantic-negative: #FE3939;
      --color-semantic-negative-200: #FE9E9E;
      --color-semantic-negative-50: #FFDEDE;
      --color-semantic-success: #197A4B;
      --color-semantic-error: #CE0000;
    }

    /* ===== グリッドレイアウト ===== */
    .dashboard-grid {
      display: grid;
      grid-template-columns: repeat(6, 1fr);
      gap: 16px;
      padding: 24px;
      max-width: 1920px;
      margin: 0 auto;
      background: var(--color-bg-standard);
    }

    /* ===== 共通カード ===== */
    .card {
      background: var(--color-bg-card);
      border-radius: 8px;
      padding: 20px;
      box-shadow: 0 1px 3px rgba(0,0,0,0.08);
    }

    /* ===== スパンユーティリティ ===== */
    .kpi-card     { grid-column: span 1; }
    .chart-small  { grid-column: span 2; }
    .chart-medium { grid-column: span 3; }
    .chart-large  { grid-column: span 4; }
    .chart-full   { grid-column: span 6; }

    /* ===== ヘッダー・フッター ===== */
    .dashboard-header {
      grid-column: span 6;
      display: flex;
      justify-content: space-between;
      align-items: center;
      padding: 16px 0;
    }
    .dashboard-header h1 { font-size: 24px; font-weight: 700; }

    .dashboard-footer {
      grid-column: span 6;
      font-size: 12px;
      color: var(--color-text-label);
      padding: 12px 0;
      border-top: 1px solid var(--color-bg-border);
    }

    /* ===== KPIカード ===== */
    .kpi-label  { font-size: 14px; color: var(--color-text-label); margin-bottom: 4px; }
    .kpi-value  { font-size: 36px; font-weight: 700; line-height: 1.2; }
    .kpi-change { font-size: 14px; font-weight: 600; margin-top: 4px; }
    .kpi-change.positive { color: var(--color-semantic-positive); }
    .kpi-change.negative { color: var(--color-semantic-negative); }

    /* ===== チャートコンテナ ===== */
    .chart-container {
      background: var(--color-bg-card);
      border-radius: 8px;
      padding: 20px;
      box-shadow: 0 1px 3px rgba(0,0,0,0.08);
    }
    .chart-container h3 {
      font-size: 16px;
      font-weight: 600;
      margin-bottom: 12px;
    }
    .chart-container canvas { width: 100% !important; }

    /* ===== フィルタパネル ===== */
    .filter-panel {
      background: var(--color-bg-card);
      border-radius: 8px;
      padding: 20px;
      box-shadow: 0 1px 3px rgba(0,0,0,0.08);
    }
    .filter-panel h3 {
      font-size: 14px;
      font-weight: 600;
      margin-bottom: 12px;
    }
    .filter-panel select,
    .filter-panel input {
      width: 100%;
      padding: 8px;
      border: 1px solid var(--color-bg-border);
      border-radius: 4px;
      background: var(--color-bg-control);
      font-size: 14px;
      margin-bottom: 8px;
    }

    /* ===== テーブル ===== */
    .data-table {
      width: 100%;
      border-collapse: collapse;
    }
    .data-table th {
      text-align: left;
      font-size: 14px;
      font-weight: 600;
      padding: 10px 12px;
      border-bottom: 2px solid var(--color-bg-border);
    }
    .data-table td {
      font-size: 14px;
      padding: 10px 12px;
      border-bottom: 1px solid var(--color-bg-border);
    }
    .data-table td.numeric { text-align: right; font-variant-numeric: tabular-nums; }

    /* ===== レスポンシブ ===== */
    @media (max-width: 1024px) {
      .dashboard-grid { grid-template-columns: repeat(3, 1fr); padding: 16px; }
      .chart-large  { grid-column: span 3; }
      .chart-medium { grid-column: span 3; }
      .chart-full   { grid-column: span 3; }
      .kpi-card     { grid-column: span 1; }
    }
    @media (max-width: 640px) {
      .dashboard-grid { grid-template-columns: 1fr; padding: 12px; }
      .chart-large, .chart-medium, .chart-small,
      .chart-full, .kpi-card { grid-column: span 1; }
    }
  </style>
</head>
<body>
  <div class="dashboard-grid">
    <!-- パターンごとの内容をここに配置 -->
  </div>
</body>
</html>
```

---

## パターン1: 単一指標の多角的分析

**用途**: 一つの重要指標を構成比、時系列、地域別など複数の角度から掘り下げる

### ASCIIレイアウト図

```
┌──────────────────────────────────────────┐
│ ヘッダー: タイトル ＋ 更新日時              │  6col
├──────┬────────────┬──────┤
│ KPI値 │            │フィルタ│
│ (大)  │  時系列     │ 地域  │
│ 2col  │ 折れ線グラフ │ 選択  │
│      │   (3col)   │ 1col  │
│ドーナツ│            │      │
│構成比  │            │      │
├──────┼────────────┼──────┤
│      │  内訳       │ 地域別 │
│      │ 棒グラフ    │ 棒グラフ│
│      │   (3col)   │ 2col  │
├──────┴────────────┴──────┤
│ フッター: 最終更新日・出典                  │  6col
└──────────────────────────────────────────┘
```

### HTML構造

```html
<!-- ===== パターン1: 単一指標の多角的分析 ===== -->

<!-- ヘッダー -->
<header class="dashboard-header">
  <h1>交通事故発生状況</h1>
  <span class="dashboard-footer">最終更新: 2024年3月1日</span>
</header>

<!-- KPI（大）+ ドーナツチャート: 左 2col -->
<div class="card chart-small" style="grid-row: span 2;">
  <div class="kpi-label">交通事故件数（累計）</div>
  <div class="kpi-value">12,345</div>
  <div class="kpi-change negative">▲ 3.2% 前年比</div>
  <div class="mt-20">
    <canvas id="donut-composition"></canvas>
    <!-- Chart.js ドーナツチャートで構成比を表示 -->
  </div>
</div>

<!-- 時系列チャート: 中央 3col -->
<div class="chart-container chart-medium">
  <h3>月別推移</h3>
  <canvas id="timeseries-line"></canvas>
</div>

<!-- フィルタパネル: 右 1col -->
<div class="filter-panel kpi-card">
  <h3>絞り込み</h3>
  <label class="kpi-label" for="filter-region">地域</label>
  <select id="filter-region">
    <option>全国</option>
    <option>北海道</option>
    <option>東北</option>
  </select>
  <label class="kpi-label" for="filter-year">年度</label>
  <select id="filter-year">
    <option>2024</option>
    <option>2023</option>
  </select>
</div>

<!-- 内訳棒グラフ: 中央下 3col -->
<div class="chart-container chart-medium">
  <h3>事故種別内訳</h3>
  <canvas id="breakdown-bar"></canvas>
</div>

<!-- 地域別棒グラフ: 右下 2col -->
<div class="chart-container chart-small">
  <h3>地域別件数</h3>
  <canvas id="region-bar"></canvas>
</div>

<!-- フッター -->
<footer class="dashboard-footer">
  出典: 警察庁「交通事故統計」 | データ定義: 発生件数は人身事故のみ
</footer>
```

---

## パターン2: 複数指標の並列比較

**用途**: 複数の指標を横並びで比較し、全体像を把握する

### ASCIIレイアウト図

```
┌──────────────────────────────────────────┐
│ ヘッダー: タイトル ＋ フィルタ（右寄せ）      │  6col
├──────┬──────┬──────┬──────┤
│ KPI A │ KPI B │ KPI C │フィルタ│
│ 指標A │ 指標B │ 指標C │      │
│ 2col  │ 2col  │ 1col  │ 1col │
├──────┼──────┤      │      │
│内訳値A│内訳値B│ 内訳C │      │
│ 2col  │ 2col  │ 1col  │      │
├──────┴──────┼──────┴──────┤
│ 指標A内訳    │ 指標C推移     │
│ 棒グラフ     │ 折れ線グラフ   │
│  (3col)     │  (3col)      │
├─────────────┴──────────────┤
│ フッター: 最終更新日・出典                  │  6col
└──────────────────────────────────────────┘
```

### HTML構造

```html
<!-- ===== パターン2: 複数指標の並列比較 ===== -->

<!-- ヘッダー -->
<header class="dashboard-header">
  <h1>主要業績指標（KPI）ダッシュボード</h1>
  <span class="kpi-label">2024年度 第3四半期</span>
</header>

<!-- KPIカード行: 指標A -->
<div class="card kpi-card chart-small">
  <div class="kpi-label">指標A: 売上高</div>
  <div class="kpi-value">¥850M</div>
  <div class="kpi-change positive">▲ 12.3% 前年比</div>
</div>

<!-- KPIカード行: 指標B -->
<div class="card kpi-card chart-small">
  <div class="kpi-label">指標B: 利用者数</div>
  <div class="kpi-value">24.5万</div>
  <div class="kpi-change positive">▲ 5.1% 前年比</div>
</div>

<!-- KPIカード行: 指標C -->
<div class="card kpi-card">
  <div class="kpi-label">指標C: 満足度</div>
  <div class="kpi-value">78%</div>
  <div class="kpi-change negative">▼ 2.0pt</div>
</div>

<!-- フィルタパネル: 右 1col -->
<div class="filter-panel kpi-card" style="grid-row: span 2;">
  <h3>絞り込み</h3>
  <label class="kpi-label" for="filter-period">期間</label>
  <select id="filter-period">
    <option>2024 Q3</option>
    <option>2024 Q2</option>
    <option>2024 Q1</option>
  </select>
  <label class="kpi-label" for="filter-division">部門</label>
  <select id="filter-division">
    <option>全部門</option>
    <option>営業部</option>
    <option>開発部</option>
  </select>
</div>

<!-- 内訳行: 指標A内訳 -->
<div class="card kpi-card chart-small">
  <div class="kpi-label">国内売上</div>
  <div class="kpi-value kpi-value--xsmall">¥620M</div>
  <div class="kpi-label mt-8">海外売上</div>
  <div class="kpi-value kpi-value--xsmall">¥230M</div>
</div>

<!-- 内訳行: 指標B内訳 -->
<div class="card kpi-card chart-small">
  <div class="kpi-label">新規利用者</div>
  <div class="kpi-value kpi-value--xsmall">8.2万</div>
  <div class="kpi-label mt-8">継続利用者</div>
  <div class="kpi-value kpi-value--xsmall">16.3万</div>
</div>

<!-- 内訳行: 指標C内訳 -->
<div class="card kpi-card">
  <div class="kpi-label">サービス</div>
  <div class="kpi-value kpi-value--xsmall">82%</div>
  <div class="kpi-label mt-8">サポート</div>
  <div class="kpi-value kpi-value--xsmall">71%</div>
</div>

<!-- 指標A 内訳棒グラフ: 左 3col -->
<div class="chart-container chart-medium">
  <h3>売上高の部門別内訳</h3>
  <canvas id="breakdown-a-bar"></canvas>
</div>

<!-- 指標C 時系列折れ線グラフ: 右 3col -->
<div class="chart-container chart-medium">
  <h3>満足度の推移</h3>
  <canvas id="timeseries-c-line"></canvas>
</div>

<!-- フッター -->
<footer class="dashboard-footer">
  出典: 社内集計システム | 最終更新: 2024年12月15日
</footer>
```

---

## パターン3: フィルタ重視型

**用途**: フィルタで絞り込みながら分析する探索的なダッシュボード

### ASCIIレイアウト図

```
┌──────┬──────────────────────────┐
│      │ ヘッダー: タイトル           │  header 4col
│      ├──────┬──────┬──────┤
│フィルタ│ KPI値 │ KPI値 │ KPI値 │
│サイド  │ (大)  │      │      │
│バー   │ 各カード 各1col         │
│      ├──────┴──────────────┤
│ 2col  │ 時系列チャート             │
│      │ 折れ線グラフ (4col)        │
│      ├──────────────────────┤
│      │ 内訳折れ線グラフ            │
│      │ (4col)                   │
│      ├──────────────────────┤
│      │ フッター                   │  4col
└──────┴──────────────────────────┘
```

### HTML構造

```html
<!-- ===== パターン3: フィルタ重視型 ===== -->

<!-- フィルタサイドバー: 左 2col、全行にわたる -->
<div class="filter-panel" style="grid-column: span 2; grid-row: 1 / -1;">
  <h3>フィルタ</h3>

  <label class="kpi-label" for="filter-category">カテゴリ</label>
  <select id="filter-category">
    <option>すべて</option>
    <option>カテゴリA</option>
    <option>カテゴリB</option>
  </select>

  <label class="kpi-label" for="filter-region">地域</label>
  <select id="filter-region">
    <option>全国</option>
    <option>関東</option>
    <option>関西</option>
    <option>中部</option>
  </select>

  <label class="kpi-label" for="filter-date-from">期間（開始）</label>
  <input type="date" id="filter-date-from" value="2024-01-01">

  <label class="kpi-label" for="filter-date-to">期間（終了）</label>
  <input type="date" id="filter-date-to" value="2024-12-31">

  <label class="kpi-label" for="filter-status">ステータス</label>
  <select id="filter-status">
    <option>すべて</option>
    <option>完了</option>
    <option>進行中</option>
    <option>未着手</option>
  </select>

  <button class="filter-button mt-12">適用</button>
</div>

<!-- ヘッダー: 右 4col -->
<header class="dashboard-header chart-large">
  <h1>申請状況ダッシュボード</h1>
  <span class="kpi-label">最終更新: 2024年12月15日</span>
</header>

<!-- KPIカード: 右側に3枚、各1col（残り1colは余白） -->
<div class="card kpi-card">
  <div class="kpi-label">総申請件数</div>
  <div class="kpi-value kpi-value--small">5,280</div>
  <div class="kpi-change positive">▲ 15.2%</div>
</div>

<div class="card kpi-card">
  <div class="kpi-label">処理完了率</div>
  <div class="kpi-value kpi-value--small">87.3%</div>
  <div class="kpi-change positive">▲ 4.1pt</div>
</div>

<div class="card kpi-card">
  <div class="kpi-label">平均処理日数</div>
  <div class="kpi-value kpi-value--small">3.2日</div>
  <div class="kpi-change positive">▼ 0.8日</div>
</div>

<!-- 空セル（レイアウト調整用） -->
<div style="grid-column: span 1;"></div>

<!-- 時系列チャート: 右 4col -->
<div class="chart-container chart-large">
  <h3>月別申請件数の推移</h3>
  <canvas id="timeseries-applications"></canvas>
</div>

<!-- 内訳チャート: 右 4col -->
<div class="chart-container chart-large">
  <h3>カテゴリ別推移</h3>
  <canvas id="breakdown-line"></canvas>
</div>

<!-- フッター: 右 4col -->
<footer class="dashboard-footer chart-large">
  出典: 申請管理システム | データ定義: 処理完了は最終承認済みの案件
</footer>
```

---

## パターン4: テーブル一覧型

**用途**: 詳細な数値データを一覧で確認する

### ASCIIレイアウト図

```
┌──────────────────────────────────────────┐
│ ヘッダー: タイトル ＋ フィルタ（右寄せ）      │  6col
├──────┬──────┬──────┬──────┤
│ KPI  │ KPI  │ KPI  │フィルタ│
│ 1col │ 1col │ 1col │      │
│      │      │      │ 1col │
├──────┴──────┴──────┼──────┤
│                    │ 小    │
│  テーブル（メイン）   │折れ線  │
│                    │グラフ  │
│  (4col)            │ 2col  │
│                    │      │
├────────────────────┤      │
│ フィルタ操作          │      │
│ (4col)             │      │
├────────────────────┴──────┤
│ フッター: 最終更新日・出典                  │  6col
└──────────────────────────────────────────┘
```

### HTML構造

```html
<!-- ===== パターン4: テーブル一覧型 ===== -->

<!-- ヘッダー -->
<header class="dashboard-header">
  <h1>予算執行状況一覧</h1>
  <div class="header-filters">
    <select class="filter-select">
      <option>2024年度</option>
      <option>2023年度</option>
    </select>
    <select class="filter-select">
      <option>全部局</option>
      <option>総務部</option>
      <option>企画部</option>
    </select>
  </div>
</header>

<!-- KPIカード行: 3枚 -->
<div class="card kpi-card">
  <div class="kpi-label">予算総額</div>
  <div class="kpi-value kpi-value--compact">¥4.2B</div>
</div>

<div class="card kpi-card">
  <div class="kpi-label">執行済額</div>
  <div class="kpi-value kpi-value--compact">¥3.1B</div>
  <div class="kpi-change positive">73.8%</div>
</div>

<div class="card kpi-card">
  <div class="kpi-label">未執行額</div>
  <div class="kpi-value kpi-value--compact">¥1.1B</div>
</div>

<!-- KPI行の残り 3col は空（テーブルとサイドチャートの開始位置合わせ） -->
<div style="grid-column: span 3;"></div>

<!-- テーブル: メインエリア 4col -->
<div class="card" style="grid-column: span 4; grid-row: span 2; overflow-x: auto;">
  <h3 class="card-title">部局別予算執行状況</h3>
  <table class="data-table">
    <thead>
      <tr>
        <th>部局名</th>
        <th class="text-right">予算額</th>
        <th class="text-right">執行額</th>
        <th class="text-right">執行率</th>
        <th class="text-right">前年比</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td>総務部</td>
        <td class="numeric">¥820M</td>
        <td class="numeric">¥650M</td>
        <td class="numeric">79.3%</td>
        <td class="numeric cell-positive">▲ 5.2%</td>
      </tr>
      <tr>
        <td>企画部</td>
        <td class="numeric">¥1,200M</td>
        <td class="numeric">¥880M</td>
        <td class="numeric">73.3%</td>
        <td class="numeric cell-negative">▼ 1.8%</td>
      </tr>
      <tr>
        <td>情報システム部</td>
        <td class="numeric">¥950M</td>
        <td class="numeric">¥720M</td>
        <td class="numeric">75.8%</td>
        <td class="numeric cell-positive">▲ 8.4%</td>
      </tr>
      <!-- 追加行... -->
    </tbody>
  </table>
</div>

<!-- サイドチャート: 右 2col -->
<div class="chart-container chart-small" style="grid-row: span 2;">
  <h3>執行率の推移</h3>
  <canvas id="execution-rate-line"></canvas>
</div>

<!-- フィルタ操作行: 下部 4col -->
<div class="card chart-large" style="display: flex; gap: 12px; align-items: center; padding: 12px 20px;">
  <span class="kpi-label">表示件数:</span>
  <select class="filter-select">
    <option>20件</option>
    <option>50件</option>
    <option>100件</option>
  </select>
  <span class="kpi-label ml-auto">1-20 / 45件</span>
</div>

<!-- フッター -->
<footer class="dashboard-footer">
  出典: 財務管理システム | 最終更新: 2024年12月1日
</footer>
```

---

## アスペクト比

### 16:9（デフォルト: 1920x1080）

共通構造のCSS（`max-width: 1920px`、6カラムグリッド）がそのまま適用される。追加CSSは不要。

### 4:3（1440x1080）

4:3の表示環境（プロジェクタ等）で使用する場合の上書きCSS。
カラム数を4に減らし、横幅を狭くする。

```css
/* ===== 4:3 アスペクト比対応 ===== */
.dashboard-grid {
  max-width: 1440px;
  grid-template-columns: repeat(4, 1fr);
  gap: 12px;
  padding: 20px;
}

/* スパンの上限を4に制限 */
.chart-full   { grid-column: span 4; }
.chart-large  { grid-column: span 3; }
.chart-medium { grid-column: span 2; }
.chart-small  { grid-column: span 2; }
.kpi-card     { grid-column: span 1; }

/* ヘッダー・フッターも4col */
.dashboard-header { grid-column: span 4; }
.dashboard-footer { grid-column: span 4; }

/* KPI値のフォントサイズを縮小 */
.kpi-value { font-size: 28px; }

/* テーブルのフォントサイズを縮小 */
.data-table th,
.data-table td { font-size: 13px; padding: 8px 10px; }

/* レスポンシブ上書き */
@media (max-width: 768px) {
  .dashboard-grid { grid-template-columns: repeat(2, 1fr); }
  .chart-large, .chart-medium, .chart-full { grid-column: span 2; }
}
@media (max-width: 480px) {
  .dashboard-grid { grid-template-columns: 1fr; }
  .chart-large, .chart-medium, .chart-small,
  .chart-full, .kpi-card { grid-column: span 1; }
}
```

**パターン別の4:3適用時の注意点:**

| パターン | 6col時 | 4col時の変更 |
|---------|--------|------------|
| パターン1 | KPI(2) + チャート(3) + フィルタ(1) | KPI(1) + チャート(2) + フィルタ(1) |
| パターン2 | KPI×3(2+2+1) + フィルタ(1) | KPI×3(1+1+1) + フィルタ(1) |
| パターン3 | フィルタ(2) + コンテンツ(4) | フィルタ(1) + コンテンツ(3) |
| パターン4 | テーブル(4) + チャート(2) | テーブル(3) + チャート(1)、またはテーブル(4)全幅 |
