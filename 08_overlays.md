# 画像・ビデオ・SVGオーバーレイ

**難易度：上級** ⭐⭐⭐⭐

## このチュートリアルで学ぶこと

- 画像オーバーレイの追加
- ビデオオーバーレイの実装
- SVGオーバーレイの使用
- 座標境界の指定方法
- インタラクティブなオーバーレイ
- 実用的な応用例

## 必要な前提知識

- [01_quick_start.md](01_quick_start.md)の基礎知識
- HTML5のcanvas/video要素（推奨）

## オーバーレイとは？

地図上に画像、ビデオ、SVGなどのメディアを重ねて表示する機能です。以下のような用途があります：

- 古地図や航空写真の重ね合わせ
- 建物の設計図やフロアマップ
- リアルタイムビデオストリーム
- カスタムグラフィックスの表示

## 1. 基本的なセットアップ

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>オーバーレイ</title>
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
    <h1>画像・ビデオ・SVGオーバーレイ</h1>
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

## 2. 画像オーバーレイの基本

### シンプルな画像オーバーレイ

```javascript
var map = L.map('map').setView([35.6812, 139.7671], 15);

L.tileLayer('https://tile.openstreetmap.org/{z}/{x}/{y}.png', {
    maxZoom: 19,
    attribution: '© OpenStreetMap contributors'
}).addTo(map);

// 画像の表示範囲を指定（南西、北東の座標）
var imageBounds = [
    [35.680, 139.765],  // 南西（左下）
    [35.682, 139.769]   // 北東（右上）
];

// 画像オーバーレイを追加
var imageOverlay = L.imageOverlay('path/to/image.png', imageBounds, {
    opacity: 0.8,
    alt: '画像の説明'
}).addTo(map);

// 画像の範囲に地図をフィット
map.fitBounds(imageBounds);
```

### 画像オーバーレイのオプション

| オプション | 説明 | デフォルト |
|----------|------|-----------|
| `opacity` | 透明度（0〜1） | 1.0 |
| `alt` | 代替テキスト | '' |
| `interactive` | クリックイベントを有効にする | false |
| `crossOrigin` | CORSの設定 | false |
| `errorOverlayUrl` | エラー時の画像URL | '' |
| `zIndex` | z-index | 1 |
| `className` | CSSクラス名 | '' |

## 3. 古地図オーバーレイの例

```javascript
var map = L.map('map').setView([35.6812, 139.7671], 14);

L.tileLayer('https://tile.openstreetmap.org/{z}/{x}/{y}.png', {
    maxZoom: 19,
    attribution: '© OpenStreetMap contributors'
}).addTo(map);

// 古地図の範囲（例：江戸時代の地図）
var historicalMapBounds = [
    [35.675, 139.760],
    [35.687, 139.775]
];

// 古地図オーバーレイ
var historicalMap = L.imageOverlay(
    'https://example.com/historical-map.png',
    historicalMapBounds,
    {
        opacity: 0.7,
        alt: '江戸時代の地図'
    }
);

// レイヤーコントロールに追加
var overlayMaps = {
    "古地図": historicalMap
};

L.control.layers(null, overlayMaps).addTo(map);

// デフォルトで表示
historicalMap.addTo(map);
map.fitBounds(historicalMapBounds);
```

## 4. インタラクティブな画像オーバーレイ

```javascript
var map = L.map('map').setView([35.6812, 139.7671], 15);

L.tileLayer('https://tile.openstreetmap.org/{z}/{x}/{y}.png', {
    maxZoom: 19,
    attribution: '© OpenStreetMap contributors'
}).addTo(map);

var imageBounds = [[35.680, 139.765], [35.682, 139.769]];

var imageOverlay = L.imageOverlay('path/to/image.png', imageBounds, {
    opacity: 0.8,
    interactive: true  // クリックイベントを有効化
}).addTo(map);

// クリックイベント
imageOverlay.on('click', function(e) {
    alert('画像がクリックされました！');
    console.log('クリック位置:', e.latlng);
});

// マウスオーバーで不透明度を変更
imageOverlay.on('mouseover', function() {
    this.setOpacity(1.0);
});

imageOverlay.on('mouseout', function() {
    this.setOpacity(0.8);
});
```

