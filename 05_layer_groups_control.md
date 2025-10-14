# レイヤーグループとレイヤーコントロール

**難易度：中級** ⭐⭐⭐

## このチュートリアルで学ぶこと

- レイヤーグループの作成と管理
- レイヤーコントロールの追加
- ベースマップの切り替え
- オーバーレイレイヤーの表示/非表示
- 動的なレイヤー管理

## 必要な前提知識

- [01_quick_start.md](01_quick_start.md)の基礎知識
- [04_geojson.md](04_geojson.md)のGeoJSON基礎（推奨）

## レイヤーとは？

Leafletでは、地図上のすべての要素（タイル、マーカー、図形など）が**レイヤー**です。レイヤーをグループ化することで：

- 複数の要素をまとめて管理
- ユーザーが表示/非表示を切り替え可能
- 地図の見た目を動的に変更

## 1. 基本的なセットアップ

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>レイヤーグループとコントロール</title>
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
    <h1>レイヤーグループとコントロール</h1>
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

## 2. レイヤーグループの基本

### シンプルなレイヤーグループ

```javascript
var map = L.map('map').setView([35.6812, 139.7671], 13);

L.tileLayer('https://tile.openstreetmap.org/{z}/{x}/{y}.png', {
    maxZoom: 19,
    attribution: '© OpenStreetMap contributors'
}).addTo(map);

// 複数のマーカーをグループ化
var cities = L.layerGroup([
    L.marker([35.6812, 139.7671]).bindPopup('東京'),
    L.marker([35.6895, 139.6917]).bindPopup('新宿'),
    L.marker([35.6762, 139.6503]).bindPopup('渋谷')
]);

// グループをマップに追加
cities.addTo(map);

// グループ全体を削除
// map.removeLayer(cities);
```

### レイヤーグループのメソッド

```javascript
var cities = L.layerGroup();

// レイヤーを追加
cities.addLayer(L.marker([35.6812, 139.7671]).bindPopup('東京'));
cities.addLayer(L.marker([35.6895, 139.6917]).bindPopup('新宿'));

// マップに追加
cities.addTo(map);

// 特定のレイヤーを削除
var marker = L.marker([35.6762, 139.6503]).bindPopup('渋谷');
cities.addLayer(marker);
cities.removeLayer(marker);

// すべてのレイヤーをクリア
cities.clearLayers();
```

## 3. レイヤーコントロールの追加

ユーザーがレイヤーを切り替えられるコントロールを追加します。

### 基本的なレイヤーコントロール

```javascript
var map = L.map('map').setView([35.6812, 139.7671], 13);

// ベースマップ（背景地図）の定義
var osmLayer = L.tileLayer('https://tile.openstreetmap.org/{z}/{x}/{y}.png', {
    maxZoom: 19,
    attribution: '© OpenStreetMap contributors'
});

var gsiLayer = L.tileLayer('https://cyberjapandata.gsi.go.jp/xyz/std/{z}/{x}/{y}.png', {
    maxZoom: 18,
    attribution: '<a href="https://maps.gsi.go.jp/development/ichiran.html">地理院タイル</a>'
});

// オーバーレイレイヤーの定義
var cities = L.layerGroup([
    L.marker([35.6812, 139.7671]).bindPopup('東京'),
    L.marker([35.6895, 139.6917]).bindPopup('新宿'),
    L.marker([35.6762, 139.6503]).bindPopup('渋谷')
]);

var landmarks = L.layerGroup([
    L.marker([35.6586, 139.7454]).bindPopup('東京タワー'),
    L.marker([35.7101, 139.8107]).bindPopup('東京スカイツリー')
]);

// デフォルトで表示するレイヤー
osmLayer.addTo(map);
cities.addTo(map);

// レイヤーコントロールを作成
var baseMaps = {
    "OpenStreetMap": osmLayer,
    "地理院地図": gsiLayer
};

var overlayMaps = {
    "都市": cities,
    "ランドマーク": landmarks
};

L.control.layers(baseMaps, overlayMaps).addTo(map);
```

### ベースマップとオーバーレイの違い

| タイプ | 説明 | 表示方法 |
|-------|------|---------|
| **ベースマップ** | 背景地図（一度に1つだけ表示） | ラジオボタン |
| **オーバーレイ** | 追加レイヤー（複数同時表示可） | チェックボックス |

## 4. 複数のベースマップ

さまざまな地図スタイルを用意：

