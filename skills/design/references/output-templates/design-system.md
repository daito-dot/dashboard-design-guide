# デザインシステム

キーカラーを選択するとダッシュボード全体のトンマナが決まるテーマシステム。

このデザインシステムは「材料」であり「完成品」ではない。CSS変数を通じて一貫性を提供するが、品質はこれらの変数を目的に応じて正しく組み合わせることで生まれる。変数をそのまま使えば一貫性は得られるが、なぜその色をその場所に使うのかを判断するのはAIの仕事である。

## キーカラーの選択

Phase 1の要件確認時に、ダッシュボードの性質に応じてキーカラーを選択する。
デフォルトはBlue。ユーザーの要望や組織のブランドカラーに合わせて変更可能。

| キーカラー | 印象 | 適する用途 |
|-----------|------|-----------|
| Blue | 信頼・公式 | 行政・公共データ、デフォルト |
| Light Blue | 軽やか・親しみ | サービス利用状況 |
| Cyan | 落ち着き・安定 | 環境・インフラ |
| Green | 成長・健全 | 財務・業績 |
| Orange | 活力・注意 | 営業・マーケティング |
| Red | 緊急・重要 | アラート・セキュリティ（メインテーマとしては稀） |
| Solid Gray | 中立・控えめ | データ密度の高い分析画面 |

## テーマ構造

テーマは以下の層で構成される。キーカラーによって変わるのは **★** のついた層のみ。

### 固定層（全テーマ共通）

テキスト、背景（Standard/Control）、セカンダリ（Yellow）、ニュートラル（Gray）、セマンティック Negative/Success/Errorは全テーマで同一。

```css
/* ===== 固定層: テキスト ===== */
--color-text-black: #000000;
--color-text-white: #FFFFFF;
--color-text-label: #626264;        /* Solid Gray 600 */
--color-text-link: #0017C1;         /* Blue 900 */

/* ===== 固定層: 背景 ===== */
--color-bg-standard: #F8F8FB;
--color-bg-card: #FFFFFF;
--color-bg-control: #F1F1F4;        /* Solid Gray 50 */
--color-bg-border: #D2D2D8;         /* Solid Gray 200 */

/* ===== 固定層: チャート セカンダリ（Yellow） ===== */
--color-chart-secondary-1200: #A58000;
--color-chart-secondary-600: #FFC700;
--color-chart-secondary-900: #D2A400;

/* ===== 固定層: チャート ニュートラル（Solid Gray） ===== */
--color-chart-neutral-900: #3D3D3F; /* Solid Gray 900 */
--color-chart-neutral-600: #626264; /* Solid Gray 600 */
--color-chart-neutral-400: #93939B; /* Solid Gray 400 */
--color-chart-neutral-200: #D2D2D8; /* Solid Gray 200 */

/* ===== 固定層: セマンティック Negative ===== */
--color-semantic-negative: #FE3939; /* Red 600 */
--color-semantic-negative-200: #FE9E9E; /* Red 200 */
--color-semantic-negative-50: #FFDEDE;  /* Red 50 */

/* ===== 固定層: セマンティック Success / Error ===== */
--color-semantic-success: #197A4B;  /* Green 600 */
--color-semantic-error: #CE0000;    /* Red 900 */
```

### 可変層（キーカラーに依存） ★

チャート Primary（6段階）、背景 Highlight、セマンティック Positiveはキーカラーによって変わる。

```css
/* ===== 可変層: チャート プライマリ ===== */
--color-chart-primary-1200: /* key color 1200 */;
--color-chart-primary-900:  /* key color 900 */;
--color-chart-primary-600:  /* key color 600 */;
--color-chart-primary-400:  /* key color 400 */;
--color-chart-primary-200:  /* key color 200 */;
--color-chart-primary-50:   /* key color 50 */;

/* ===== 可変層: 背景 Highlight ===== */
--color-bg-highlight: /* key color 900 */;

/* ===== 可変層: セマンティック Positive ===== */
--color-semantic-positive: /* key color 600 */;
--color-semantic-positive-200: /* key color 200 */;
--color-semantic-positive-50:  /* key color 50 */;
```

## 7ファミリーのCSS変数セット

### Blue（デフォルト）

