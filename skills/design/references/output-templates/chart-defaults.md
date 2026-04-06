# Chart.js デフォルト設定

ダッシュボード生成時にChart.jsのグローバル設定として最初に適用する。
この設定により、ガイドブックのDo側の仕様がチャートに自動的に反映される。

この設定は「良し悪しの判断基準」をコードとして表現したもの。各設定にどのDo/Don'tを実現しているかをコメントで示している。設定値を変更する場合は、それがどの原則に影響するかを理解した上で行う。

## CSS変数からChart.js色を取得するヘルパー

チャートカラーをCSS変数から動的に取得する。テーマ切り替え時にも自動追従する。

```javascript
/**
 * CSS変数の値を取得するヘルパー
 * design-system.md で定義されたCSS変数を参照する
 */
function getCSSVar(name) {
  return getComputedStyle(document.documentElement)
    .getPropertyValue(name)
    .trim();
}

/**
 * チャート系列のカラーパレットを取得する
 * design-system.md「チャート系列への割り当て順」に準拠:
 *   1: primary-600, 2: primary-900, 3: primary-400, 4: primary-1200, 5: primary-200
 *
 * 【Do】色数を絞り、注目すべき系列を明確にする
 * 【Don't防止】レインボー配色を防ぎ、統一パレットを強制する
 */
function getChartPalette() {
  return [
    getCSSVar('--color-chart-primary-600'),
    getCSSVar('--color-chart-primary-900'),
    getCSSVar('--color-chart-primary-400'),
    getCSSVar('--color-chart-primary-1200'),
    getCSSVar('--color-chart-primary-200'),
  ];
}

/**
 * 半透明バージョン（面グラフ用）
 * 【Do】半透明にして重なりを見えやすくする
 */
function getChartPaletteAlpha(alpha = 0.3) {
  return getChartPalette().map(hex => {
    const r = parseInt(hex.slice(1, 3), 16);
    const g = parseInt(hex.slice(3, 5), 16);
    const b = parseInt(hex.slice(5, 7), 16);
    return `rgba(${r}, ${g}, ${b}, ${alpha})`;
  });
}
```

## グローバル設定

すべてのチャートに共通で適用するデフォルト値。`<script>` の先頭でこのブロックを実行する。

```javascript
// =============================================
// Chart.js グローバルデフォルト設定
// =============================================
// 適用タイミング: ページ読み込み時、Chart.js インポート直後
// 前提: design-system.md の CSS変数が :root に定義済み

(function applyChartDefaults() {

  // --- フォント ---
  // 【Do】タイトルや凡例は端的に記述する → 統一フォントで視覚ノイズを減らす
  Chart.defaults.font.family = "'Noto Sans JP', 'Hiragino Kaku Gothic ProN', sans-serif";
  Chart.defaults.font.size = 12;                    // 軸ラベル: 12px
  Chart.defaults.color = getCSSVar('--color-text-label');  // #626264 (Solid Gray 600)

  // --- レスポンシブ ---
  Chart.defaults.responsive = true;
  Chart.defaults.maintainAspectRatio = false;        // コンテナに合わせて伸縮

  // --- アニメーション ---
  // 【Don't防止】不要なアニメーションを排除 → 短いdurationで素早く表示
  // 【Do】待たせすぎない
  Chart.defaults.animation.duration = 300;

  // --- グリッド線（共通） ---
  // 【Do】不要な要素（過剰なグリッド線）を削除する
  // 【Do】グリッド線は最小限。読み取り補助として薄い線を使う
  Chart.defaults.scale.grid.color = getCSSVar('--color-bg-control');  // #F1F1F4
  Chart.defaults.scale.grid.lineWidth = 1;
  Chart.defaults.scale.grid.drawBorder = false;      // 軸線を非表示にしてすっきりさせる

  // --- 軸ティック ---
  Chart.defaults.scale.ticks.font = { size: 12 };
  Chart.defaults.scale.ticks.color = getCSSVar('--color-text-label');
  Chart.defaults.scale.ticks.padding = 8;

  // --- 凡例 ---
  // 【Do】グラフと凡例を隣接させ、順序を対応づける
  Chart.defaults.plugins.legend.position = 'top';
  Chart.defaults.plugins.legend.align = 'start';
  Chart.defaults.plugins.legend.labels.usePointStyle = true;
  Chart.defaults.plugins.legend.labels.pointStyle = 'circle';
  Chart.defaults.plugins.legend.labels.padding = 16;
  Chart.defaults.plugins.legend.labels.font = { size: 12 };
  Chart.defaults.plugins.legend.labels.color = getCSSVar('--color-text-label');

  // --- ツールチップ ---
  // 【Do】ツールチップで正確な値と単位を表示する
  Chart.defaults.plugins.tooltip.enabled = true;
  Chart.defaults.plugins.tooltip.backgroundColor = getCSSVar('--color-chart-neutral-900'); // #3D3D3F
  Chart.defaults.plugins.tooltip.titleFont = { size: 12 };
  Chart.defaults.plugins.tooltip.bodyFont = { size: 13 };
  Chart.defaults.plugins.tooltip.padding = 10;
  Chart.defaults.plugins.tooltip.cornerRadius = 4;
  Chart.defaults.plugins.tooltip.displayColors = true;
  Chart.defaults.plugins.tooltip.boxPadding = 4;

  // --- タイトル ---
  // 【Do】タイトルにグラフの内容とデータ種別（月次推移・累計等）を表記する
  Chart.defaults.plugins.title.display = false;       // 個別チャートで明示的に設定する
  Chart.defaults.plugins.title.font = { size: 16, weight: 'bold' };
  Chart.defaults.plugins.title.color = getCSSVar('--color-text-black');
  Chart.defaults.plugins.title.padding = { bottom: 16 };
  Chart.defaults.plugins.title.align = 'start';

})();
```

