# コンポーネントライブラリ

ダッシュボードを構成するアトミックなパーツ。すべてのコンポーネントは `design-system.md` のCSS変数を使用する。

これらは材料であり、完成品ではない。目的に応じてパーツを選び、組み合わせ、調整する。そのまま貼り付けて終わりにしない。同時に、カラーパレットやタイポグラフィの仕様を無視した独自の値をハードコードしない。

> **Chart.jsとCSS変数**: Chart.jsはCSS変数を直接解釈できない。チャート初期化コード内の色指定には `chart-defaults.md` で定義された `getCSSVar()` ヘルパーを使用する。本ファイルのコード例では、可読性のためCSS変数名をコメントで併記し、実際の値は `getCSSVar()` で取得する形式で記述する。

> **探索型のインタラクションコンポーネント**: ドリルダウン、クロスフィルタリング、タブナビゲーション等の探索型ダッシュボード向けコンポーネントは `references/interaction-patterns.md` を参照。

---

## 共通スタイル

すべてのカード系コンポーネントが継承するベーススタイル。

```css
.card {
  background: var(--color-bg-card);
  border: 1px solid var(--color-bg-border);
  border-radius: 8px;
  padding: 20px;
  box-sizing: border-box;
}

.card-title {
  font-size: 16px;
  font-weight: 600;
  color: var(--color-text-black);
  margin: 0 0 12px 0;
}

.card-label {
  font-size: 14px;
  font-weight: 400;
  color: var(--color-text-label);
}

/* スペーシングユーティリティ */
.mt-4 { margin-top: 4px; }
.mt-8 { margin-top: 8px; }
.mt-12 { margin-top: 12px; }
.mt-16 { margin-top: 16px; }
.mt-20 { margin-top: 20px; }
.ml-auto { margin-left: auto; }
```

---

## ヘッダー

ダッシュボードのタイトルバー。タイトル（左）、組織名・ロゴエリア（右）、更新日時を含む。

```html
<header class="dashboard-header">
  <div class="header-left">
    <h1 class="header-title">{{ダッシュボードタイトル}}</h1>
    <span class="header-date">最終更新: {{YYYY年MM月DD日}}</span>
  </div>
  <div class="header-right">
    <span class="header-org">{{組織名}}</span>
  </div>
</header>
```

```css
.dashboard-header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 16px 24px;
  background: var(--color-bg-highlight);
  color: var(--color-text-white);
  border-radius: 8px 8px 0 0;
}

.header-title {
  font-size: 20px;
  font-weight: 700;
  margin: 0;
  color: var(--color-text-white);
}

.header-date {
  font-size: 12px;
  font-weight: 400;
  color: var(--color-text-white);
  opacity: 0.8;
  margin-top: 4px;
  display: block;
}

.header-right {
  display: flex;
  align-items: center;
  gap: 12px;
}

.header-org {
  font-size: 14px;
  font-weight: 600;
  color: var(--color-text-white);
}
```

### ヘッダー内フィルタ行

ヘッダーにフィルタコントロールを配置する場合に使用。タイトルの右側にフィルタを横並びで配置する。

```html
<header class="dashboard-header">
  <h1 class="header-title">{{ダッシュボードタイトル}}</h1>
  <div class="header-filters">
    <select class="filter-select"><!-- 選択肢 --></select>
    <select class="filter-select"><!-- 選択肢 --></select>
  </div>
</header>
```

```css
.header-filters {
  display: flex;
  gap: 8px;
  align-items: center;
}

.header-filters .filter-select {
  background: rgba(255, 255, 255, 0.15);
  color: var(--color-text-white);
  border-color: rgba(255, 255, 255, 0.3);
}
```

---

## KPIカード

### 大サイズ（span 2-3 columns）

主要指標を大きく表示する。ラベル + 大きな数値 + 変化インジケーター（矢印+割合）。

```html
<div class="card kpi-card kpi-large">
  <span class="kpi-label">{{指標名}}</span>
  <div class="kpi-value-row">
    <span class="kpi-value">{{12,345}}</span>
    <span class="kpi-unit">{{件}}</span>
  </div>
  <div class="kpi-change kpi-change--positive">
    <span class="kpi-change-arrow">&#9650;</span>
    <span class="kpi-change-value">+12.3%</span>
    <span class="kpi-change-period">前年比</span>
  </div>
</div>
```