## 5. ビデオオーバーレイ

### 基本的なビデオオーバーレイ

```javascript
var map = L.map('map').setView([35.6812, 139.7671], 15);

L.tileLayer('https://tile.openstreetmap.org/{z}/{x}/{y}.png', {
    maxZoom: 19,
    attribution: '© OpenStreetMap contributors'
}).addTo(map);

var videoBounds = [
    [35.680, 139.765],
    [35.682, 139.769]
];

// ビデオオーバーレイを作成
var videoOverlay = L.videoOverlay('path/to/video.mp4', videoBounds, {
    opacity: 0.8,
    autoplay: true,
    loop: true,
    muted: true
}).addTo(map);

map.fitBounds(videoBounds);
```

### ビデオオーバーレイのオプション

| オプション | 説明 | デフォルト |
|----------|------|-----------|
| `autoplay` | 自動再生 | true |
| `loop` | ループ再生 | true |
| `muted` | ミュート | false |
| `playsInline` | インライン再生 | true |
| `opacity` | 透明度 | 1.0 |

### ビデオコントロールの追加

```javascript
var videoOverlay = L.videoOverlay('path/to/video.mp4', videoBounds, {
    opacity: 0.8,
    autoplay: false,
    loop: true,
    muted: false
}).addTo(map);

// ビデオ要素を取得
var videoElement = videoOverlay.getElement();

// カスタムコントロールを作成
L.Control.VideoControl = L.Control.extend({
    onAdd: function(map) {
        var container = L.DomUtil.create('div', 'leaflet-bar leaflet-control');
        container.style.background = 'white';
        container.style.padding = '5px';

        var playBtn = L.DomUtil.create('button', '', container);
        playBtn.innerHTML = '▶️ 再生';
        playBtn.onclick = function() {
            videoElement.play();
        };

        var pauseBtn = L.DomUtil.create('button', '', container);
        pauseBtn.innerHTML = '⏸️ 停止';
        pauseBtn.onclick = function() {
            videoElement.pause();
        };

        return container;
    }
});

var videoControl = new L.Control.VideoControl({position: 'topright'});
map.addControl(videoControl);
```

## 6. SVGオーバーレイ

### 基本的なSVGオーバーレイ

```javascript
var map = L.map('map').setView([35.6812, 139.7671], 15);

L.tileLayer('https://tile.openstreetmap.org/{z}/{x}/{y}.png', {
    maxZoom: 19,
    attribution: '© OpenStreetMap contributors'
}).addTo(map);

// SVG文字列
var svgElement = document.createElementNS("http://www.w3.org/2000/svg", "svg");
svgElement.setAttribute('xmlns', "http://www.w3.org/2000/svg");
svgElement.setAttribute('viewBox', "0 0 200 200");
svgElement.innerHTML = '<rect width="200" height="200" fill="blue" opacity="0.5"/><circle cx="100" cy="100" r="50" fill="red" opacity="0.7"/>';

var svgElementBounds = [[35.680, 139.765], [35.682, 139.769]];

var svgOverlay = L.svgOverlay(svgElement, svgElementBounds, {
    opacity: 0.8,
    interactive: true
}).addTo(map);
```

### SVGで矢印を描画

```javascript
var svgElement = document.createElementNS("http://www.w3.org/2000/svg", "svg");
svgElement.setAttribute('xmlns', "http://www.w3.org/2000/svg");
svgElement.setAttribute('viewBox', "0 0 200 200");

// 矢印を描画
svgElement.innerHTML = `
    <defs>
        <marker id="arrowhead" markerWidth="10" markerHeight="10" refX="9" refY="3" orient="auto">
            <polygon points="0 0, 10 3, 0 6" fill="red" />
        </marker>
    </defs>
    <line x1="20" y1="100" x2="180" y2="100" stroke="red" stroke-width="3" marker-end="url(#arrowhead)" />
    <text x="100" y="80" text-anchor="middle" font-size="20" fill="black">方向</text>
