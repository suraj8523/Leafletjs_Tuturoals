# マップペインの操作

**難易度：上級** ⭐⭐⭐⭐⭐

## このチュートリアルで学ぶこと

- マップペインの概念
- レイヤーの表示順序の制御
- カスタムペインの作成
- z-indexの管理
- 高度なレイヤー制御
- 実用的な応用例

## 必要な前提知識

- [01_quick_start.md](01_quick_start.md)の基礎知識
- [05_layer_groups_control.md](05_layer_groups_control.md)のレイヤー管理
- CSS の z-index の理解（推奨）

## マップペインとは？

Leafletのマップは複数の**ペイン**（pane）で構成されています。ペインはレイヤーの表示順序を制御するためのコンテナです。

### デフォルトのペイン

Leafletには以下のデフォルトペインがあります：

| ペイン名 | z-index | 用途 |
|---------|---------|------|
| `mapPane` | auto | すべてのペインの親 |
| `tilePane` | 200 | タイルレイヤー |
| `overlayPane` | 400 | ベクター（SVG/Canvas） |
| `shadowPane` | 500 | マーカーの影 |
| `markerPane` | 600 | マーカーアイコン |
| `tooltipPane` | 650 | ツールチップ |
| `popupPane` | 700 | ポップアップ |

## 1. 基本的なセットアップ

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>マップペイン</title>
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css"
          integrity="sha256-p4NxAoJBhIIN+hmNHrzRCf9tD/miZyoHS5obTRR9BMY="
          crossorigin=""/>
    <style>
        body {
            margin: 0;
            padding: 20px;
            font-family: Arial, sans-serif;
        }
        #map {
            height: 600px;
            width: 100%;
        }
    </style>
</head>
<body>
    <h1>マップペインの操作</h1>
    <div id="map"></div>

    <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"
            integrity="sha256-20nQCchB9co0qIjJZRGuk2/Z9VM+kNiyxNV1lvTlZBo="
            crossorigin=""></script>
    <script>
        // ここにコードを追加
    </script>
</body>
</html>
```

## 2. デフォルトペインの確認

```javascript
var map = L.map('map').setView([35.6812, 139.7671], 13);

L.tileLayer('https://tile.openstreetmap.org/{z}/{x}/{y}.png', {
    maxZoom: 19,
    attribution: '© OpenStreetMap contributors'
}).addTo(map);

// すべてのペインを取得
var panes = map.getPanes();
console.log('利用可能なペイン:', Object.keys(panes));

// 各ペインのz-indexを確認
Object.keys(panes).forEach(function(paneName) {
    var pane = panes[paneName];
    console.log(paneName + ' z-index:', window.getComputedStyle(pane).zIndex);
});
```

## 3. カスタムペインの作成

### 基本的なカスタムペイン

```javascript
var map = L.map('map').setView([35.6812, 139.7671], 13);

L.tileLayer('https://tile.openstreetmap.org/{z}/{x}/{y}.png', {
    maxZoom: 19,
    attribution: '© OpenStreetMap contributors'
}).addTo(map);

// カスタムペインを作成
map.createPane('customPane');

// z-indexを設定（マーカーとポップアップの間）
map.getPane('customPane').style.zIndex = 650;

// オプション：ポインターイベントを無効化
map.getPane('customPane').style.pointerEvents = 'none';
```

### カスタムペインにレイヤーを追加

```javascript
// カスタムペインを作成
map.createPane('labelsPane');
map.getPane('labelsPane').style.zIndex = 650;
map.getPane('labelsPane').style.pointerEvents = 'none';

// カスタムペインに円を追加
var circle = L.circle([35.6812, 139.7671], {
    color: 'red',
    fillColor: '#f03',
    fillOpacity: 0.5,
    radius: 500,
    pane: 'labelsPane'  // カスタムペインを指定
}).addTo(map);

// 通常のペインにマーカーを追加
L.marker([35.6812, 139.7671]).addTo(map)
    .bindPopup('マーカー（上に表示）');
```

## 4. レイヤーの表示順序を制御

```javascript
var map = L.map('map').setView([35.6812, 139.7671], 13);

L.tileLayer('https://tile.openstreetmap.org/{z}/{x}/{y}.png', {
    maxZoom: 19,
    attribution: '© OpenStreetMap contributors'
}).addTo(map);

// 背景用ペイン（タイルの上）
map.createPane('backgroundPane');
map.getPane('backgroundPane').style.zIndex = 250;

// 前景用ペイン（マーカーの上）
map.createPane('foregroundPane');
map.getPane('foregroundPane').style.zIndex = 650;

// 背景レイヤー
L.rectangle([
    [35.680, 139.765],
    [35.682, 139.770]
], {
    color: 'blue',
    fillColor: 'blue',
    fillOpacity: 0.3,
    pane: 'backgroundPane'
}).addTo(map).bindPopup('背景レイヤー');