```css
.kpi-card {
  display: flex;
  flex-direction: column;
  gap: 4px;
}

.kpi-large {
  min-width: 240px;
  padding: 20px 24px;
}

.kpi-label {
  font-size: 14px;
  font-weight: 400;
  color: var(--color-text-label);
}

.kpi-value-row {
  display: flex;
  align-items: baseline;
  gap: 4px;
}

.kpi-value {
  font-size: 40px;
  font-weight: 700;
  color: var(--color-text-black);
  line-height: 1.2;
}

.kpi-unit {
  font-size: 16px;
  font-weight: 400;
  color: var(--color-text-label);
}

.kpi-change {
  display: flex;
  align-items: center;
  gap: 4px;
  font-size: 14px;
  font-weight: 600;
}

.kpi-change--positive {
  color: var(--color-semantic-positive);
}

.kpi-change--positive .kpi-change-arrow {
  color: var(--color-semantic-positive);
}

.kpi-change--negative {
  color: var(--color-semantic-negative);
}

.kpi-change--negative .kpi-change-arrow {
  color: var(--color-semantic-negative);
}

.kpi-change-period {
  font-size: 12px;
  font-weight: 400;
  color: var(--color-text-label);
  margin-left: 4px;
}
```

### 小サイズ（span 1 column）

コンパクトなKPI表示。サイドバーやサブ指標に使用。

```html
<div class="card kpi-card kpi-small">
  <span class="kpi-label">{{指標名}}</span>
  <div class="kpi-value-row">
    <span class="kpi-value kpi-value--small">{{1,234}}</span>
    <span class="kpi-unit">{{件}}</span>
  </div>
  <div class="kpi-change kpi-change--negative">
    <span class="kpi-change-arrow">&#9660;</span>
    <span class="kpi-change-value">-3.2%</span>
    <span class="kpi-change-period">前月比</span>
  </div>
</div>
```

```css
.kpi-small {
  min-width: 160px;
  padding: 16px 20px;
}

.kpi-value--small {
  font-size: 32px;
}

.kpi-value--compact {
  font-size: 28px;
}

.kpi-value--xsmall {
  font-size: 20px;
}
```

### ハイライトバリアント

背景をキーカラーにし、最も重要な指標を強調表示する。ダッシュボード左上に配置する場合に使用。

```html
<div class="card kpi-card kpi-large kpi-highlight">
  <span class="kpi-label">{{指標名}}</span>
  <div class="kpi-value-row">
    <span class="kpi-value">{{10,000,000}}</span>
    <span class="kpi-unit">{{件}}</span>
  </div>
  <div class="kpi-change kpi-change--positive-inv">
    <span class="kpi-change-arrow">&#9650;</span>
    <span class="kpi-change-value">+8.2%</span>
    <span class="kpi-change-period">前年比</span>
  </div>
</div>
```

```css
.kpi-highlight {
  background: var(--color-bg-highlight);
}

.kpi-highlight .kpi-label,
.kpi-highlight .kpi-value,
.kpi-highlight .kpi-unit {
  color: var(--color-text-white);
}

.kpi-change--positive-inv {
  color: var(--color-text-white);
}

.kpi-change--positive-inv .kpi-change-arrow {
  color: var(--color-text-white);
}

.kpi-highlight .kpi-change-period {
  color: var(--color-text-white);
  opacity: 0.8;
}
```

### 割合バー付きバリアント

達成率や構成比を視覚的に示すプログレスバーを含むKPIカード。

```html
<div class="card kpi-card kpi-small">
  <span class="kpi-label">{{指標名の割合}}</span>
  <div class="kpi-value-row">
    <span class="kpi-value kpi-value--small">80%</span>
  </div>
  <div class="kpi-progress">
    <div class="kpi-progress-bar" style="width: 80%"></div>
  </div>
</div>
```

```css
.kpi-progress {
  width: 100%;
  height: 6px;
  background: var(--color-bg-control);
  border-radius: 3px;
  margin-top: 8px;
  overflow: hidden;
}

.kpi-progress-bar {
  height: 100%;
  background: var(--color-chart-primary-600);
  border-radius: 3px;
  transition: width 0.3s ease;
}
```

---

## フィルター

### ドロップダウン

```html
<div class="filter-group">
  <label class="filter-label" for="filter-dropdown">{{ラベル}}</label>
  <div class="filter-select-wrapper">
    <select class="filter-select" id="filter-dropdown">
      <option value="">選択してください</option>
      <option value="1">{{選択肢1}}</option>
      <option value="2">{{選択肢2}}</option>
      <option value="3">{{選択肢3}}</option>
    </select>
  </div>
</div>
```