```javascript
var map = L.map('map').setView([35.6812, 139.7671], 13);

// OpenStreetMap
var osm = L.tileLayer('https://tile.openstreetmap.org/{z}/{x}/{y}.png', {
    attribution: '© OpenStreetMap contributors'
});

// 地理院地図（標準）
var gsiStandard = L.tileLayer('https://cyberjapandata.gsi.go.jp/xyz/std/{z}/{x}/{y}.png', {
    attribution: '<a href="https://maps.gsi.go.jp/development/ichiran.html">地理院タイル</a>'
});

// 地理院地図（淡色）
var gsiPale = L.tileLayer('https://cyberjapandata.gsi.go.jp/xyz/pale/{z}/{x}/{y}.png', {
    attribution: '<a href="https://maps.gsi.go.jp/development/ichiran.html">地理院タイル</a>'
});

// OpenTopoMap
var openTopo = L.tileLayer('https://{s}.tile.opentopomap.org/{z}/{x}/{y}.png', {
    attribution: '© OpenTopoMap contributors'
});

// Esri WorldImagery（衛星写真）
var satellite = L.tileLayer('https://server.arcgisonline.com/ArcGIS/rest/services/World_Imagery/MapServer/tile/{z}/{y}/{x}', {
    attribution: 'Tiles © Esri'
});

// デフォルト表示
osm.addTo(map);

// レイヤーコントロール
var baseMaps = {
    "OpenStreetMap": osm,
    "地理院地図（標準）": gsiStandard,
    "地理院地図（淡色）": gsiPale,
    "地形図": openTopo,
    "衛星写真": satellite
};

L.control.layers(baseMaps, null, {
    position: 'topright',
    collapsed: false  // 常に展開表示
}).addTo(map);
```

## 5. カテゴリー別オーバーレイ

```javascript
var map = L.map('map').setView([35.6812, 139.7671], 12);

L.tileLayer('https://tile.openstreetmap.org/{z}/{x}/{y}.png', {
    maxZoom: 19,
    attribution: '© OpenStreetMap contributors'
}).addTo(map);

// レストランレイヤー
var restaurants = L.layerGroup([
    L.marker([35.6812, 139.7671]).bindPopup('レストランA'),
    L.marker([35.6850, 139.7700]).bindPopup('レストランB')
]);

// ホテルレイヤー
var hotels = L.layerGroup([
    L.marker([35.6762, 139.6503]).bindPopup('ホテルA'),
    L.marker([35.6895, 139.6917]).bindPopup('ホテルB')
]);

// 公園レイヤー
var parks = L.layerGroup([
    L.circle([35.7148, 139.7738], {
        color: 'green',
        fillColor: '#3f3',
        fillOpacity: 0.3,
        radius: 500
    }).bindPopup('上野公園'),
    L.circle([35.6852, 139.7528], {
        color: 'green',
        fillColor: '#3f3',
        fillOpacity: 0.3,
        radius: 1000
    }).bindPopup('皇居外苑')
]);

// 鉄道路線レイヤー
var railway = L.layerGroup([
    L.polyline([
        [35.6812, 139.7671],
        [35.6895, 139.6917],
        [35.6762, 139.6503]
    ], {color: 'blue', weight: 3}).bindPopup('山手線（一部）')
]);

var overlayMaps = {
    "🍴 レストラン": restaurants,
    "🏨 ホテル": hotels,
    "🌳 公園": parks,
    "🚃 鉄道": railway
};

L.control.layers(null, overlayMaps).addTo(map);
```

## 6. GeoJSONをレイヤーコントロールに追加

