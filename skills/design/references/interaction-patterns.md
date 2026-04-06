# インタラクションパターン

探索型ダッシュボードのインタラクション設計。提示型は「見るだけ」で完結するが、探索型はユーザーの操作によってデータの見え方が変わる。このファイルは探索型のインタラクションを設計するための原則と材料を提供する。

すべてのコンポーネントは `output-templates/design-system.md` のCSS変数を使用する。

---

## 探索型インタラクションの3原則

### 1. 全体と部分の往復

典型的なフロー: **Overview → Focus → Detail → Overview**。ただし常にOverviewから始めるとは限らない。検索や特定のアラートから入り、コンテキストを広げていくフロー（Search → Context → Expand）もある。

設計に求められるのは「どこからでも全体に戻れること」と「どこからでも詳細に降りられること」。

### 2. ビュー間連動

一つのビューでの操作が、他のビューの表示に波及する。これにより、多次元のデータを単一の操作で横断的に探索できる。

連動の種類:
- **選択連動**: あるチャートでの選択が他のチャートの対応データをハイライト
- **フィルタ連動**: あるフィルタの変更が関連するすべてのチャートを更新
- **ナビゲーション連動**: あるビューのズームやスクロールが他のビューに同期

連動は強力だが、過剰な連動はユーザーを混乱させる。どのビューがどのビューに影響するかを明示する。

### 3. 段階的開示

情報を一度に全部見せず、ユーザーの操作に応じて段階的に開示する。画面の情報量を管理しつつ、必要な深さまで到達できるようにする。

段階的開示の手段:
- **Detail-on-Demand**: ホバーやクリックで個別データの詳細を表示
- **ドリルダウン**: 集約レベルを下げてより細かいデータに遷移
- **ページ分割**: 関心の異なる情報群をページに分けてナビゲート

---

## インタラクション設計の判断基準

Phase 1で探索型と判定された後、Phase 2で個々の画面・チャートにどのインタラクションを付けるかを決めるときに使う。

1. **ユーザーはこのデータに対して「なぜ？」と問うか？** — 問うなら、答えにたどり着く手段（ドリルダウン、フィルタ）が必要
2. **複数の切り口で同じデータを見る必要があるか？** — あるなら、ビュー間連動（クロスフィルタリング）が必要
3. **データの粒度が粗いまとまりと細かい詳細の両方あるか？** — あるなら、段階的開示（ドリルダウン、Detail-on-Demand）が必要
4. **情報量が1画面に収まらないか？** — 収まらないなら、ページナビゲーションかパラメータ化が必要

不要なインタラクションは追加しない。提示型で十分な画面に探索機能を足すと、操作の手間が増えるだけで判断は速くならない。

---

## チャートクリックイベントの共通基盤

複数のインタラクション（ドリルダウン、クロスフィルタリング）が同じチャートの`onClick`を使う場合に衝突を避けるための共通基盤。各コンポーネントはこの基盤を通じてチャートクリックを受け取る。

```javascript
/**
 * チャートクリックのイベントディスパッチャー
 *
 * Chart.jsのonClickを一元管理し、複数のハンドラが共存できるようにする。
 *
 * 使い方:
 *   const dispatcher = new ChartClickDispatcher(chart);
 *   dispatcher.on('click', (label, index, dataset) => { ... });
 *   dispatcher.on('click', (label, index, dataset) => { ... }); // 複数登録可
 */
class ChartClickDispatcher {
  constructor(chart) {
    this.chart = chart;
    this.handlers = [];

    chart.options.onClick = (event, elements) => {
      if (elements.length === 0) {
        this.handlers.forEach(fn => fn(null, -1, null));
        return;
      }
      const el = elements[0];
      const label = chart.data.labels[el.index];
      const dataset = chart.data.datasets[el.datasetIndex];
      this.handlers.forEach(fn => fn(label, el.index, dataset));
    };
  }

  on(event, handler) {
    if (event === 'click') this.handlers.push(handler);
  }

  off(event, handler) {
    if (event === 'click') {
      this.handlers = this.handlers.filter(fn => fn !== handler);
    }
  }
}
```

---

## コンポーネント

### Detail-on-Demand（テーブル行展開）

テーブル行のクリックで詳細情報を展開表示する。

