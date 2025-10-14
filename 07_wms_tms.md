# WMSとTMSサービスの統合

**難易度：中級〜上級** ⭐⭐⭐⭐

## このチュートリアルで学ぶこと

- WMS（Web Map Service）の基礎
- TMS（Tile Map Service）の基礎
- WMSレイヤーの追加方法
- TMSレイヤーの使用
- レイヤーパラメータのカスタマイズ
- 実用的なWMS/TMSサービスの活用

## 必要な前提知識

- [01_quick_start.md](01_quick_start.md)の基礎知識
- [05_layer_groups_control.md](05_layer_groups_control.md)のレイヤー管理

## WMSとTMSとは？

### WMS（Web Map Service）

OGC（Open Geospatial Consortium）が定義した標準規格で、サーバーから地図画像を配信します。

**特徴**：
- リクエストごとに動的に画像を生成
- 複数のレイヤーを組み合わせ可能
- カスタマイズ可能なパラメータ

### TMS（Tile Map Service）

事前に生成されたタイル画像を配信するサービス。

**特徴**：
- 高速（事前生成済み）
- キャッシュに適している
- 固定のズームレベルとタイルサイズ

### タイルレイヤーとの違い

Leafletの`L.tileLayer()`は基本的にTMS形式をサポート。WMSは`L.tileLayer.wms()`を使用します。

## 1. 基本的なセットアップ

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>WMS/TMS統合</title>
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
    <h1>WMS/TMS統合</h1>
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

## 2. WMSレイヤーの基本

### シンプルなWMSレイヤー

```javascript
var map = L.map('map').setView([35.6812, 139.7671], 10);

// ベースマップ
L.tileLayer('https://tile.openstreetmap.org/{z}/{x}/{y}.png', {
    maxZoom: 19,
    attribution: '© OpenStreetMap contributors'
}).addTo(map);

// WMSレイヤーを追加
var wmsLayer = L.tileLayer.wms('https://example.com/wms', {
    layers: 'layer_name',
    format: 'image/png',
    transparent: true,
    attribution: 'WMS Data Provider'
}).addTo(map);
```

### WMSのパラメータ

| パラメータ | 説明 | 必須 |
|----------|------|------|
| `layers` | 表示するレイヤー名 | ✓ |
| `format` | 画像フォーマット（png, jpeg） | 推奨 |
| `transparent` | 背景透過 | 任意 |
| `version` | WMSバージョン（1.1.1, 1.3.0） | 任意 |
| `crs` | 座標参照系 | 任意 |
| `styles` | スタイル名 | 任意 |

## 3. 日本の地理院WMS

国土地理院が提供するWMSサービスを使用：

```javascript
var map = L.map('map').setView([35.6812, 139.7671], 10);

// ベースマップ
L.tileLayer('https://tile.openstreetmap.org/{z}/{x}/{y}.png', {
    maxZoom: 19,
    attribution: '© OpenStreetMap contributors'
}).addTo(map);

// 地理院WMS - 標高タイル
var gsiRelief = L.tileLayer.wms('https://cyberjapandata.gsi.go.jp/xyz/relief/{z}/{x}/{y}.png', {
    layers: 'relief',
    format: 'image/png',
    transparent: true,
    attribution: '<a href="https://maps.gsi.go.jp/">国土地理院</a>'
});

// 地理院WMS - 陰影起伏図
var gsiHillshade = L.tileLayer.wms('https://cyberjapandata.gsi.go.jp/xyz/hillshademap/{z}/{x}/{y}.png', {
    layers: 'hillshademap',
    format: 'image/png',
    transparent: false,
    attribution: '<a href="https://maps.gsi.go.jp/">国土地理院</a>'
});

// レイヤーコントロール
var overlayMaps = {
    "標高タイル": gsiRelief,
    "陰影起伏図": gsiHillshade
};

L.control.layers(null, overlayMaps).addTo(map);
```

## 4. 複数のWMSレイヤーを重ねる