```javascript
var map = L.map('map').setView([35.6812, 139.7671], 11);

L.tileLayer('https://tile.openstreetmap.org/{z}/{x}/{y}.png', {
    maxZoom: 19,
    attribution: '© OpenStreetMap contributors'
}).addTo(map);

// GeoJSONデータ
var touristSpots = {
    "type": "FeatureCollection",
    "features": [
        {
            "type": "Feature",
            "properties": {"name": "東京タワー"},
            "geometry": {"type": "Point", "coordinates": [139.7454, 35.6586]}
        },
        {
            "type": "Feature",
            "properties": {"name": "東京スカイツリー"},
            "geometry": {"type": "Point", "coordinates": [139.8107, 35.7101]}
        }
    ]
};

var temples = {
    "type": "FeatureCollection",
    "features": [
        {
            "type": "Feature",
            "properties": {"name": "浅草寺"},
            "geometry": {"type": "Point", "coordinates": [139.7967, 35.7148]}
        }
    ]
};

// GeoJSONレイヤーを作成
var touristLayer = L.geoJSON(touristSpots, {
    onEachFeature: function(feature, layer) {
        layer.bindPopup(feature.properties.name);
    },
    pointToLayer: function(feature, latlng) {
        return L.circleMarker(latlng, {
            radius: 8,
            fillColor: "#ff7800",
            color: "#fff",
            weight: 2,
            opacity: 1,
            fillOpacity: 0.8
        });
    }
});

var templeLayer = L.geoJSON(temples, {
    onEachFeature: function(feature, layer) {
        layer.bindPopup(feature.properties.name);
    },
    pointToLayer: function(feature, latlng) {
        return L.circleMarker(latlng, {
            radius: 8,
            fillColor: "#9b59b6",
            color: "#fff",
            weight: 2,
            opacity: 1,
            fillOpacity: 0.8
        });
    }
});

var overlayMaps = {
    "観光スポット": touristLayer,
    "寺社": templeLayer
};

L.control.layers(null, overlayMaps).addTo(map);
```

## 7. レイヤーコントロールのオプション

```javascript
L.control.layers(baseMaps, overlayMaps, {
    position: 'topright',      // 位置: topleft, topright, bottomleft, bottomright
    collapsed: true,           // 折りたたみ表示（デフォルト: true）
    autoZIndex: true,          // 自動的にz-indexを管理
    hideSingleBase: false,     // ベースマップが1つの場合は非表示
    sortLayers: false,         // レイヤーをソート
    sortFunction: undefined    // カスタムソート関数
}).addTo(map);
```

### 常に展開表示する例

```javascript
L.control.layers(baseMaps, overlayMaps, {
    collapsed: false
}).addTo(map);
```

## 8. 動的なレイヤー管理

レイヤーコントロール作成後に、レイヤーを追加・削除する：

```javascript
var map = L.map('map').setView([35.6812, 139.7671], 13);

var osm = L.tileLayer('https://tile.openstreetmap.org/{z}/{x}/{y}.png', {
    attribution: '© OpenStreetMap contributors'
}).addTo(map);

var cities = L.layerGroup([
    L.marker([35.6812, 139.7671]).bindPopup('東京')
]).addTo(map);

// レイヤーコントロールを作成
var layerControl = L.control.layers(
    {"OpenStreetMap": osm},
    {"都市": cities}
).addTo(map);

// 後からレイヤーを追加
var landmarks = L.layerGroup([
    L.marker([35.6586, 139.7454]).bindPopup('東京タワー')
]);

layerControl.addOverlay(landmarks, "ランドマーク");

// ベースマップを追加
var gsi = L.tileLayer('https://cyberjapandata.gsi.go.jp/xyz/std/{z}/{x}/{y}.png', {
    attribution: '地理院タイル'
});

layerControl.addBaseLayer(gsi, "地理院地図");

// レイヤーを削除
// layerControl.removeLayer(landmarks);
```

## 9. レイヤーイベント

レイヤーの追加・削除時にイベントをハンドリング：

```javascript
map.on('overlayadd', function(e) {
    console.log('レイヤーが追加されました: ' + e.name);
});

map.on('overlayremove', function(e) {
    console.log('レイヤーが削除されました: ' + e.name);
});

map.on('baselayerchange', function(e) {
    console.log('ベースマップが変更されました: ' + e.name);
});
```

### 実用例：レイヤーに応じて表示範囲を調整

```javascript
var landmarksWithBounds = L.layerGroup([
    L.marker([35.6586, 139.7454]).bindPopup('東京タワー'),
    L.marker([35.7101, 139.8107]).bindPopup('東京スカイツリー')
]);

map.on('overlayadd', function(e) {
    if (e.name === 'ランドマーク') {
        // レイヤーに合わせて地図をズーム
        map.fitBounds(landmarksWithBounds.getBounds());
    }
});
```

## 10. 完全な実践例

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>東京マップ - レイヤーコントロール</title>
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css"
          integrity="sha256-p4NxAoJBhIIN+hmNHrzRCf9tD/miZyoHS5obTRR9BMY="
          crossorigin=""/>
    <style>
        body {
            margin: 0;
            padding: 0;
            font-family: Arial, sans-serif;
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
            padding: 10px;
            border-radius: 5px;
            box-shadow: 0 0 15px rgba(0,0,0,0.2);
        }
    </style>