```html
<tr class="expandable-row" onclick="toggleDetail(this)">
  <td>東京都</td>
  <td class="number">1,234,567</td>
  <td class="number">+5.2%</td>
  <td class="expand-icon">▶</td>
</tr>
<tr class="detail-row" hidden>
  <td colspan="4">
    <div class="detail-content">
      <!-- 展開時の詳細コンテンツ -->
    </div>
  </td>
</tr>
```

```css
.expandable-row {
  cursor: pointer;
}

.expandable-row:hover {
  background: var(--color-bg-control);
}

.expand-icon {
  color: var(--color-text-label);
  font-size: 12px;
  transition: transform 0.2s;
}

.expandable-row.expanded .expand-icon {
  transform: rotate(90deg);
}

.detail-row .detail-content {
  padding: 12px 16px;
  background: var(--color-bg-standard);
  border-left: 3px solid var(--color-chart-primary-600);
}
```

```javascript
function toggleDetail(row) {
  const detailRow = row.nextElementSibling;
  const isExpanded = row.classList.toggle('expanded');
  detailRow.hidden = !isExpanded;
}
```

---

### クロスフィルタリング（ビュー間連動）

あるチャートでのクリックが他のチャートをフィルタリングする。探索型の中核パターン。

接続は意図的に一方向。`connect(sourceId, updateFn)` で「AをクリックしたらBが更新される」を明示的に宣言する。双方向にしたければ `connect` を逆方向にもう一度呼ぶ。この設計により、どのビューがどのビューに影響するかが常にコードで明示される。便利な双方向メソッドは過剰な連動を暗黙に誘発するため、提供しない。

```javascript
/**
 * クロスフィルタリング管理
 *
 * ChartClickDispatcherと組み合わせて使う。
 *
 * 使い方:
 *   const cf = new CrossFilter(document.querySelector('.filter-indicator'));
 *   const dispatcher = new ChartClickDispatcher(regionChart);
 *   cf.register('region', regionChart, dispatcher);
 *   cf.connect('region', (selected, allFilters) => {
 *     // selected: クリックされたラベル（解除時はnull）
 *     // allFilters: 全フィルタの現在状態
 *     updateCategoryChart(selected);
 *   });
 */
class CrossFilter {
  constructor(indicatorEl) {
    this.charts = {};
    this.listeners = {};
    this.activeFilters = {};
    this.indicatorEl = indicatorEl || null;
  }

  register(id, chart, dispatcher) {
    this.charts[id] = { chart };
    this.listeners[id] = [];

    dispatcher.on('click', (label) => {
      if (label === null) {
        this.clearFilter(id);
      } else {
        this.applyFilter(id, label);
      }
    });
  }

  connect(sourceId, updateFn) {
    this.listeners[sourceId].push(updateFn);
  }

  applyFilter(sourceId, value) {
    this.activeFilters[sourceId] = value;
    this.listeners[sourceId].forEach(fn => fn(value, this.activeFilters));
    this.updateIndicator();
  }

  clearFilter(sourceId) {
    delete this.activeFilters[sourceId];
    this.listeners[sourceId].forEach(fn => fn(null, this.activeFilters));
    this.updateIndicator();
  }

  clearAll() {
    Object.keys(this.activeFilters).forEach(id => this.clearFilter(id));
  }

  updateIndicator() {
    if (!this.indicatorEl) return;

    const filters = Object.entries(this.activeFilters);
    if (filters.length === 0) {
      this.indicatorEl.hidden = true;
      return;
    }
    this.indicatorEl.hidden = false;
    this.indicatorEl.querySelector('.filter-indicator-text').textContent =
      filters.map(([id, val]) => `${id}: ${val}`).join(' / ');
  }
}
```

#### フィルタ状態インジケーター

クロスフィルタリング使用時に、現在適用中のフィルタを表示する。ユーザーが「今何で絞り込まれているか」を常に把握できるようにする。

```html
<div class="filter-indicator" id="crossFilterIndicator" hidden>
  <span class="filter-indicator-label">絞り込み中:</span>
  <span class="filter-indicator-text"></span>
  <button class="filter-indicator-clear">✕ 解除</button>
</div>
```

```css
.filter-indicator {
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 8px 16px;
  background: var(--color-chart-primary-50);
  border: 1px solid var(--color-chart-primary-200);
  border-radius: 6px;
  font-size: 13px;
  color: var(--color-text-black);
}

.filter-indicator-label {
  font-weight: 600;
  color: var(--color-chart-primary-900);
}

.filter-indicator-clear {
  margin-left: auto;
  padding: 2px 8px;
  border: none;
  border-radius: 4px;
  background: var(--color-chart-primary-200);
  color: var(--color-chart-primary-900);
  font-size: 12px;
  cursor: pointer;
}

.filter-indicator-clear:hover {
  background: var(--color-chart-primary-400);
  color: var(--color-text-white);
}
```