```javascript
var map = L.map('map').setView([35.6812, 139.7671], 12);

// ベースマップ
var baseMap = L.tileLayer('https://{s}.basemaps.cartocdn.com/light_all/{z}/{x}/{y}{r}.png', {
    attribution: '© OpenStreetMap contributors © CARTO',
    subdomains: 'abcd',
    maxZoom: 19
}).addTo(map);

// WMSレイヤー1: 道路
var roadsWMS = L.tileLayer.wms('https://example.com/wms', {
    layers: 'roads',
    format: 'image/png',
    transparent: true,
    opacity: 0.7
});

// WMSレイヤー2: 建物
var buildingsWMS = L.tileLayer.wms('https://example.com/wms', {
    layers: 'buildings',
    format: 'image/png',
    transparent: true,
    opacity: 0.8
});

// WMSレイヤー3: 土地利用
var landUseWMS = L.tileLayer.wms('https://example.com/wms', {
    layers: 'landuse',
    format: 'image/png',
    transparent: true,
    opacity: 0.5
});

var overlayMaps = {
    "道路": roadsWMS,
    "建物": buildingsWMS,
    "土地利用": landUseWMS
};

L.control.layers(null, overlayMaps).addTo(map);
```

## 5. カスタムパラメータの追加

WMSリクエストにカスタムパラメータを追加：

```javascript
var wmsLayer = L.tileLayer.wms('https://example.com/wms', {
    layers: 'layer_name',
    format: 'image/png',
    transparent: true,
    version: '1.3.0',
    crs: L.CRS.EPSG4326,
    styles: 'custom_style',
    // カスタムパラメータ
    time: '2024-01-01',
    elevation: '1000',
    custom_param: 'value'
}).addTo(map);
```

## 6. GetFeatureInfoの実装

WMSレイヤーをクリックして情報を取得：

```javascript
var map = L.map('map').setView([35.6812, 139.7671], 10);

L.tileLayer('https://tile.openstreetmap.org/{z}/{x}/{y}.png', {
    maxZoom: 19,
    attribution: '© OpenStreetMap contributors'
}).addTo(map);

var wmsLayer = L.tileLayer.wms('https://example.com/wms', {
    layers: 'layer_name',
    format: 'image/png',
    transparent: true
}).addTo(map);

// クリックイベント
map.on('click', function(e) {
    var latlng = e.latlng;
    var point = map.latLngToContainerPoint(latlng);
    var size = map.getSize();

    var params = {
        request: 'GetFeatureInfo',
        service: 'WMS',
        version: '1.1.1',
        layers: 'layer_name',
        query_layers: 'layer_name',
        styles: '',
        bbox: map.getBounds().toBBoxString(),
        width: size.x,
        height: size.y,
        info_format: 'application/json',
        x: Math.round(point.x),
        y: Math.round(point.y)
    };

    var url = 'https://example.com/wms?' + L.Util.getParamString(params);

    fetch(url)
        .then(response => response.json())
        .then(data => {
            console.log('Feature Info:', data);
            // ポップアップで表示
            L.popup()
                .setLatLng(latlng)
                .setContent('<pre>' + JSON.stringify(data, null, 2) + '</pre>')
                .openOn(map);
        })
        .catch(error => {
            console.error('GetFeatureInfo エラー:', error);
        });
});
```

## 7. TMS（Tile Map Service）

TMSは事前生成されたタイルを使用します。

```javascript
var map = L.map('map').setView([35.6812, 139.7671], 10);

// 標準のタイルレイヤー（TMS形式）
L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
    maxZoom: 19,
    attribution: '© OpenStreetMap contributors'
}).addTo(map);

// TMS形式のカスタムタイル
var customTMS = L.tileLayer('https://example.com/tiles/{z}/{x}/{y}.png', {
    tms: true,  // TMS座標系を使用
    maxZoom: 18,
    attribution: 'Custom TMS Provider'
});
```

### TMSとXYZの違い

- **XYZ**：Y座標が上から下（標準）
- **TMS**：Y座標が下から上