</head>
<body>
    <div class="info-panel">
        <h3>東京観光マップ</h3>
        <p>右上のコントロールでレイヤーを切り替えられます</p>
    </div>
    <div id="map"></div>

    <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"
            integrity="sha256-20nQCchB9co0qIjJZRGuk2/Z9VM+kNiyxNV1lvTlZBo="
            crossorigin=""></script>
    <script>
        var map = L.map('map').setView([35.6812, 139.7671], 12);

        // ベースマップ
        var osm = L.tileLayer('https://tile.openstreetmap.org/{z}/{x}/{y}.png', {
            attribution: '© OpenStreetMap contributors'
        });

        var gsiStandard = L.tileLayer('https://cyberjapandata.gsi.go.jp/xyz/std/{z}/{x}/{y}.png', {
            attribution: '<a href="https://maps.gsi.go.jp/development/ichiran.html">地理院タイル</a>'
        });

        var gsiPale = L.tileLayer('https://cyberjapandata.gsi.go.jp/xyz/pale/{z}/{x}/{y}.png', {
            attribution: '<a href="https://maps.gsi.go.jp/development/ichiran.html">地理院タイル</a>'
        });

        // オーバーレイ - 観光地
        var touristSpots = L.layerGroup([
            L.marker([35.6586, 139.7454]).bindPopup('<b>東京タワー</b><br>高さ333m'),
            L.marker([35.7101, 139.8107]).bindPopup('<b>東京スカイツリー</b><br>高さ634m'),
            L.marker([35.7148, 139.7967]).bindPopup('<b>浅草寺</b><br>東京最古の寺')
        ]);

        // オーバーレイ - 主要駅
        var majorStations = L.layerGroup([
            L.circleMarker([35.6812, 139.7671], {
                radius: 8,
                fillColor: "#e74c3c",
                color: "#fff",
                weight: 2,
                fillOpacity: 0.8
            }).bindPopup('<b>東京駅</b>'),
            L.circleMarker([35.6895, 139.6917], {
                radius: 8,
                fillColor: "#e74c3c",
                color: "#fff",
                weight: 2,
                fillOpacity: 0.8
            }).bindPopup('<b>新宿駅</b>'),
            L.circleMarker([35.6762, 139.6503], {
                radius: 8,
                fillColor: "#e74c3c",
                color: "#fff",
                weight: 2,
                fillOpacity: 0.8
            }).bindPopup('<b>渋谷駅</b>')
        ]);

        // オーバーレイ - 公園
        var parks = L.layerGroup([
            L.circle([35.7148, 139.7738], {
                color: 'green',
                fillColor: '#2ecc71',
                fillOpacity: 0.3,
                radius: 500
            }).bindPopup('<b>上野公園</b>'),
            L.circle([35.6852, 139.7528], {
                color: 'green',
                fillColor: '#2ecc71',
                fillOpacity: 0.3,
                radius: 1000
            }).bindPopup('<b>皇居外苑</b>')
        ]);

        // デフォルトレイヤー
        osm.addTo(map);
        touristSpots.addTo(map);

        // レイヤーコントロール
        var baseMaps = {
            "OpenStreetMap": osm,
            "地理院地図（標準）": gsiStandard,
            "地理院地図（淡色）": gsiPale
        };

        var overlayMaps = {
            "🗼 観光地": touristSpots,
            "🚃 主要駅": majorStations,
            "🌳 公園": parks
        };

        L.control.layers(baseMaps, overlayMaps, {
            position: 'topright',
            collapsed: false
        }).addTo(map);

        // イベントリスナー
        map.on('overlayadd', function(e) {
            console.log('追加: ' + e.name);
        });

        map.on('overlayremove', function(e) {
            console.log('削除: ' + e.name);
        });
    </script>
</body>
</html>
```

## よくある質問

### Q: レイヤーコントロールが表示されない

**A:** `L.control.layers().addTo(map)`を忘れていませんか？また、ベースマップとオーバーレイの両方がnullの場合、コントロールは表示されません。

### Q: レイヤーグループとFeatureGroupの違いは？

**A:**
- **LayerGroup**：基本的なグループ化
- **FeatureGroup**：イベント処理とバウンディングボックス機能を持つ拡張版

```javascript
// FeatureGroupの例
var group = L.featureGroup([marker1, marker2]);
map.fitBounds(group.getBounds());  // すべての要素が見える範囲に調整
```

### Q: レイヤーの表示順序を制御したい

**A:** `setZIndex()`を使用：

```javascript
layer.setZIndex(1000);  // 高い値ほど前面に表示
```

## 💪 練習問題

レイヤー管理の理解を深めるための演習です。

### 演習1：カテゴリー別レイヤー切り替え（初級）

**課題：** 3種類のカテゴリー（飲食店、公園、駅）をレイヤーグループで管理し、レイヤーコントロールで切り替えできるようにしてください。

**要件：**
- 各カテゴリーに最低3つのマーカー
- 色分けされたアイコン
- レイヤーコントロールで表示/非表示を切り替え
- デフォルトですべて表示

<details>
<summary>💡 実装のヒント</summary>

```
1. カテゴリーごとにレイヤーグループを作成：
   var restaurants = L.layerGroup([...]);
   var parks = L.layerGroup([...]);
   var stations = L.layerGroup([...]);