// 前景レイヤー
L.rectangle([
    [35.681, 139.766],
    [35.683, 139.771]
], {
    color: 'red',
    fillColor: 'red',
    fillOpacity: 0.5,
    pane: 'foregroundPane'
}).addTo(map).bindPopup('前景レイヤー');

// 通常のマーカー
L.marker([35.6815, 139.768]).addTo(map)
    .bindPopup('通常のマーカー');
```

## 5. ラベルを常に最前面に表示

```javascript
var map = L.map('map').setView([35.6812, 139.7671], 12);

L.tileLayer('https://tile.openstreetmap.org/{z}/{x}/{y}.png', {
    maxZoom: 19,
    attribution: '© OpenStreetMap contributors'
}).addTo(map);

// ラベル専用ペインを作成（最前面）
map.createPane('labelPane');
map.getPane('labelPane').style.zIndex = 1000;
map.getPane('labelPane').style.pointerEvents = 'none';

// エリアを描画
var areas = [
    {name: 'エリアA', bounds: [[35.680, 139.760], [35.685, 139.765]], color: 'blue'},
    {name: 'エリアB', bounds: [[35.682, 139.765], [35.687, 139.770]], color: 'green'},
    {name: 'エリアC', bounds: [[35.678, 139.765], [35.683, 139.770]], color: 'red'}
];

areas.forEach(function(area) {
    // エリアの四角形
    L.rectangle(area.bounds, {
        color: area.color,
        fillColor: area.color,
        fillOpacity: 0.3
    }).addTo(map);

    // ラベル（常に最前面）
    var center = L.latLngBounds(area.bounds).getCenter();
    var label = L.marker(center, {
        icon: L.divIcon({
            className: 'area-label',
            html: '<div style="background: white; padding: 5px; border: 2px solid ' + area.color + '; border-radius: 3px; font-weight: bold;">' + area.name + '</div>',
            iconSize: [80, 30]
        }),
        pane: 'labelPane'
    }).addTo(map);
});
```

## 6. 透過レイヤーとクリックイベント

```javascript
var map = L.map('map').setView([35.6812, 139.7671], 13);

L.tileLayer('https://tile.openstreetmap.org/{z}/{x}/{y}.png', {
    maxZoom: 19,
    attribution: '© OpenStreetMap contributors'
}).addTo(map);

// 透過レイヤー用ペイン（クリックイベントを通過）
map.createPane('transparentPane');
map.getPane('transparentPane').style.zIndex = 450;
map.getPane('transparentPane').style.pointerEvents = 'none';

// インタラクティブレイヤー用ペイン
map.createPane('interactivePane');
map.getPane('interactivePane').style.zIndex = 400;

// 透過レイヤー（クリック不可）
L.rectangle([
    [35.680, 139.765],
    [35.682, 139.770]
], {
    color: 'blue',
    fillColor: 'blue',
    fillOpacity: 0.2,
    pane: 'transparentPane'
}).addTo(map);

// インタラクティブレイヤー（クリック可能）
L.rectangle([
    [35.681, 139.766],
    [35.683, 139.771]
], {
    color: 'red',
    fillColor: 'red',
    fillOpacity: 0.5,
    pane: 'interactivePane'
}).addTo(map).on('click', function() {
    alert('クリックされました！');
});
```

## 7. 動的なペイン管理

```javascript
var map = L.map('map').setView([35.6812, 139.7671], 13);

L.tileLayer('https://tile.openstreetmap.org/{z}/{x}/{y}.png', {
    maxZoom: 19,
    attribution: '© OpenStreetMap contributors'
}).addTo(map);

// 複数のペインを作成
var paneNames = ['pane1', 'pane2', 'pane3'];
var panes = {};

paneNames.forEach(function(paneName, index) {
    map.createPane(paneName);
    map.getPane(paneName).style.zIndex = 400 + (index * 10);
    panes[paneName] = [];
});

// 各ペインにレイヤーを追加
L.circle([35.6812, 139.7671], {
    radius: 1000,
    color: 'red',
    pane: 'pane1'
}).addTo(map);

L.circle([35.6812, 139.7671], {
    radius: 800,
    color: 'green',
    pane: 'pane2'
}).addTo(map);

L.circle([35.6812, 139.7671], {
    radius: 600,
    color: 'blue',
    pane: 'pane3'
}).addTo(map);

// ボタンでz-indexを変更
function changeOrder() {
    map.getPane('pane1').style.zIndex = 420;
    map.getPane('pane2').style.zIndex = 410;
    map.getPane('pane3').style.zIndex = 400;
}
```

## 8. アニメーション効果の追加

```javascript
var map = L.map('map').setView([35.6812, 139.7671], 13);