```css
.filter-group {
  display: flex;
  flex-direction: column;
  gap: 6px;
}

.filter-label {
  font-size: 14px;
  font-weight: 600;
  color: var(--color-text-black);
}

.filter-select-wrapper {
  position: relative;
}

.filter-select {
  width: 100%;
  padding: 8px 32px 8px 12px;
  font-size: 14px;
  color: var(--color-text-black);
  background: var(--color-bg-card);
  border: 1px solid var(--color-bg-border);
  border-radius: 6px;
  appearance: none;
  cursor: pointer;
}

.filter-select:focus {
  outline: 2px solid var(--color-chart-primary-600);
  outline-offset: -1px;
}

.filter-select-wrapper::after {
  content: "";
  position: absolute;
  right: 12px;
  top: 50%;
  transform: translateY(-50%);
  width: 0;
  height: 0;
  border-left: 5px solid transparent;
  border-right: 5px solid transparent;
  border-top: 5px solid var(--color-text-label);
  pointer-events: none;
}
```

### 検索ボックス

```html
<div class="filter-group">
  <label class="filter-label" for="filter-search">{{ラベル}}</label>
  <input
    class="filter-search"
    type="text"
    id="filter-search"
    placeholder="キーワードを入力..."
  />
</div>
```

```css
.filter-search {
  width: 100%;
  padding: 8px 12px;
  font-size: 14px;
  color: var(--color-text-black);
  background: var(--color-bg-card);
  border: 1px solid var(--color-bg-border);
  border-radius: 6px;
  box-sizing: border-box;
}

.filter-search::placeholder {
  color: var(--color-chart-neutral-400);
}

.filter-search:focus {
  outline: 2px solid var(--color-chart-primary-600);
  outline-offset: -1px;
}
```

### チェックボックスリスト

```html
<div class="filter-group">
  <span class="filter-label">{{ラベル}}</span>
  <div class="filter-checkbox-list">
    <label class="filter-checkbox-item">
      <input type="checkbox" name="{{group}}" value="1" />
      <span class="filter-checkbox-text">{{選択肢1}}</span>
    </label>
    <label class="filter-checkbox-item">
      <input type="checkbox" name="{{group}}" value="2" />
      <span class="filter-checkbox-text">{{選択肢2}}</span>
    </label>
    <label class="filter-checkbox-item">
      <input type="checkbox" name="{{group}}" value="3" />
      <span class="filter-checkbox-text">{{選択肢3}}</span>
    </label>
  </div>
</div>
```

```css
.filter-checkbox-list {
  display: flex;
  flex-direction: column;
  gap: 8px;
}

.filter-checkbox-item {
  display: flex;
  align-items: center;
  gap: 8px;
  cursor: pointer;
  font-size: 14px;
  color: var(--color-text-black);
}

.filter-checkbox-item input[type="checkbox"] {
  width: 16px;
  height: 16px;
  accent-color: var(--color-chart-primary-600);
  cursor: pointer;
}

.filter-checkbox-text {
  font-size: 14px;
  color: var(--color-text-black);
}
```

### ラジオボタンリスト

```html
<div class="filter-group">
  <span class="filter-label">{{ラベル}}</span>
  <div class="filter-radio-list">
    <label class="filter-radio-item">
      <input type="radio" name="{{group}}" value="1" checked />
      <span class="filter-radio-text">{{選択肢1}}</span>
    </label>
    <label class="filter-radio-item">
      <input type="radio" name="{{group}}" value="2" />
      <span class="filter-radio-text">{{選択肢2}}</span>
    </label>
    <label class="filter-radio-item">
      <input type="radio" name="{{group}}" value="3" />
      <span class="filter-radio-text">{{選択肢3}}</span>
    </label>
  </div>
</div>
```

```css
.filter-radio-list {
  display: flex;
  flex-direction: column;
  gap: 8px;
}

.filter-radio-item {
  display: flex;
  align-items: center;
  gap: 8px;
  cursor: pointer;
  font-size: 14px;
  color: var(--color-text-black);
}

.filter-radio-item input[type="radio"] {
  width: 16px;
  height: 16px;
  accent-color: var(--color-chart-primary-600);
  cursor: pointer;
}

.filter-radio-text {
  font-size: 14px;
  color: var(--color-text-black);
}
```