## チャートタイプ別設定

各チャートを生成する際にマージするオプション。グローバル設定と組み合わせて使う。

### 折れ線グラフ

```javascript
/**
 * 折れ線グラフのデフォルトオプション
 * 【Do】マーカー付きにするとデータポイントが明確になる
 * 【Do】系列数は3本以内を推奨
 * ※ Y軸は0始まりでなくてよい（折れ線はトレンド把握が目的）
 */
const lineChartDefaults = {
  type: 'line',
  options: {
    scales: {
      x: {
        grid: {
          display: false,  // X軸グリッド非表示でシンプルに
        },
      },
      y: {
        beginAtZero: false,  // 折れ線は変化の傾向を見せるため0始まり不要
        grid: {
          color: getCSSVar('--color-bg-control'),
          lineWidth: 1,
        },
      },
    },
    elements: {
      line: {
        tension: 0.1,        // わずかな曲線で自然な見た目にする
        borderWidth: 2,
        fill: false,          // 塗りつぶしなし（面グラフと区別）
      },
      point: {
        radius: 3,            // 【Do】データポイントにマーカーを表示
        hoverRadius: 5,
        hitRadius: 10,        // タッチ・クリック判定を広めにとる
        borderWidth: 2,
        backgroundColor: getCSSVar('--color-bg-card'),  // 白抜きマーカー
      },
    },
    plugins: {
      tooltip: {
        mode: 'index',         // 同じX軸上の全系列を表示
        intersect: false,
      },
    },
  },
};
```

### 棒グラフ

```javascript
/**
 * 棒グラフのデフォルトオプション
 * 【Do】Y軸は必ず0から始める（これは厳守） ← 最重要ルール
 * 【Don't防止】差を誇張しないため、beginAtZero: true を強制
 * 【Do】色は1-2色に抑える（比較要素ごとに色分け）
 */
const barChartDefaults = {
  type: 'bar',
  options: {
    scales: {
      x: {
        grid: {
          display: false,     // 縦棒の場合、X軸グリッド不要
        },
      },
      y: {
        beginAtZero: true,    // ★★★ 最重要: 棒グラフは必ず0始まり ★★★
        grid: {
          color: getCSSVar('--color-bg-control'),
          lineWidth: 1,
        },
      },
    },
    elements: {
      bar: {
        borderWidth: 0,
        borderRadius: 2,      // 角をわずかに丸めて現代的な印象に
      },
    },
    // 棒の幅設定
    datasets: {
      bar: {
        barPercentage: 0.7,
        categoryPercentage: 0.8,
      },
    },
  },
};

/**
 * 横棒グラフ（カテゴリ名が長い場合に使用）
 * 【Do】カテゴリが長いテキストなら横棒グラフを使う
 */
const horizontalBarChartDefaults = {
  type: 'bar',
  options: {
    indexAxis: 'y',            // 横棒グラフに切り替え
    scales: {
      x: {
        beginAtZero: true,     // ★★★ 横棒でも0始まり厳守 ★★★
        grid: {
          color: getCSSVar('--color-bg-control'),
          lineWidth: 1,
        },
      },
      y: {
        grid: {
          display: false,
        },
      },
    },
    elements: {
      bar: {
        borderWidth: 0,
        borderRadius: 2,
      },
    },
    datasets: {
      bar: {
        barPercentage: 0.7,
        categoryPercentage: 0.8,
      },
    },
  },
};
```