L.tileLayer('https://tile.openstreetmap.org/{z}/{x}/{y}.png', {
    maxZoom: 19,
    attribution: '© OpenStreetMap contributors'
}).addTo(map);

// アニメーション用ペイン
map.createPane('animatedPane');
map.getPane('animatedPane').style.zIndex = 650;

// CSSアニメーションを追加
var paneElement = map.getPane('animatedPane');
paneElement.style.animation = 'pulse 2s infinite';

// CSSを追加
var style = document.createElement('style');
style.textContent = `
    @keyframes pulse {
        0%, 100% { opacity: 1; }
        50% { opacity: 0.5; }
    }
`;
document.head.appendChild(style);

// アニメーションするレイヤー
L.circle([35.6812, 139.7671], {
    radius: 500,
    color: 'red',
    fillColor: '#f03',
    fillOpacity: 0.5,
    pane: 'animatedPane'
}).addTo(map);
```

## 9. 実践例：複数レイヤーの階層管理

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>レイヤー階層管理</title>
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css"
          integrity="sha256-p4NxAoJBhIIN+hmNHrzRCf9tD/miZyoHS5obTRR9BMY="
          crossorigin=""/>
    <style>
        body {
            margin: 0;
            padding: 0;
        }
        #map {
            height: 100vh;
            width: 100%;
        }
        .control-panel {
            position: absolute;
            top: 10px;
            right: 10px;
            z-index: 1000;
            background: white;
            padding: 15px;
            border-radius: 5px;
            box-shadow: 0 0 15px rgba(0,0,0,0.2);
        }
        .control-panel button {
            display: block;
            width: 100%;
            margin: 5px 0;
            padding: 8px;
            cursor: pointer;
        }
    </style>
</head>
<body>
    <div class="control-panel">
        <h3>レイヤー順序</h3>
        <button onclick="bringToFront('layer1')">Layer 1を最前面</button>
        <button onclick="bringToFront('layer2')">Layer 2を最前面</button>
        <button onclick="bringToFront('layer3')">Layer 3を最前面</button>
    </div>
    <div id="map"></div>

    <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"
            integrity="sha256-20nQCchB9co0qIjJZRGuk2/Z9VM+kNiyxNV1lvTlZBo="
            crossorigin=""></script>
    <script>
        var map = L.map('map').setView([35.6812, 139.7671], 13);

        L.tileLayer('https://tile.openstreetmap.org/{z}/{x}/{y}.png', {
            maxZoom: 19,
            attribution: '© OpenStreetMap contributors'
        }).addTo(map);

        // 3つのカスタムペインを作成
        map.createPane('layer1Pane');
        map.createPane('layer2Pane');
        map.createPane('layer3Pane');

        map.getPane('layer1Pane').style.zIndex = 400;
        map.getPane('layer2Pane').style.zIndex = 410;
        map.getPane('layer3Pane').style.zIndex = 420;

        // レイヤー1（赤）
        L.rectangle([
            [35.680, 139.765],
            [35.683, 139.770]
        ], {
            color: 'red',
            fillColor: 'red',
            fillOpacity: 0.5,
            pane: 'layer1Pane'
        }).addTo(map).bindPopup('Layer 1 (赤)');

        // レイヤー2（緑）
        L.rectangle([
            [35.681, 139.766],
            [35.684, 139.771]
        ], {
            color: 'green',
            fillColor: 'green',
            fillOpacity: 0.5,
            pane: 'layer2Pane'
        }).addTo(map).bindPopup('Layer 2 (緑)');

        // レイヤー3（青）
        L.rectangle([
            [35.682, 139.767],
            [35.685, 139.772]
        ], {
            color: 'blue',
            fillColor: 'blue',
            fillOpacity: 0.5,
            pane: 'layer3Pane'
        }).addTo(map).bindPopup('Layer 3 (青)');

        // レイヤーを最前面に持ってくる関数
        function bringToFront(layerName) {
            // すべてのペインをリセット
            map.getPane('layer1Pane').style.zIndex = 400;
            map.getPane('layer2Pane').style.zIndex = 410;
            map.getPane('layer3Pane').style.zIndex = 420;

            // 指定されたペインを最前面に
            map.getPane(layerName + 'Pane').style.zIndex = 430;
        }
    </script>
</body>
</html>
```

## 10. よくある使用例

### ラベルを常に最前面に表示

```javascript
map.createPane('labelPane');
map.getPane('labelPane').style.zIndex = 1000;
map.getPane('labelPane').style.pointerEvents = 'none';

// ラベルをlabelPaneに追加
L.marker(coords, {
    icon: labelIcon,
    pane: 'labelPane'
}).addTo(map);
```

### クリック可能なレイヤーとそうでないレイヤーを分ける