### 日付範囲スライダー

```html
<div class="filter-group">
  <span class="filter-label">{{ラベル}}</span>
  <div class="filter-date-range">
    <input
      class="filter-date-input"
      type="date"
      id="date-start"
      value="{{YYYY-MM-DD}}"
    />
    <span class="filter-date-separator">~</span>
    <input
      class="filter-date-input"
      type="date"
      id="date-end"
      value="{{YYYY-MM-DD}}"
    />
  </div>
</div>
```

```css
.filter-date-range {
  display: flex;
  align-items: center;
  gap: 8px;
}

.filter-date-input {
  padding: 8px 12px;
  font-size: 14px;
  color: var(--color-text-black);
  background: var(--color-bg-card);
  border: 1px solid var(--color-bg-border);
  border-radius: 6px;
  flex: 1;
}

.filter-date-input:focus {
  outline: 2px solid var(--color-chart-primary-600);
  outline-offset: -1px;
}

.filter-date-separator {
  font-size: 14px;
  color: var(--color-text-label);
}
```

### フィルタ適用ボタン

フィルタパネル内で適用・リセット操作に使用するボタン。

```html
<button class="filter-button">適用</button>
<button class="filter-button filter-button--secondary">リセット</button>
```

```css
.filter-button {
  width: 100%;
  padding: 8px 16px;
  border: none;
  border-radius: 6px;
  font-size: 14px;
  font-weight: 600;
  cursor: pointer;
  background: var(--color-chart-primary-600);
  color: var(--color-text-white);
}

.filter-button:hover {
  background: var(--color-chart-primary-900);
}

.filter-button--secondary {
  background: var(--color-bg-control);
  color: var(--color-text-label);
}

.filter-button--secondary:hover {
  background: var(--color-bg-border);
  color: var(--color-text-black);
}
```

---

## チャートコンテナ

すべてのチャートが共有するラッパー。チャートタイトル + canvas + 凡例エリア + 出典注記で構成。

```html
<div class="card chart-container">
  <h3 class="chart-title">{{チャートタイトル}}</h3>
  <div class="chart-canvas-wrapper">
    <canvas id="chart-{{id}}"></canvas>
  </div>
  <div class="chart-legend" id="legend-{{id}}">
    <!-- Chart.jsのhtmlLegendPluginで生成、またはカスタムHTML -->
  </div>
  <p class="chart-source">出典: {{データソース名}}</p>
</div>
```

```css
.chart-container {
  display: flex;
  flex-direction: column;
  gap: 12px;
}

.chart-title {
  font-size: 16px;
  font-weight: 600;
  color: var(--color-text-black);
  margin: 0;
}

.chart-canvas-wrapper {
  position: relative;
  width: 100%;
  /* アスペクト比を維持。必要に応じて高さを固定 */
}

.chart-legend {
  display: flex;
  flex-wrap: wrap;
  gap: 12px;
  justify-content: center;
  font-size: 12px;
  color: var(--color-text-label);
}

.chart-legend-item {
  display: flex;
  align-items: center;
  gap: 4px;
}

.chart-legend-swatch {
  width: 12px;
  height: 12px;
  border-radius: 2px;
  display: inline-block;
}

.chart-source {
  font-size: 11px;
  font-weight: 400;
  color: var(--color-chart-neutral-400);
  margin: 0;
  text-align: right;
}
```

---

## 折れ線グラフ

時系列の連続的な変化・トレンド把握に最適。系列数は3本以内を推奨。

```html
<div class="card chart-container">
  <h3 class="chart-title">{{チャートタイトル}}</h3>
  <div class="chart-canvas-wrapper">
    <canvas id="chart-line"></canvas>
  </div>
  <div class="chart-legend" id="legend-line"></div>
  <p class="chart-source">出典: {{データソース名}}</p>
</div>
```

```javascript
// Chart.js 初期化スニペット（グローバル設定は chart-defaults.md を参照）
const chartLine = new Chart(document.getElementById('chart-line'), {
  type: 'line',
  data: {
    labels: [/* 時間軸ラベル */],
    datasets: [
      {
        label: '{{系列名1}}',
        data: [/* 値 */],
        borderColor: getCSSVar('--color-chart-primary-600'),
        backgroundColor: getCSSVar('--color-chart-primary-50'),
        tension: 0.3,
        pointRadius: 3,
      },
      {
        label: '{{系列名2}}',
        data: [/* 値 */],
        borderColor: getCSSVar('--color-chart-primary-900'),
        backgroundColor: 'transparent',
        tension: 0.3,
        pointRadius: 3,
      },
    ],
  },
  options: {
    responsive: true,
    maintainAspectRatio: true,
    scales: {
      y: { beginAtZero: false },
    },
  },
});
```