`;

var svgBounds = [[35.680, 139.765], [35.682, 139.769]];

L.svgOverlay(svgElement, svgBounds, {
    opacity: 1,
    interactive: true
}).addTo(map);
```

## 7. フロアマップ（建物内地図）の例

```javascript
var map = L.map('map', {
    crs: L.CRS.Simple,  // 非地理座標系を使用
    minZoom: -2
});

// 建物のフロアマップ画像
var bounds = [[0, 0], [1000, 1000]];
var floorPlan = L.imageOverlay('path/to/floorplan.png', bounds).addTo(map);

map.fitBounds(bounds);

// 部屋にマーカーを追加
L.marker([500, 500], {
    icon: L.divIcon({
        className: 'room-marker',
        html: '<div style="background: red; width: 20px; height: 20px; border-radius: 50%;"></div>',
        iconSize: [20, 20]
    })
}).addTo(map).bindPopup('会議室A');

// エリアを四角形で表示
L.rectangle([[200, 200], [400, 400]], {
    color: 'blue',
    weight: 2,
    fillOpacity: 0.2
}).addTo(map).bindPopup('オフィスエリア');
```

## 8. 複数の画像を切り替える

```javascript
var map = L.map('map').setView([35.6812, 139.7671], 15);

L.tileLayer('https://tile.openstreetmap.org/{z}/{x}/{y}.png', {
    maxZoom: 19,
    attribution: '© OpenStreetMap contributors'
}).addTo(map);

var bounds = [[35.680, 139.765], [35.682, 139.769]];

// 複数の画像オーバーレイ
var overlay1 = L.imageOverlay('path/to/image1.png', bounds, {opacity: 0.8});
var overlay2 = L.imageOverlay('path/to/image2.png', bounds, {opacity: 0.8});
var overlay3 = L.imageOverlay('path/to/image3.png', bounds, {opacity: 0.8});

// レイヤーコントロール
var overlayMaps = {
    "2020年": overlay1,
    "2021年": overlay2,
    "2022年": overlay3
};

L.control.layers(null, overlayMaps).addTo(map);

// デフォルトで1つ目を表示
overlay1.addTo(map);
map.fitBounds(bounds);
```

## 9. Canvas オーバーレイ（カスタム描画）

```javascript
var map = L.map('map').setView([35.6812, 139.7671], 15);

L.tileLayer('https://tile.openstreetmap.org/{z}/{x}/{y}.png', {
    maxZoom: 19,
    attribution: '© OpenStreetMap contributors'
}).addTo(map);

// Canvas要素を作成
var canvas = document.createElement('canvas');
canvas.width = 500;
canvas.height = 500;

var ctx = canvas.getContext('2d');

// 円を描画
ctx.beginPath();
ctx.arc(250, 250, 100, 0, 2 * Math.PI);
ctx.fillStyle = 'rgba(255, 0, 0, 0.5)';
ctx.fill();
ctx.strokeStyle = 'red';
ctx.lineWidth = 5;
ctx.stroke();

// テキストを描画
ctx.font = '30px Arial';
ctx.fillStyle = 'black';
ctx.textAlign = 'center';
ctx.fillText('カスタム描画', 250, 250);

// CanvasをImageOverlayとして追加
var bounds = [[35.680, 139.765], [35.682, 139.769]];
var canvasOverlay = L.imageOverlay(canvas.toDataURL(), bounds, {
    opacity: 0.8
}).addTo(map);

map.fitBounds(bounds);
```

## 10. 完全な実践例：災害マップ

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>災害マップ - オーバーレイ例</title>
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
        .info-panel {
            position: absolute;
            top: 10px;
            left: 60px;
            z-index: 1000;
            background: white;
            padding: 15px;
            border-radius: 5px;
            box-shadow: 0 0 15px rgba(0,0,0,0.2);
            max-width: 300px;
        }
    </style>