```javascript
// 初期化例
const indicatorEl = document.getElementById('crossFilterIndicator');
const cf = new CrossFilter(indicatorEl);

indicatorEl.querySelector('.filter-indicator-clear')
  .addEventListener('click', () => cf.clearAll());
```

---

### ドリルダウンナビゲーション

集約データから詳細データへ段階的に遷移する。パンくずリストで現在位置を示し、任意の階層に戻れるようにする。

```html
<nav class="breadcrumb" aria-label="ドリルダウン階層">
  <ol class="breadcrumb-list" id="drillBreadcrumb">
    <!-- DrillDownクラスが動的に生成 -->
  </ol>
</nav>
```

```css
.breadcrumb {
  padding: 8px 0;
}

.breadcrumb-list {
  display: flex;
  align-items: center;
  gap: 4px;
  list-style: none;
  padding: 0;
  margin: 0;
  font-size: 13px;
}

.breadcrumb-item + .breadcrumb-item::before {
  content: '›';
  margin-right: 4px;
  color: var(--color-text-label);
}

.breadcrumb-link {
  background: none;
  border: none;
  padding: 2px 4px;
  border-radius: 4px;
  color: var(--color-text-link);
  font-size: 13px;
  cursor: pointer;
  text-decoration: underline;
  text-underline-offset: 2px;
}

.breadcrumb-link:hover {
  background: var(--color-bg-control);
}

.breadcrumb-current {
  color: var(--color-text-black);
  font-weight: 600;
}
```

#### ドリルダウン管理

ChartClickDispatcherと組み合わせて使う。CrossFilterと同じチャートに同時適用可能。

```javascript
/**
 * ドリルダウン管理
 *
 * 使い方:
 *   const dispatcher = new ChartClickDispatcher(chart);
 *   const drill = new DrillDown(chart, breadcrumbEl, dispatcher, [
 *     { label: '全国', getData: (filters) => nationalData },
 *     { label: '地方', getData: (filters) => getRegionData(filters) },
 *     { label: '都道府県', getData: (filters) => getPrefData(filters) },
 *   ]);
 */
class DrillDown {
  constructor(chart, breadcrumbEl, dispatcher, levels) {
    this.chart = chart;
    this.breadcrumbEl = breadcrumbEl;
    this.levels = levels;
    this.currentLevel = 0;
    this.path = [{ label: levels[0].label, filter: null }];

    dispatcher.on('click', (label) => {
      if (label === null || this.currentLevel >= levels.length - 1) return;
      this.drillInto(label);
    });

    this.render();
  }

  drillInto(filterValue) {
    this.currentLevel++;
    this.path.push({
      label: filterValue,
      filter: filterValue
    });
    this.updateChart();
    this.render();
  }

  drillTo(levelIndex) {
    if (levelIndex >= this.currentLevel) return;
    this.currentLevel = levelIndex;
    this.path = this.path.slice(0, levelIndex + 1);
    this.updateChart();
    this.render();
  }

  updateChart() {
    const level = this.levels[this.currentLevel];
    const filters = this.path.map(p => p.filter).filter(Boolean);
    const data = level.getData(filters);

    this.chart.data.labels = data.labels;
    this.chart.data.datasets[0].data = data.values;
    this.chart.update();
  }

  render() {
    const ol = this.breadcrumbEl.querySelector('.breadcrumb-list') || this.breadcrumbEl;
    const self = this;
    ol.innerHTML = '';

    this.path.forEach((item, i) => {
      const li = document.createElement('li');
      li.className = 'breadcrumb-item';

      if (i === this.path.length - 1) {
        li.classList.add('breadcrumb-current');
        li.setAttribute('aria-current', 'page');
        li.textContent = item.label;
      } else {
        const btn = document.createElement('button');
        btn.className = 'breadcrumb-link';
        btn.textContent = item.label;
        btn.addEventListener('click', () => self.drillTo(i));
        li.appendChild(btn);
      }
      ol.appendChild(li);
    });
  }
}
```

---

### ページナビゲーション（タブ）

情報量が多い探索型ダッシュボードを複数ページに分割し、タブで切り替える。概要→詳細の階層構造にも、独立した観点の並列表示にも使える。