---

## 棒グラフ

カテゴリ間の比較、期間ごとの量の比較に最適。**Y軸は必ず0始まり**（厳守）。

```html
<div class="card chart-container">
  <h3 class="chart-title">{{チャートタイトル}}</h3>
  <div class="chart-canvas-wrapper">
    <canvas id="chart-bar"></canvas>
  </div>
  <div class="chart-legend" id="legend-bar"></div>
  <p class="chart-source">出典: {{データソース名}}</p>
</div>
```

```javascript
const chartBar = new Chart(document.getElementById('chart-bar'), {
  type: 'bar',
  data: {
    labels: [/* カテゴリラベル */],
    datasets: [
      {
        label: '{{系列名}}',
        data: [/* 値 */],
        backgroundColor: getCSSVar('--color-chart-primary-600'),
        borderRadius: 4,
      },
    ],
  },
  options: {
    responsive: true,
    maintainAspectRatio: true,
    // カテゴリ名が長い場合は indexAxis: 'y' で横棒に切り替え
    scales: {
      y: { beginAtZero: true },
    },
  },
});
```

---

## 積み上げ棒グラフ

内訳の比較、構成比のカテゴリ間比較に最適。100%積み上げも可能。

```html
<div class="card chart-container">
  <h3 class="chart-title">{{チャートタイトル}}</h3>
  <div class="chart-canvas-wrapper">
    <canvas id="chart-stacked-bar"></canvas>
  </div>
  <div class="chart-legend" id="legend-stacked-bar"></div>
  <p class="chart-source">出典: {{データソース名}}</p>
</div>
```

```javascript
const chartStackedBar = new Chart(document.getElementById('chart-stacked-bar'), {
  type: 'bar',
  data: {
    labels: [/* カテゴリラベル */],
    datasets: [
      {
        label: '{{内訳1}}',
        data: [/* 値 */],
        backgroundColor: getCSSVar('--color-chart-primary-600'),
      },
      {
        label: '{{内訳2}}',
        data: [/* 値 */],
        backgroundColor: getCSSVar('--color-chart-primary-900'),
      },
      {
        label: '{{内訳3}}',
        data: [/* 値 */],
        backgroundColor: getCSSVar('--color-chart-primary-400'),
      },
    ],
  },
  options: {
    responsive: true,
    maintainAspectRatio: true,
    scales: {
      x: { stacked: true },
      y: { stacked: true, beginAtZero: true },
    },
  },
});
```

---

## 積み上げ面グラフ

累積値の時系列変化、内訳の推移に最適。半透明にして重なりを見えやすくする。

```html
<div class="card chart-container">
  <h3 class="chart-title">{{チャートタイトル}}</h3>
  <div class="chart-canvas-wrapper">
    <canvas id="chart-stacked-area"></canvas>
  </div>
  <div class="chart-legend" id="legend-stacked-area"></div>
  <p class="chart-source">出典: {{データソース名}}</p>
</div>
```

```javascript
const chartStackedArea = new Chart(document.getElementById('chart-stacked-area'), {
  type: 'line',
  data: {
    labels: [/* 時間軸ラベル */],
    datasets: [
      {
        label: '{{内訳1}}',
        data: [/* 値 */],
        borderColor: getCSSVar('--color-chart-primary-600'),
        backgroundColor: getCSSVar('--color-chart-primary-600'),  // fill時に半透明化
        fill: true,
      },
      {
        label: '{{内訳2}}',
        data: [/* 値 */],
        borderColor: getCSSVar('--color-chart-primary-900'),
        backgroundColor: getCSSVar('--color-chart-primary-900'),
        fill: true,
      },
      {
        label: '{{内訳3}}',
        data: [/* 値 */],
        borderColor: getCSSVar('--color-chart-primary-400'),
        backgroundColor: getCSSVar('--color-chart-primary-400'),
        fill: true,
      },
    ],
  },
  options: {
    responsive: true,
    maintainAspectRatio: true,
    scales: {
      x: { stacked: true },
      y: { stacked: true, beginAtZero: true },
    },
    elements: {
      line: { tension: 0.3 },
      point: { radius: 0 },
    },
  },
});
```