### 積み上げ棒グラフ

```javascript
/**
 * 積み上げ棒グラフのデフォルトオプション
 * 【Do】構成比の比較に使用（100%積み上げ含む）
 * Y軸は0始まりが自動的に適用される（積み上げのため）
 */
const stackedBarChartDefaults = {
  type: 'bar',
  options: {
    scales: {
      x: {
        stacked: true,
        grid: {
          display: false,
        },
      },
      y: {
        stacked: true,
        beginAtZero: true,     // ★★★ 棒グラフは必ず0始まり ★★★
        grid: {
          color: getCSSVar('--color-bg-control'),
          lineWidth: 1,
        },
      },
    },
    elements: {
      bar: {
        borderWidth: 0,
      },
    },
  },
};
```

### 積み上げ面グラフ

```javascript
/**
 * 積み上げ面グラフのデフォルトオプション
 * 【Do】半透明にして重なりを見えやすくする
 * 【Do】累積値の時系列変化、内訳の推移に使用
 * 注意: 系列の順番に注意（変動が大きいものを上に）
 */
const stackedAreaChartDefaults = {
  type: 'line',
  options: {
    scales: {
      x: {
        grid: {
          display: false,
        },
      },
      y: {
        stacked: true,
        beginAtZero: true,
        grid: {
          color: getCSSVar('--color-bg-control'),
          lineWidth: 1,
        },
      },
    },
    elements: {
      line: {
        tension: 0.1,
        borderWidth: 2,
        fill: true,             // 【積み上げ面】塗りつぶし有効
      },
      point: {
        radius: 0,              // 面グラフではポイント非表示（見づらくなるため）
        hoverRadius: 4,
        hitRadius: 10,
      },
    },
    plugins: {
      tooltip: {
        mode: 'index',
        intersect: false,
      },
      // fill用の透明度はdataset側で backgroundColor に rgba を指定する
      // getChartPaletteAlpha(0.3) を使用すること
    },
  },
};
```

### ドーナツチャート

```javascript
/**
 * ドーナツチャートのデフォルトオプション
 * 【Do】カテゴリは5個以下＋「その他」が上限
 * 【Do】ドーナツチャートを推奨（中央に数値を表示できる）
 * 【Do】最も大きいカテゴリを12時の位置から時計回りに配置
 */
const doughnutChartDefaults = {
  type: 'doughnut',
  options: {
    cutout: '60%',               // ドーナツの穴の大きさ
    rotation: -90,               // 12時の位置から開始（デフォルトは3時）
    circumference: 360,
    plugins: {
      legend: {
        position: 'right',       // ドーナツは右に凡例を配置
        align: 'center',
        labels: {
          usePointStyle: true,
          pointStyle: 'circle',
          padding: 12,
          font: { size: 12 },
        },
      },
      tooltip: {
        callbacks: {
          label: function(context) {
            const total = context.dataset.data.reduce((a, b) => a + b, 0);
            const value = context.parsed;
            const percentage = ((value / total) * 100).toFixed(1);
            return `${context.label}: ${value.toLocaleString()} (${percentage}%)`;
          },
        },
      },
    },
  },
};
```

## ドーナツ中央テキストプラグイン

ガイドブックではドーナツチャートの中央に合計値や主要指標を表示する。
Chart.jsにはこの機能が組み込まれていないため、カスタムプラグインを使用する。