```html
<div class="tab-nav" role="tablist" aria-label="ダッシュボードページ">
  <button class="tab-button tab-active" role="tab"
          aria-selected="true" aria-controls="tab-overview"
          tabindex="0"
          onclick="switchTab('overview')">
    概要
  </button>
  <button class="tab-button" role="tab"
          aria-selected="false" aria-controls="tab-regional"
          tabindex="-1"
          onclick="switchTab('regional')">
    地域別分析
  </button>
  <button class="tab-button" role="tab"
          aria-selected="false" aria-controls="tab-trend"
          tabindex="-1"
          onclick="switchTab('trend')">
    トレンド
  </button>
</div>

<div id="tab-overview" class="tab-panel" role="tabpanel">
  <!-- 概要ページのコンテンツ -->
</div>
<div id="tab-regional" class="tab-panel" role="tabpanel" hidden>
  <!-- 地域別分析のコンテンツ -->
</div>
<div id="tab-trend" class="tab-panel" role="tabpanel" hidden>
  <!-- トレンドのコンテンツ -->
</div>
```

```css
.tab-nav {
  display: flex;
  gap: 0;
  border-bottom: 2px solid var(--color-bg-border);
  margin-bottom: 20px;
}

.tab-button {
  padding: 10px 20px;
  border: none;
  border-bottom: 2px solid transparent;
  margin-bottom: -2px;
  background: none;
  font-size: 14px;
  font-weight: 500;
  color: var(--color-text-label);
  cursor: pointer;
  transition: color 0.15s, border-color 0.15s;
}

.tab-button:hover {
  color: var(--color-text-black);
}

.tab-button.tab-active {
  color: var(--color-chart-primary-900);
  border-bottom-color: var(--color-chart-primary-600);
  font-weight: 600;
}
```

```javascript
function switchTab(tabId) {
  const tablist = document.querySelector('[role="tablist"]');
  const tabs = tablist.querySelectorAll('[role="tab"]');

  // ボタンの状態を更新
  tabs.forEach(btn => {
    const isTarget = btn.getAttribute('aria-controls') === `tab-${tabId}`;
    btn.classList.toggle('tab-active', isTarget);
    btn.setAttribute('aria-selected', isTarget);
    btn.setAttribute('tabindex', isTarget ? '0' : '-1');
  });

  // パネルの表示を切り替え
  document.querySelectorAll('.tab-panel').forEach(panel => {
    panel.hidden = panel.id !== `tab-${tabId}`;
  });

  // 遅延初期化: タブが初めて表示されたときにチャートを描画
  const panel = document.getElementById(`tab-${tabId}`);
  if (panel.dataset.initialized !== 'true') {
    panel.dataset.initialized = 'true';
    const event = new CustomEvent('tab:init', { detail: { panel } });
    document.dispatchEvent(event);
  }
}

// キーボードナビゲーション（WAI-ARIA Tabs準拠）
document.querySelector('[role="tablist"]').addEventListener('keydown', (e) => {
  const tabs = [...e.currentTarget.querySelectorAll('[role="tab"]')];
  const current = tabs.indexOf(e.target);
  if (current === -1) return;

  let next;
  if (e.key === 'ArrowRight') {
    next = (current + 1) % tabs.length;
  } else if (e.key === 'ArrowLeft') {
    next = (current - 1 + tabs.length) % tabs.length;
  } else if (e.key === 'Home') {
    next = 0;
  } else if (e.key === 'End') {
    next = tabs.length - 1;
  } else {
    return;
  }

  e.preventDefault();
  tabs[next].focus();
  const tabId = tabs[next].getAttribute('aria-controls').replace('tab-', '');
  switchTab(tabId);
});
```

---

### チャートのクリックアクション

チャート要素をクリックしたときに何が起きるかを視覚的に示す。クリック可能であることをユーザーに伝える。

```css
/* Chart.jsのcanvas要素に適用 */
.chart-clickable {
  cursor: pointer;
}

/* クリック可能であることを示すヒント */
.chart-click-hint {
  font-size: 11px;
  color: var(--color-text-label);
  text-align: right;
  margin-top: 4px;
}
```

```html
<div class="card">
  <h3 class="card-title">地域別売上</h3>
  <canvas id="regionChart" class="chart-clickable"></canvas>
  <p class="chart-click-hint">クリックで詳細を表示</p>
</div>
```