```javascript
// 背景用（クリック不可）
map.createPane('backgroundPane');
map.getPane('backgroundPane').style.pointerEvents = 'none';

// インタラクティブレイヤー用（クリック可）
map.createPane('interactivePane');
map.getPane('interactivePane').style.pointerEvents = 'auto';
```

## よくある質問

### Q: ペインが表示されない

**A:** z-indexが適切に設定されているか確認してください。また、ペインにレイヤーが追加されているか確認。

### Q: ペインの順序を動的に変更できる？

**A:** はい、`getPane().style.zIndex`を変更することで可能です：

```javascript
map.getPane('customPane').style.zIndex = 500;
```

### Q: ペインを削除できる？

**A:** ペイン自体の削除はサポートされていませんが、非表示にすることは可能：

```javascript
map.getPane('customPane').style.display = 'none';
```

## 💪 練習問題

マップペインの理解を深めるための演習です。

### 演習1：カスタムペインでレイヤーの重なり順を制御（初級）

**課題：** 3つの異なるペインを作成し、z-indexを調整してレイヤーの表示順序を制御してください。

**要件:**
- 3つのカスタムペイン（背景、中間、前景）を作成
- 各ペインに異なるz-index値を設定
- 各ペインに円やポリゴンを追加
- ボタンでz-indexを入れ替えて順序を変更

<details>
<summary>💡 実装のヒント</summary>

```
1. ペイン作成:
   map.createPane('backgroundPane');
   map.getPane('backgroundPane').style.zIndex = 250;

2. レイヤーをペインに追加:
   L.circle([lat, lng], {pane: 'backgroundPane'}).addTo(map);

3. z-index変更:
   map.getPane('backgroundPane').style.zIndex = 650;
```
</details>

### 演習2：ラベルペインの実装（中級）

**課題：** 常に最前面に表示されるラベル専用ペインを作成し、他のレイヤーの下に隠れないようにしてください。

**要件:**
- ラベル専用ペイン（z-index: 1000）
- ラベルはクリック不可（pointerEvents: 'none'）
- 複数の地域にラベルを配置
- 地図をズームしてもラベルが見える

<details>
<summary>💡 LLM活用プロンプト例</summary>

```
Leaflet.jsで常に最前面に表示されるラベルレイヤーを実装したいです。

【要件】
1. カスタムペイン'labelPane'を作成（z-index: 1000）
2. pointerEvents: 'none' でクリックイベントを無効化
3. L.divIconを使用してテキストラベルを作成
4. ラベルは白背景、黒枠の見やすいスタイル
5. 複数のラベルを配置

【実装例】
- ペインの作成と設定
- divIconのHTML/CSSスタイリング
- labelPaneへのマーカー追加

コード例を提供してください。
```
</details>

### 演習3：動的ペイン管理システム（上級）

**課題：** レイヤーカテゴリーごとにペインを動的に作成し、z-indexを管理するシステムを実装してください。

**要件:**
- レイヤー設定からペインを自動生成
- ドラッグ&ドロップでz-index順序を変更
- 現在のペイン一覧と順序を表示
- 設定をLocalStorageに保存

<details>
<summary>💡 LLM活用プロンプト例</summary>

```
【ステップ1: ペイン管理構造】
「複数のペインとz-indexを管理するデータ構造を設計してください。
var paneConfig = {
    'roads': {zIndex: 400, layers: [...]},
    'buildings': {zIndex: 450, layers: [...]}
};
この構造でペインを動的に作成・管理する方法を教えてください。」

【ステップ2: UI with Drag & Drop】
「HTML5 Drag and Drop APIを使用して、ペインの順序を変更できるUIを実装してください。
- draggable属性を持つリスト要素
- dragstart, dragover, drop イベント
- ドロップ時にz-indexを再計算
- 視覚的なフィードバック」

【ステップ3: LocalStorage 永続化】
「ペイン設定をLocalStorageに保存し、復元する機能を実装してください。」
```
</details>

### 🎓 学習のポイント

**z-index の管理:**
1. デフォルトペインの範囲を把握（200-700）
2. カスタムペインは適切な範囲に配置
3. 相対的な順序を維持する設計
4. 動的変更時の再描画を考慮

### 📝 LLM活用のコツ

**ペインのデバッグ：**
```
「Leafletのマップペインが期待通りに表示されません。
z-indexは正しく設定していますが、レイヤーの順序がおかしいです。
デバッグ方法と確認すべきポイントを教えてください。」
```

## 次のステップ

- [10_extending_leaflet.md](10_extending_leaflet.md)：Leafletの拡張方法

## 参考リンク

- [Leaflet Map Panes](https://leafletjs.com/reference.html#map-pane)
- [Leaflet Tutorial: Map Panes](https://leafletjs.com/examples/map-panes/)
- [CSS z-index](https://developer.mozilla.org/en-US/docs/Web/CSS/z-index)