```javascript
// TMSレイヤーの場合
var tmsLayer = L.tileLayer('https://example.com/tms/{z}/{x}/{y}.png', {
    tms: true  // Y座標を反転
});
```

## 8. 実用例：地理院タイルの活用

```javascript
var map = L.map('map').setView([35.6812, 139.7671], 10);

// ベースマップ - 地理院標準地図
var gsiStandard = L.tileLayer('https://cyberjapandata.gsi.go.jp/xyz/std/{z}/{x}/{y}.png', {
    attribution: '<a href="https://maps.gsi.go.jp/development/ichiran.html">地理院タイル</a>',
    maxZoom: 18
});

// 地理院淡色地図
var gsiPale = L.tileLayer('https://cyberjapandata.gsi.go.jp/xyz/pale/{z}/{x}/{y}.png', {
    attribution: '<a href="https://maps.gsi.go.jp/development/ichiran.html">地理院タイル</a>',
    maxZoom: 18
});

// 航空写真
var gsiPhoto = L.tileLayer('https://cyberjapandata.gsi.go.jp/xyz/seamlessphoto/{z}/{x}/{y}.jpg', {
    attribution: '<a href="https://maps.gsi.go.jp/development/ichiran.html">地理院タイル</a>',
    maxZoom: 18
});

// オーバーレイ - 陰影起伏図
var gsiRelief = L.tileLayer('https://cyberjapandata.gsi.go.jp/xyz/relief/{z}/{x}/{y}.png', {
    attribution: '<a href="https://maps.gsi.go.jp/development/ichiran.html">地理院タイル</a>',
    opacity: 0.5,
    maxZoom: 15
});

// オーバーレイ - 傾斜量図
var gsiSlope = L.tileLayer('https://cyberjapandata.gsi.go.jp/xyz/slopemap/{z}/{x}/{y}.png', {
    attribution: '<a href="https://maps.gsi.go.jp/development/ichiran.html">地理院タイル</a>',
    opacity: 0.7,
    maxZoom: 15
});

// デフォルト表示
gsiStandard.addTo(map);

// レイヤーコントロール
var baseMaps = {
    "標準地図": gsiStandard,
    "淡色地図": gsiPale,
    "航空写真": gsiPhoto
};

var overlayMaps = {
    "陰影起伏図": gsiRelief,
    "傾斜量図": gsiSlope
};

L.control.layers(baseMaps, overlayMaps).addTo(map);
```

## 9. WMTSレイヤー（発展）

WMTS（Web Map Tile Service）はWMSのタイル版です。

```javascript
// LeafletでWMTSを使用する場合、プラグインが必要
// または、URLを直接指定
var wmtsLayer = L.tileLayer('https://example.com/wmts/layer/{TileMatrix}/{TileRow}/{TileCol}.png', {
    attribution: 'WMTS Provider'
}).addTo(map);
```