2. カスタムアイコンで色分け：
   var redIcon = L.icon({iconUrl: '...'});
   var greenIcon = L.icon({iconUrl: '...'});

3. レイヤーコントロールに追加：
   var overlayMaps = {
       "飲食店": restaurants,
       "公園": parks,
       "駅": stations
   };
   L.control.layers(null, overlayMaps).addTo(map);

4. デフォルト表示：
   restaurants.addTo(map);
   parks.addTo(map);
   stations.addTo(map);
```
</details>

### 演習2：ベースマップとテーマの切り替え（中級）

**課題：** 複数のベースマップ（標準地図、衛星写真、地形図）を用意し、切り替えられるようにしてください。さらに、現在のベースマップに応じてオーバーレイの色を変更してください。

**要件：**
- 3種類以上のベースマップ
- ベースマップ切り替え時にイベント発火
- イベントに応じてオーバーレイのスタイルを変更
- 現在のベースマップ名を表示

<details>
<summary>💡 LLM活用プロンプト例</summary>

**ChatGPT/Claude等へのプロンプト：**

```
Leaflet.jsでベースマップの切り替えに応じてオーバーレイのスタイルを変更したいです。

【現在の状態】
- 複数のベースマップを用意済み（OSM、地理院標準、地理院淡色）
- レイヤーコントロールで切り替え可能

【実現したい機能】
1. ベースマップが変更されたらイベントを検知
2. 各ベースマップに適したオーバーレイスタイルを適用
   - OSM: 青い太線
   - 地理院標準: 赤い点線
   - 地理院淡色: 濃い緑の実線
3. 現在のベースマップ名を画面に表示

【技術的な要求】
- map.on('baselayerchange') イベントを使用
- e.name でベースマップ名を取得
- switch文または連想配列でスタイルを管理
- オーバーレイレイヤーの setStyle() で動的に変更

【コードスタイル】
- 日本語コメント
- スタイル設定は定数オブジェクトで管理
- 初心者にもわかりやすい実装
```

**実装のコアアイディア：**
```javascript
// スタイル定義
var layerStyles = {
    'OpenStreetMap': {
        color: 'blue',
        weight: 4,
        dashArray: null
    },
    '地理院地図（標準）': {
        color: 'red',
        weight: 3,
        dashArray: '5, 10'
    },
    '地理院地図（淡色）': {
        color: '#2d5016',
        weight: 4,
        dashArray: null
    }
};

// ベースマップ変更イベント
map.on('baselayerchange', function(e) {
    var styleName = e.name;
    var newStyle = layerStyles[styleName];

    if (newStyle && overlayLayer) {
        overlayLayer.setStyle(newStyle);
    }

    document.getElementById('current-basemap').textContent =
        '現在のベースマップ: ' + styleName;
});
```
</details>

### 演習3：階層的レイヤー管理システム（上級）

**課題：** 複数レベルのレイヤー階層（親カテゴリー、子カテゴリー）を管理し、親を切り替えると子も連動して切り替わるシステムを実装してください。

**要件：**
- 親カテゴリー（例：交通、施設、自然）
- 各親に複数の子カテゴリー（例：交通→駅、バス停、駐車場）
- 親のチェックボックスで子を一括オン/オフ
- カスタムコントロールUIで階層表示
- レイヤーの表示/非表示状態を LocalStorage に保存

<details>
<summary>💡 LLM活用プロンプト例</summary>

**段階的なアプローチ：**

```
【ステップ1: 階層データ構造の設計】
「Leaflet.jsで階層的なレイヤー管理システムのデータ構造を設計してください。

【データ構造例】
var layerHierarchy = {
    '交通': {
        '駅': L.layerGroup([...]),
        'バス停': L.layerGroup([...]),
        '駐車場': L.layerGroup([...])
    },
    '施設': {
        '学校': L.layerGroup([...]),
        '病院': L.layerGroup([...])
    }
};