> **注意**: `backgroundColor` には半透明の値を使用する。CSS変数から取得した色に `+ '80'`（50%透明）を付加するか、`rgba()` に変換する。

---

## 円グラフ / ドーナツチャート

全体に対する構成比をコンパクトに伝える。カテゴリは5個以下+「その他」が上限。ドーナツチャートを推奨（中央に総数値を表示可能）。

```html
<div class="card chart-container">
  <h3 class="chart-title">{{チャートタイトル}}</h3>
  <div class="chart-canvas-wrapper chart-donut-wrapper">
    <canvas id="chart-donut"></canvas>
    <!-- ドーナツ中央値（position: absoluteで重ねる） -->
    <div class="donut-center">
      <span class="donut-center-value">{{合計値}}</span>
      <span class="donut-center-label">{{単位}}</span>
    </div>
  </div>
  <div class="chart-legend" id="legend-donut"></div>
  <p class="chart-source">出典: {{データソース名}}</p>
</div>
```

```css
.chart-donut-wrapper {
  position: relative;
  display: flex;
  align-items: center;
  justify-content: center;
}

.donut-center {
  position: absolute;
  display: flex;
  flex-direction: column;
  align-items: center;
  pointer-events: none;
}

.donut-center-value {
  font-size: 28px;
  font-weight: 700;
  color: var(--color-text-black);
  line-height: 1.2;
}

.donut-center-label {
  font-size: 12px;
  font-weight: 400;
  color: var(--color-text-label);
}
```

```javascript
const chartDonut = new Chart(document.getElementById('chart-donut'), {
  type: 'doughnut',
  data: {
    labels: [/* カテゴリ名 */],
    datasets: [
      {
        data: [/* 値 */],
        backgroundColor: [
          getCSSVar('--color-chart-primary-600'),
          getCSSVar('--color-chart-primary-900'),
          getCSSVar('--color-chart-primary-400'),
          getCSSVar('--color-chart-secondary-600'),
          getCSSVar('--color-chart-neutral-400'),
        ],
        borderWidth: 2,
        borderColor: getCSSVar('--color-bg-card'),
      },
    ],
  },
  options: {
    responsive: true,
    maintainAspectRatio: true,
    cutout: '60%',
    plugins: {
      legend: { display: false },  // カスタム凡例を使用
    },
  },
});
```

> **円グラフ（Pie）にする場合**: `type: 'pie'` に変更し、`cutout` オプションを削除する。ドーナツ中央値のHTMLも不要。

---

## 複合グラフ

棒グラフと折れ線グラフの重ね合わせ。量と率の同時表示などに使用。ただし二重軸は誤解を招くため、可能なら2つのチャートに分けることを検討する。

```html
<div class="card chart-container">
  <h3 class="chart-title">{{チャートタイトル}}</h3>
  <div class="chart-canvas-wrapper">
    <canvas id="chart-combo"></canvas>
  </div>
  <div class="chart-legend" id="legend-combo"></div>
  <p class="chart-source">出典: {{データソース名}}</p>
</div>
```

```javascript
const chartCombo = new Chart(document.getElementById('chart-combo'), {
  type: 'bar',
  data: {
    labels: [/* カテゴリ / 時間軸ラベル */],
    datasets: [
      {
        type: 'bar',
        label: '{{棒グラフ系列名}}',
        data: [/* 値 */],
        backgroundColor: getCSSVar('--color-chart-primary-600'),
        borderRadius: 4,
        yAxisID: 'y',
      },
      {
        type: 'line',
        label: '{{折れ線系列名}}',
        data: [/* 値 */],
        borderColor: getCSSVar('--color-chart-secondary-600'),
        backgroundColor: 'transparent',
        tension: 0.3,
        pointRadius: 3,
        yAxisID: 'y1',
      },
    ],
  },
  options: {
    responsive: true,
    maintainAspectRatio: true,
    scales: {
      y: {
        beginAtZero: true,
        position: 'left',
      },
      y1: {
        beginAtZero: true,
        position: 'right',
        grid: { drawOnChartArea: false },
      },
    },
  },
});
```

---

## データテーブル

詳細データの提供・正確な数値の参照用。ソート可能、ヘッダー固定、ゼブラストライプ。