```javascript
/**
 * ドーナツ中央テキストプラグイン
 * 中央に合計値やラベルを描画する
 *
 * 使い方:
 *   plugins: [centerTextPlugin],
 *   options: {
 *     plugins: {
 *       centerText: {
 *         value: '1,234',        // 表示する値
 *         unit: '件',            // 単位（省略可）
 *         label: '合計',         // ラベル（省略可）
 *       }
 *     }
 *   }
 */
const centerTextPlugin = {
  id: 'centerText',
  afterDraw(chart, args, options) {
    if (!options || !options.value) return;

    const { ctx, chartArea } = chart;
    const centerX = (chartArea.left + chartArea.right) / 2;
    const centerY = (chartArea.top + chartArea.bottom) / 2;

    ctx.save();
    ctx.textAlign = 'center';
    ctx.textBaseline = 'middle';

    // 値の描画
    const valueText = options.unit ? `${options.value}${options.unit}` : options.value;
    ctx.font = `bold 24px ${Chart.defaults.font.family}`;
    ctx.fillStyle = getCSSVar('--color-text-black');
    ctx.fillText(valueText, centerX, options.label ? centerY - 8 : centerY);

    // ラベルの描画
    if (options.label) {
      ctx.font = `12px ${Chart.defaults.font.family}`;
      ctx.fillStyle = getCSSVar('--color-text-label');
      ctx.fillText(options.label, centerX, centerY + 16);
    }

    ctx.restore();
  },
};
```

## カラーパレットの適用

チャートを生成する際に、CSS変数からパレットを取得してdatasetに適用する。

```javascript
/**
 * datasetsにパレットカラーを自動適用する
 * 系列数が5を超える場合はセカンダリ・ニュートラルを追加
 *
 * 【Don't防止】レインボー配色を防止し、テーマに沿った色を強制
 * 【Do】色数を絞り、注目すべき系列を明確にする
 */
function applyPaletteToDatasets(datasets) {
  const palette = getChartPalette();

  // 5系列を超える場合のフォールバック
  // 【注意】5系列以上は設計を見直すことを推奨
  const extendedPalette = [
    ...palette,
    getCSSVar('--color-chart-secondary-600'),    // Yellow 600
    getCSSVar('--color-chart-neutral-600'),       // Solid Gray 600
    getCSSVar('--color-chart-secondary-900'),     // Yellow 900
    getCSSVar('--color-chart-neutral-400'),       // Solid Gray 400
  ];

  datasets.forEach((dataset, index) => {
    const color = extendedPalette[index % extendedPalette.length];

    // 色が未指定の場合のみ適用（明示的な色指定を優先）
    if (!dataset.borderColor) {
      dataset.borderColor = color;
    }
    if (!dataset.backgroundColor) {
      // 棒・ドーナツは不透明、折れ線のfillは半透明
      if (dataset.type === 'line' && dataset.fill) {
        const r = parseInt(color.slice(1, 3), 16);
        const g = parseInt(color.slice(3, 5), 16);
        const b = parseInt(color.slice(5, 7), 16);
        dataset.backgroundColor = `rgba(${r}, ${g}, ${b}, 0.3)`;
      } else {
        dataset.backgroundColor = color;
      }
    }
    if (!dataset.pointBackgroundColor && dataset.type !== 'bar') {
      dataset.pointBackgroundColor = getCSSVar('--color-bg-card');
    }
  });

  return datasets;
}
```

## ツールチップのカスタム設定

単位付きの値表示やフォーマットのカスタマイズ。

```javascript
/**
 * ツールチップに単位を付加するコールバック生成関数
 * 【Do】ツールチップで正確な値と単位を表示する
 * 【Do】軸ラベルと単位を明記する
 *
 * @param {string} unit - 単位（例: '件', '円', '%'）
 */
function createTooltipCallback(unit) {
  return {
    callbacks: {
      label: function(context) {
        const label = context.dataset.label || '';
        const value = context.parsed.y ?? context.parsed;
        const formatted = typeof value === 'number'
          ? value.toLocaleString()
          : value;
        return `${label}: ${formatted}${unit}`;
      },
    },
  };
}

/**
 * ツールチップに構成比や補足情報を表示するカスタムコールバック例
 *
 * 値だけでなく、全体に対する割合や前期比を表示したいときに使う。
 */
// 例: 構成比を自動計算して表示
tooltip: {
  callbacks: {
    label: (item) => {
      const value = item.formattedValue;
      const total = item.dataset.data.reduce((a, b) => a + b, 0);
      const pct = ((item.raw / total) * 100).toFixed(1);
      return `${item.dataset.label}: ${value}（${pct}%）`;
    }
  }
}
```

/**
 * Y軸ラベルに単位を付加するコールバック生成関数
 *
 * @param {string} unit - 単位（例: '件', '百万円'）
 */