</head>
<body>
    <div class="info-panel">
        <h3>災害リスクマップ</h3>
        <p>各レイヤーを切り替えてリスクエリアを確認できます。</p>
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

        // Canvas で洪水リスクエリアを描画
        var floodCanvas = document.createElement('canvas');
        floodCanvas.width = 800;
        floodCanvas.height = 800;
        var floodCtx = floodCanvas.getContext('2d');

        // グラデーションを作成
        var gradient = floodCtx.createRadialGradient(400, 400, 50, 400, 400, 300);
        gradient.addColorStop(0, 'rgba(0, 0, 255, 0.7)');
        gradient.addColorStop(1, 'rgba(0, 0, 255, 0)');

        floodCtx.fillStyle = gradient;
        floodCtx.fillRect(0, 0, 800, 800);

        var floodBounds = [[35.675, 139.760], [35.687, 139.775]];
        var floodOverlay = L.imageOverlay(floodCanvas.toDataURL(), floodBounds, {
            opacity: 0.6
        });

        // Canvas で地震リスクエリアを描画
        var earthquakeCanvas = document.createElement('canvas');
        earthquakeCanvas.width = 800;
        earthquakeCanvas.height = 800;
        var eqCtx = earthquakeCanvas.getContext('2d');

        var eqGradient = eqCtx.createRadialGradient(400, 400, 50, 400, 400, 350);
        eqGradient.addColorStop(0, 'rgba(255, 0, 0, 0.7)');
        eqGradient.addColorStop(1, 'rgba(255, 0, 0, 0)');

        eqCtx.fillStyle = eqGradient;
        eqCtx.fillRect(0, 0, 800, 800);

        var earthquakeBounds = [[35.677, 139.762], [35.685, 139.773]];
        var earthquakeOverlay = L.imageOverlay(earthquakeCanvas.toDataURL(), earthquakeBounds, {
            opacity: 0.6
        });

        // レイヤーコントロール
        var overlayMaps = {
            "🌊 洪水リスク": floodOverlay,
            "🏚️ 地震リスク": earthquakeOverlay
        };

        L.control.layers(null, overlayMaps).addTo(map);

        // 避難所マーカー
        var shelters = [
            {name: '避難所A', coords: [35.680, 139.768]},
            {name: '避難所B', coords: [35.682, 139.770]}
        ];

        shelters.forEach(function(shelter) {
            L.marker(shelter.coords).addTo(map)
                .bindPopup('<b>' + shelter.name + '</b><br>収容人数: 500人');
        });
    </script>
</body>
</html>
```

## よくある質問

### Q: 画像が表示されない

**A:** 以下を確認：
1. 画像URLが正しいか
2. 画像の座標範囲が正しいか
3. CORS問題がないか（外部画像の場合）
4. 画像ファイルが存在するか

### Q: オーバーレイのサイズを変更するには？

**A:** `setBounds()`メソッドを使用：

```javascript
var newBounds = [[35.679, 139.764], [35.683, 139.770]];
imageOverlay.setBounds(newBounds);
```

### Q: ビデオが自動再生されない

**A:** ブラウザの自動再生ポリシーにより、`muted: true`が必要な場合があります：

```javascript
var videoOverlay = L.videoOverlay('video.mp4', bounds, {
    autoplay: true,
    muted: true  // 自動再生に必要
});
```

## 💪 練習問題

オーバーレイの理解を深めるための演習です。

### 演習1：画像オーバーレイの配置と調整（初級）

**課題：** 古地図や航空写真を現代の地図上に重ねて表示し、透明度スライダーで見比べられるようにしてください。

**要件:**
- 画像オーバーレイの追加
- 透明度を0-100%で調整するスライダー
- 「表示/非表示」ボタン
- 画像の範囲に地図を自動フィット

<details>
<summary>💡 実装のヒント</summary>

```
1. 画像オーバーレイの作成:
   var imageOverlay = L.imageOverlay('old-map.png', bounds, {opacity: 0.7});

2. スライダーイベント:
   slider.addEventListener('input', function(e) {
       imageOverlay.setOpacity(e.target.value / 100);
   });