## 10. 完全な実践例

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>地理院タイル統合マップ</title>
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
        .info-box {
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
        .info-box h3 {
            margin-top: 0;
        }
    </style>
</head>
<body>
    <div class="info-box">
        <h3>地理院タイルマップ</h3>
        <p>右上のコントロールで地図タイルとオーバーレイを切り替えられます。</p>
        <ul>
            <li><strong>標準地図</strong>: 基本的な地図</li>
            <li><strong>淡色地図</strong>: 薄い色の地図</li>
            <li><strong>航空写真</strong>: 衛星画像</li>
            <li><strong>陰影起伏図</strong>: 地形の起伏</li>
        </ul>
    </div>
    <div id="map"></div>

    <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"
            integrity="sha256-20nQCchB9co0qIjJZRGuk2/Z9VM+kNiyxNV1lvTlZBo="
            crossorigin=""></script>
    <script>
        var map = L.map('map').setView([35.6812, 139.7671], 11);

        // ベースマップ
        var gsiStandard = L.tileLayer('https://cyberjapandata.gsi.go.jp/xyz/std/{z}/{x}/{y}.png', {
            attribution: '<a href="https://maps.gsi.go.jp/development/ichiran.html">地理院タイル</a>',
            maxZoom: 18
        });

        var gsiPale = L.tileLayer('https://cyberjapandata.gsi.go.jp/xyz/pale/{z}/{x}/{y}.png', {
            attribution: '<a href="https://maps.gsi.go.jp/development/ichiran.html">地理院タイル</a>',
            maxZoom: 18
        });

        var gsiPhoto = L.tileLayer('https://cyberjapandata.gsi.go.jp/xyz/seamlessphoto/{z}/{x}/{y}.jpg', {
            attribution: '<a href="https://maps.gsi.go.jp/development/ichiran.html">地理院タイル</a>',
            maxZoom: 18
        });

        var osm = L.tileLayer('https://tile.openstreetmap.org/{z}/{x}/{y}.png', {
            attribution: '© OpenStreetMap contributors',
            maxZoom: 19
        });

        // オーバーレイ
        var gsiRelief = L.tileLayer('https://cyberjapandata.gsi.go.jp/xyz/relief/{z}/{x}/{y}.png', {
            attribution: '<a href="https://maps.gsi.go.jp/development/ichiran.html">地理院タイル</a>',
            opacity: 0.6,
            maxZoom: 15
        });

        var gsiSlope = L.tileLayer('https://cyberjapandata.gsi.go.jp/xyz/slopemap/{z}/{x}/{y}.png', {
            attribution: '<a href="https://maps.gsi.go.jp/development/ichiran.html">地理院タイル</a>',
            opacity: 0.7,
            maxZoom: 15
        });

        var gsiHillshade = L.tileLayer('https://cyberjapandata.gsi.go.jp/xyz/hillshademap/{z}/{x}/{y}.png', {
            attribution: '<a href="https://maps.gsi.go.jp/development/ichiran.html">地理院タイル</a>',
            opacity: 0.5,
            maxZoom: 16
        });

        // デフォルト表示
        gsiStandard.addTo(map);

        // レイヤーコントロール
        var baseMaps = {
            "📍 標準地図": gsiStandard,
            "🗺️ 淡色地図": gsiPale,
            "🛰️ 航空写真": gsiPhoto,
            "🌍 OpenStreetMap": osm
        };

        var overlayMaps = {
            "⛰️ 陰影起伏図": gsiRelief,
            "📊 傾斜量図": gsiSlope,
            "🏔️ 陰影図": gsiHillshade
        };

        L.control.layers(baseMaps, overlayMaps, {
            position: 'topright',
            collapsed: false
        }).addTo(map);

        // スケールコントロール
        L.control.scale({
            imperial: false,
            metric: true
        }).addTo(map);
    </script>
</body>
</html>
```

## よくある質問

### Q: WMSレイヤーが表示されない

**A:** 以下を確認：
1. WMS URLが正しいか
2. `layers`パラメータが正しいか
3. CORS（クロスオリジン）の問題がないか
4. ブラウザの開発者ツールでエラーを確認

### Q: WMSとタイルレイヤーの違いは？

**A:**
- **タイルレイヤー**：事前生成、高速、固定スタイル
- **WMS**：動的生成、カスタマイズ可能、やや遅い

### Q: パフォーマンスを改善するには？

**A:**
1. 可能な限りタイルレイヤーを使用
2. WMSの場合、画像フォーマットをJPEGに（透過不要時）
3. キャッシュを活用
4. 不要なレイヤーは非表示に

## 次のステップ

- [08_overlays.md](08_overlays.md)：画像、ビデオ、SVGオーバーレイ
- [09_map_panes.md](09_map_panes.md)：マップペインの操作

## 参考リンク

- [Leaflet WMS/TMS Documentation](https://leafletjs.com/reference.html#tilelayer-wms)
- [OGC WMS Specification](https://www.ogc.org/standards/wms)
- [国土地理院タイル一覧](https://maps.gsi.go.jp/development/ichiran.html)
- [OpenLayers](https://openlayers.org/)：WMS/WMTSの高度な機能