function createAxisTickCallback(unit) {
  return {
    callback: function(value) {
      return `${value.toLocaleString()}${unit}`;
    },
  };
}
```

## 凡例のカスタム設定

```javascript
/**
 * 凡例のカスタムオプション
 * 【Do】グラフと凡例を隣接させ、順序を対応づける
 * 【Do】タイトルや凡例は冗長にせず、正しく端的に記述する
 *
 * 凡例を非表示にするケース:
 * - 系列が1つしかない棒グラフ → 凡例は冗長
 * - KPIカードの小さなスパークライン → スペース不足
 */
const legendHidden = {
  plugins: {
    legend: {
      display: false,
    },
  },
};

/**
 * 凡例を下部に配置する場合（チャートの横幅が狭い場合に使用）
 */
const legendBottom = {
  plugins: {
    legend: {
      position: 'bottom',
      align: 'center',
      labels: {
        padding: 16,
        usePointStyle: true,
        pointStyle: 'circle',
      },
    },
  },
};
```

## 使用例: 完全なチャート生成

上記のデフォルト設定とヘルパーを組み合わせた実装例。

```javascript
// === 例1: 月次推移の折れ線グラフ ===
const monthlyTrendChart = new Chart(
  document.getElementById('monthlyTrend'),
  {
    ...lineChartDefaults,
    data: {
      labels: ['4月', '5月', '6月', '7月', '8月', '9月'],
      datasets: applyPaletteToDatasets([
        { label: '申請件数', data: [120, 145, 132, 168, 155, 190] },
        { label: '承認件数', data: [110, 138, 125, 160, 148, 182] },
      ]),
    },
    options: {
      ...lineChartDefaults.options,
      plugins: {
        ...lineChartDefaults.options.plugins,
        title: { display: true, text: '申請・承認件数の月次推移' },
        tooltip: createTooltipCallback('件'),
      },
      scales: {
        ...lineChartDefaults.options.scales,
        y: {
          ...lineChartDefaults.options.scales.y,
          ticks: createAxisTickCallback('件'),
        },
      },
    },
  }
);

// === 例2: カテゴリ別の棒グラフ ===
const categoryChart = new Chart(
  document.getElementById('categoryBar'),
  {
    ...barChartDefaults,
    data: {
      labels: ['東京', '大阪', '名古屋', '福岡', '札幌'],
      datasets: applyPaletteToDatasets([
        { label: '利用者数', data: [4500, 3200, 2100, 1800, 1200] },
      ]),
    },
    options: {
      ...barChartDefaults.options,
      plugins: {
        ...legendHidden.plugins,  // 系列1つなので凡例非表示
        title: { display: true, text: '地域別利用者数' },
        tooltip: createTooltipCallback('人'),
      },
    },
  }
);

// === 例3: ドーナツチャート（中央テキスト付き） ===
const doughnutChart = new Chart(
  document.getElementById('statusDoughnut'),
  {
    ...doughnutChartDefaults,
    data: {
      labels: ['完了', '進行中', '未着手', '保留'],
      datasets: [{
        data: [45, 30, 15, 10],
        backgroundColor: getChartPalette().slice(0, 4),
      }],
    },
    options: {
      ...doughnutChartDefaults.options,
      plugins: {
        ...doughnutChartDefaults.options.plugins,
        title: { display: true, text: 'タスク進捗状況' },
        centerText: {
          value: '100',
          unit: '件',
          label: '合計',
        },
      },
    },
    plugins: [centerTextPlugin],
  }
);
```

## 設計ルール（設定で強制できないもの）

以下はChart.jsの設定では強制できないが、ダッシュボード生成時に必ず遵守すること。

| ルール | 根拠 | 対応 |
|--------|------|------|
| ドーナツ/円グラフは5カテゴリ以下 | 5個超は判読困難 | 6個以上は「その他」に集約する |
| 二重軸（dual-axis）は使わない | スケールの違いで誤解を招く | 2つのチャートに分割する |
| 3D効果を使わない | 不要な装飾は排除する | Chart.jsは2Dのみなので問題なし |
| タイトルにデータ種別を含める | 「月次推移」「累計」等を明記 | 例: "申請件数の月次推移" |
| 軸ラベルと単位を明記する | 何の数字かわからないチャートは無意味 | createAxisTickCallback を使用 |
| データの定義を明示する | 税込/税抜、対象範囲など | チャート下部に注記を追加 |
| 更新日時を表示する | データの鮮度を明示 | ダッシュボードヘッダーに記載 |
| 0始まりでないY軸には注記 | 折れ線グラフで省略する場合 | 波線マークまたは注記テキスト |
| 色だけで分類を識別しない | アクセシビリティ | 形・模様・ラベルを併用する |