全変数（固定層＋可変層）を含む完全なテーマ定義。

```css
:root {
  /* テキスト */
  --color-text-black: #000000;
  --color-text-white: #FFFFFF;
  --color-text-label: #626264;
  --color-text-link: #0017C1;

  /* 背景 */
  --color-bg-standard: #F8F8FB;
  --color-bg-card: #FFFFFF;
  --color-bg-highlight: #0017C1;     /* Blue 900 */
  --color-bg-control: #F1F1F4;
  --color-bg-border: #D2D2D8;

  /* チャート プライマリ（Blue） */
  --color-chart-primary-1200: #000060;
  --color-chart-primary-900: #0017C1;
  --color-chart-primary-600: #3460FB;
  --color-chart-primary-400: #7096F8;
  --color-chart-primary-200: #C5D7FB;
  --color-chart-primary-50: #D9E6FF;

  /* チャート セカンダリ（Yellow） */
  --color-chart-secondary-1200: #A58000;
  --color-chart-secondary-900: #D2A400;
  --color-chart-secondary-600: #FFC700;

  /* チャート ニュートラル（Solid Gray） */
  --color-chart-neutral-900: #3D3D3F;
  --color-chart-neutral-600: #626264;
  --color-chart-neutral-400: #93939B;
  --color-chart-neutral-200: #D2D2D8;

  /* セマンティック Positive */
  --color-semantic-positive: #3460FB;     /* Blue 600 */
  --color-semantic-positive-200: #C5D7FB; /* Blue 200 */
  --color-semantic-positive-50: #D9E6FF;  /* Blue 50 */

  /* セマンティック Negative */
  --color-semantic-negative: #FE3939;
  --color-semantic-negative-200: #FE9E9E;
  --color-semantic-negative-50: #FFDEDE;

  /* セマンティック Success / Error */
  --color-semantic-success: #197A4B;
  --color-semantic-error: #CE0000;
}
```

### Light Blue

可変層のみ。固定層はBlueと同一。

```css
[data-theme="light-blue"] {
  /* チャート プライマリ（Light Blue） */
  --color-chart-primary-1200: #003B5C;
  --color-chart-primary-900: #005E91;
  --color-chart-primary-600: #0083C1;
  --color-chart-primary-400: #4AA9E1;
  --color-chart-primary-200: #8AD0F8;
  --color-chart-primary-50: #D2EEFF;

  /* 背景 Highlight */
  --color-bg-highlight: #005E91;     /* Light Blue 900 */

  /* セマンティック Positive */
  --color-semantic-positive: #0083C1;     /* Light Blue 600 */
  --color-semantic-positive-200: #8AD0F8; /* Light Blue 200 */
  --color-semantic-positive-50: #D2EEFF;  /* Light Blue 50 */
}
```

### Cyan

```css
[data-theme="cyan"] {
  /* チャート プライマリ（Cyan） */
  --color-chart-primary-1200: #003C3B;
  --color-chart-primary-900: #006361;
  --color-chart-primary-600: #008B88;
  --color-chart-primary-400: #4FADA9;
  --color-chart-primary-200: #86D5D2;
  --color-chart-primary-50: #D1F3F3;

  /* 背景 Highlight */
  --color-bg-highlight: #006361;     /* Cyan 900 */

  /* セマンティック Positive */
  --color-semantic-positive: #008B88;     /* Cyan 600 */
  --color-semantic-positive-200: #86D5D2; /* Cyan 200 */
  --color-semantic-positive-50: #D1F3F3;  /* Cyan 50 */
}
```

### Green

```css
[data-theme="green"] {
  /* チャート プライマリ（Green） */
  --color-chart-primary-1200: #003B23;
  --color-chart-primary-900: #0F5B38;
  --color-chart-primary-600: #197A4B;
  --color-chart-primary-400: #4FA858;
  --color-chart-primary-200: #84CA8B;
  --color-chart-primary-50: #D1EDD7;

  /* 背景 Highlight */
  --color-bg-highlight: #0F5B38;     /* Green 900 */

  /* セマンティック Positive */
  --color-semantic-positive: #197A4B;     /* Green 600 */
  --color-semantic-positive-200: #84CA8B; /* Green 200 */
  --color-semantic-positive-50: #D1EDD7;  /* Green 50 */
}
```

