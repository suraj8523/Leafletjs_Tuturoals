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

## 次のステップ

- [06_choropleth_map.md](06_choropleth_map.md)：インタラクティブなコロプレスマップ
- [07_wms_tms.md](07_wms_tms.md)：WMS/TMSサービスの統合

## 参考リンク

- [Leaflet Layer Groups](https://leafletjs.com/reference.html#layergroup)
- [Leaflet Layers Control](https://leafletjs.com/reference.html#control-layers)
- [Leaflet Layers Control Tutorial](https://leafletjs.com/examples/layers-control/)