この構造でレイヤーを管理する方法を教えてください。」

【ステップ2: カスタムコントロールUI】
「階層的なチェックボックスUIを持つカスタムコントロールを実装してください：
- 親カテゴリーのチェックボックス（太字）
- インデントされた子カテゴリーのチェックボックス
- 親をクリックすると子も連動
- L.Control.extend() を使用
- CSSで見やすくスタイリング」

【ステップ3: 連動ロジック】
「親チェックボックスの状態に応じて子レイヤーを一括制御するロジックを実装してください：
- 親がチェックON → すべての子をマップに追加
- 親がチェックOFF → すべての子をマップから削除
- 子の一部のみON → 親は中間状態（indeterminate）表示
- イベントリスナーで状態を管理」

【ステップ4: LocalStorage 永続化】
「レイヤーの表示/非表示状態をLocalStorageに保存し、再読み込み時に復元してください：
- オブジェクト構造を JSON.stringify()
- localStorage.setItem('layerState', ...)
- ページ読み込み時に復元
- デフォルト値の処理」
```

**重要な概念：**
```javascript
// 階層構造の管理
var layerManager = {
    layers: {},

    init: function(hierarchy) {
        this.layers = hierarchy;
    },

    toggleParent: function(parentName, show) {
        var parent = this.layers[parentName];
        if (!parent) return;

        for (var childName in parent) {
            var childLayer = parent[childName];
            if (show) {
                map.addLayer(childLayer);
            } else {
                map.removeLayer(childLayer);
            }
        }
        this.saveState();
    },

    saveState: function() {
        var state = {};
        for (var parent in this.layers) {
            state[parent] = {};
            for (var child in this.layers[parent]) {
                state[parent][child] = map.hasLayer(this.layers[parent][child]);
            }
        }
        localStorage.setItem('layerState', JSON.stringify(state));
    }
};
```
</details>

### 🎓 学習のポイント

**レイヤー管理の考え方：**

1. **階層化**: 関連するレイヤーをグループ化
2. **命名規則**: 一貫性のある名前で管理
3. **イベント駆動**: レイヤー切り替えでイベント発火
4. **パフォーマンス**: 不要なレイヤーは削除、必要に応じて再追加

**デバッグのコツ：**
- `map.eachLayer()` で現在のレイヤーを確認
- `console.log(map.hasLayer(layer))` でレイヤー存在確認
- ブラウザDevToolsでDOM要素を検査
- レイヤーに名前プロパティを付けて識別

### 📝 LLM活用：レイヤー管理の高度なプロンプト

**レイヤーの動的生成：**
```
「Leaflet.jsでJSONデータから動的にレイヤーグループとコントロールを生成したいです。

【入力データ】
var layerConfig = [
    {
        name: "レストラン",
        type: "marker",
        data: [{lat: 35.68, lng: 139.76, name: "店A"}, ...],
        icon: "red-marker.png"
    },
    {
        name: "公園",
        type: "polygon",
        data: [[...coordinates...]],
        color: "green"
    }
];

【要件】
1. 各設定からレイヤーグループを自動生成
2. タイプ（marker/polygon/polyline）に応じて適切なL.layerオブジェクトを作成
3. 生成したレイヤーをレイヤーコントロールに追加
4. 拡張可能な設計

実装例を教えてください。」
```

**条件付きレイヤー表示：**
```
「ズームレベルに応じてレイヤーの表示/非表示を自動的に切り替えたいです。

【シナリオ】
- ズーム 0-10: 都道府県レベルのデータ
- ズーム 11-14: 市区町村レベルのデータ
- ズーム 15-19: 詳細な建物データ

【技術的な要求】
- map.on('zoomend') で監視
- 現在のズームレベルに応じてレイヤーを切り替え
- パフォーマンスを考慮（不要なレイヤーは完全に削除）
- ズーム閾値は設定可能に

実装とベストプラクティスを教えてください。」
```

## 次のステップ

- [06_choropleth_map.md](06_choropleth_map.md)：インタラクティブなコロプレスマップ
- [07_wms_tms.md](07_wms_tms.md)：WMS/TMSサービスの統合

## 参考リンク

- [Leaflet Layer Groups](https://leafletjs.com/reference.html#layergroup)
- [Leaflet Layers Control](https://leafletjs.com/reference.html#control-layers)
- [Leaflet Layers Control Tutorial](https://leafletjs.com/examples/layers-control/)