3. 表示/非表示:
   button.onclick = function() {
       if (map.hasLayer(imageOverlay)) {
           map.removeLayer(imageOverlay);
       } else {
           imageOverlay.addTo(map);
       }
   };
```
</details>

### 演習2：インタラクティブなフロアマップ（中級）

**課題：** 建物のフロアマップ画像を使用し、クリックした位置に部屋情報のマーカーを追加する機能を実装してください。

**要件:**
- L.CRS.Simpleを使用した非地理座標系
- フロアマップ画像をオーバーレイ
- クリック位置に部屋マーカーを追加
- 各マーカーに部屋名と説明を表示
- マーカーデータをLocalStorageに保存

<details>
<summary>💡 LLM活用プロンプト例</summary>

```
Leaflet.jsで建物のフロアマップを表示し、クリックで部屋情報を追加したいです。

【要件】
1. 非地理座標系（L.CRS.Simple）を使用
2. フロアマップ画像をL.imageOverlayで表示
3. map.on('click')でクリック位置を取得
4. プロンプトで部屋名を入力
5. マーカーとポップアップを追加
6. データをLocalStorageに保存し、再読み込み時に復元

【技術的な詳細】
- 座標系の変換方法
- 画像サイズとboundsの設定
- LocalStorage のデータ構造設計

実装例を提供してください。
```
</details>

### 演習3：ビデオオーバーレイのタイムライン制御（上級）

**課題：** ビデオオーバーレイにカスタムタイムラインコントロールを実装し、特定の時間にジャンプできる機能を追加してください。

**要件:**
- ビデオオーバーレイの表示
- カスタムタイムラインスライダー
- 再生/停止ボタン
- 特定時間へのジャンプマーカー（例: 重要シーン）
- 再生速度の変更（0.5x, 1x, 2x）

<details>
<summary>💡 LLM活用プロンプト例</summary>

```
【ステップ1: ビデオコントロール基礎】
「Leafletのビデオオーバーレイに対してJavaScriptからビデオを制御したいです。
- videoOverlay.getElement()でvideo要素を取得
- play(), pause(), currentTime, duration の使用方法
- イベントリスナー(timeupdate, ended)の実装」

【ステップ2: タイムラインUI】
「HTML5 range inputでビデオのタイムラインを実装してください:
- スライダーの値をビデオの再生位置と同期
- timeupdate イベントでスライダーを更新
- スライダー操作でビデオの再生位置を変更
- 現在時間と総時間を表示」

【ステップ3: マーカー機能】
「タイムライン上に重要シーンのマーカーを配置し、クリックでジャンプできるようにしてください:
- マーカーの配列データ [{time: 10, label: "シーン1"}, ...]
- タイムライン上にマーカーを視覚的に表示
- クリックで該当時間にジャンプ
- ツールチップでラベル表示」
```
</details>

### 🎓 学習のポイント

**オーバーレイの選択基準:**
1. **画像**: 静的な図面、古地図、航空写真
2. **ビデオ**: 時系列変化、監視カメラ、ドローン映像
3. **SVG**: ベクターグラフィックス、動的な図形、アニメーション
4. **Canvas**: リアルタイム描画、大量のデータ点

### 📝 LLM活用のコツ

**座標系の変換：**
```
「Leaflet の L.CRS.Simple でピクセル座標と地図座標の関係を理解したいです。
画像サイズが1000x800pxの場合、boundsをどう設定すべきか、
また画像上の特定ピクセル（例: 500, 400）を地図座標に変換する方法を教えてください。」
```

## 次のステップ

- [09_map_panes.md](09_map_panes.md)：マップペインの操作
- [10_extending_leaflet.md](10_extending_leaflet.md)：Leafletの拡張方法

## 参考リンク

- [Leaflet ImageOverlay](https://leafletjs.com/reference.html#imageoverlay)
- [Leaflet VideoOverlay](https://leafletjs.com/reference.html#videooverlay)
- [Leaflet SVGOverlay](https://leafletjs.com/reference.html#svgoverlay)
- [Non-geographical Maps Tutorial](https://leafletjs.com/examples/non-geographical.html)