```html
<div class="card table-container">
  <h3 class="chart-title">{{テーブルタイトル}}</h3>
  <div class="table-scroll-wrapper">
    <table class="data-table">
      <thead>
        <tr>
          <th class="text-left">{{項目名}}</th>
          <th class="text-right">{{数値列1}}</th>
          <th class="text-right">{{数値列2}}</th>
          <th class="text-right">{{増減}}</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td class="text-left">{{行1 項目名}}</td>
          <td class="text-right">{{1,234}}</td>
          <td class="text-right">{{5,678}}</td>
          <td class="text-right cell-positive">+12.3%</td>
        </tr>
        <tr>
          <td class="text-left">{{行2 項目名}}</td>
          <td class="text-right">{{987}}</td>
          <td class="text-right">{{654}}</td>
          <td class="text-right cell-negative">-3.2%</td>
        </tr>
        <!-- 繰り返し -->
      </tbody>
      <tfoot>
        <tr class="row-total">
          <td class="text-left">合計</td>
          <td class="text-right">{{2,221}}</td>
          <td class="text-right">{{6,332}}</td>
          <td class="text-right">-</td>
        </tr>
      </tfoot>
    </table>
  </div>
  <p class="chart-source">出典: {{データソース名}}</p>
</div>
```

```css
.table-container {
  display: flex;
  flex-direction: column;
  gap: 12px;
}

.table-scroll-wrapper {
  overflow-x: auto;
}

.data-table {
  width: 100%;
  border-collapse: collapse;
  font-size: 14px;
  color: var(--color-text-black);
}

.data-table th {
  position: sticky;
  top: 0;
  background: var(--color-bg-control);
  font-weight: 600;
  font-size: 13px;
  color: var(--color-text-label);
  padding: 10px 12px;
  border-bottom: 2px solid var(--color-bg-border);
  white-space: nowrap;
}

.data-table td {
  padding: 10px 12px;
  border-bottom: 1px solid var(--color-bg-border);
}

/* ゼブラストライプ */
.data-table tbody tr:nth-child(even) {
  background: var(--color-bg-standard);
}

.data-table tbody tr:hover {
  background: var(--color-chart-primary-50);
}

/* テキスト配置 */
.text-left {
  text-align: left;
}

.text-right {
  text-align: right;
  font-variant-numeric: tabular-nums;
}

/* 増減セル */
.cell-positive {
  color: var(--color-semantic-positive);
  font-weight: 600;
}

.cell-negative {
  color: var(--color-semantic-negative);
  font-weight: 600;
}

/* 合計行 */
.row-total td {
  font-weight: 700;
  border-top: 2px solid var(--color-bg-border);
  border-bottom: none;
  background: var(--color-bg-control);
}
```

---

## フッター

データ定義、出典情報、最終更新日を含むダッシュボードフッター。

```html
<footer class="dashboard-footer">
  <div class="footer-definitions">
    <h4 class="footer-heading">データ定義</h4>
    <dl class="footer-dl">
      <dt>{{用語1}}</dt>
      <dd>{{定義1}}</dd>
      <dt>{{用語2}}</dt>
      <dd>{{定義2}}</dd>
    </dl>
  </div>
  <div class="footer-meta">
    <span class="footer-source">出典: {{データソース名}}</span>
    <span class="footer-update">最終更新: {{YYYY年MM月DD日 HH:MM}}</span>
  </div>
</footer>
```

```css
.dashboard-footer {
  padding: 16px 24px;
  background: var(--color-bg-standard);
  border-top: 1px solid var(--color-bg-border);
  border-radius: 0 0 8px 8px;
}

.footer-heading {
  font-size: 13px;
  font-weight: 600;
  color: var(--color-text-black);
  margin: 0 0 8px 0;
}

.footer-dl {
  display: grid;
  grid-template-columns: auto 1fr;
  gap: 4px 12px;
  font-size: 12px;
  color: var(--color-text-label);
  margin: 0;
}

.footer-dl dt {
  font-weight: 600;
  color: var(--color-text-black);
}

.footer-dl dd {
  margin: 0;
}

.footer-meta {
  display: flex;
  justify-content: space-between;
  margin-top: 12px;
  padding-top: 8px;
  border-top: 1px solid var(--color-bg-border);
  font-size: 11px;
  color: var(--color-chart-neutral-400);
}

.footer-source,
.footer-update {
  font-size: 11px;
  color: var(--color-chart-neutral-400);
}
```