### Orange

```css
[data-theme="orange"] {
  /* チャート プライマリ（Orange） */
  --color-chart-primary-1200: #5C3200;
  --color-chart-primary-900: #944F00;
  --color-chart-primary-600: #BD6C00;
  --color-chart-primary-400: #DB9027;
  --color-chart-primary-200: #F8B95F;
  --color-chart-primary-50: #FFE8C6;

  /* 背景 Highlight */
  --color-bg-highlight: #944F00;     /* Orange 900 */

  /* セマンティック Positive */
  --color-semantic-positive: #BD6C00;     /* Orange 600 */
  --color-semantic-positive-200: #F8B95F; /* Orange 200 */
  --color-semantic-positive-50: #FFE8C6;  /* Orange 50 */
}
```

### Red

```css
[data-theme="red"] {
  /* チャート プライマリ（Red） */
  --color-chart-primary-1200: #700000;
  --color-chart-primary-900: #CE0000;
  --color-chart-primary-600: #FE3939;
  --color-chart-primary-400: #FE6A6A;
  --color-chart-primary-200: #FE9E9E;
  --color-chart-primary-50: #FFDEDE;

  /* 背景 Highlight */
  --color-bg-highlight: #CE0000;     /* Red 900 */

  /* セマンティック Positive */
  --color-semantic-positive: #FE3939;     /* Red 600 */
  --color-semantic-positive-200: #FE9E9E; /* Red 200 */
  --color-semantic-positive-50: #FFDEDE;  /* Red 50 */
}
```

### Solid Gray

```css
[data-theme="solid-gray"] {
  /* チャート プライマリ（Solid Gray） */
  --color-chart-primary-1200: #1A1A1C;
  --color-chart-primary-900: #3D3D3F;
  --color-chart-primary-600: #626264;
  --color-chart-primary-400: #93939B;
  --color-chart-primary-200: #D2D2D8;
  --color-chart-primary-50: #F1F1F4;

  /* 背景 Highlight */
  --color-bg-highlight: #3D3D3F;     /* Solid Gray 900 */

  /* セマンティック Positive */
  --color-semantic-positive: #626264;     /* Solid Gray 600 */
  --color-semantic-positive-200: #D2D2D8; /* Solid Gray 200 */
  --color-semantic-positive-50: #F1F1F4;  /* Solid Gray 50 */
}
```

## テーマ切り替えの実装

### 方法: data-theme属性

`<html>` 要素の `data-theme` 属性でテーマを切り替える。

```html
<!-- デフォルト（Blue） -->
<html>

<!-- Cyan テーマ -->
<html data-theme="cyan">

<!-- Orange テーマ -->
<html data-theme="orange">
```

### JavaScript による動的切り替え

```javascript
function setTheme(theme) {
  if (theme === 'blue') {
    document.documentElement.removeAttribute('data-theme');
  } else {
    document.documentElement.setAttribute('data-theme', theme);
  }
}

// 使用例
setTheme('cyan');
setTheme('blue'); // デフォルトに戻す
```

### CSS構成の推奨パターン

1. `:root` にBlue（デフォルト）の全変数を定義
2. `[data-theme="xxx"]` セレクタで可変層のみ上書き
3. コンポーネントのスタイルではHex値を直接使わず、必ずCSS変数を参照

```css
/* 良い例 */
.chart-bar { fill: var(--color-chart-primary-600); }
.kpi-positive { color: var(--color-semantic-positive); }
.card { background: var(--color-bg-card); border: 1px solid var(--color-bg-border); }

/* 悪い例（テーマ切り替えが効かない） */
.chart-bar { fill: #3460FB; }
```

### チャート系列への割り当て順

プライマリカラーを複数系列に割り当てる場合の順序:

```
1系列目: var(--color-chart-primary-600)
2系列目: var(--color-chart-primary-900)
3系列目: var(--color-chart-primary-400)
4系列目: var(--color-chart-primary-1200)
5系列目: var(--color-chart-primary-200)
```

6系列以上が必要な場合はセカンダリ（Yellow）またはニュートラル（Gray）を追加する。
