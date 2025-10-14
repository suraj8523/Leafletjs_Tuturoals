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

## 💪 練習問題

WMS/TMSサービスの統合理解を深めるための演習です。

### 演習1：複数のWMSレイヤーの切り替え（初級）

**課題：** 国土地理院の異なるWMSレイヤー（標準地図、淡色地図、写真）をレイヤーコントロールで切り替えられるようにしてください。

**要件：**
- 3種類の地理院タイルをベースマップとして登録
- レイヤーコントロールで切り替え
- 各レイヤーに適切な attribution を設定
- デフォルトは標準地図を表示

<details>
<summary>💡 実装のヒント</summary>

```
1. 各タイルレイヤーを定義：
   var gsiStandard = L.tileLayer('https://cyberjapandata.gsi.go.jp/xyz/std/{z}/{x}/{y}.png', {...});
   var gsiPale = L.tileLayer('https://cyberjapandata.gsi.go.jp/xyz/pale/{z}/{x}/{y}.png', {...});
   var gsiPhoto = L.tileLayer('https://cyberjapandata.gsi.go.jp/xyz/seamlessphoto/{z}/{x}/{y}.jpg', {...});

2. baseMaps オブジェクトにまとめる

3. L.control.layers(baseMaps, null).addTo(map);

4. デフォルト表示: gsiStandard.addTo(map);
```
</details>

### 演習2：WMSパラメータのカスタマイズ（中級）

**課題：** WMSレイヤーに時系列パラメータを追加し、スライダーで時間を変更できる機能を実装してください。

**要件：**
- TIME パラメータをサポートするWMSサービスを使用
- HTMLスライダーで時間を選択
- スライダー変更時にWMSレイヤーを更新
- 現在選択中の時間を表示

<details>
<summary>💡 LLM活用プロンプト例</summary>

**ChatGPT/Claude等へのプロンプト：**

```
Leaflet.jsのWMSレイヤーで時系列データを表示し、スライダーで時間を変更したいです。

【現在の状態】
- 基本的なWMSレイヤーは表示済み
- WMSサービスは TIME パラメータをサポート

【実現したい機能】
1. HTML range input で時間を選択（例: 2024-01-01 から 2024-12-31）
2. スライダー変更時にWMSのTIMEパラメータを更新
3. WMSレイヤーを再描画
4. 選択中の日付をテキスト表示

【技術的な要求】
- L.tileLayer.wms() のパラメータを動的に変更
- wmsLayer.setParams({time: newTime}) で更新
- 日付フォーマットは ISO 8601 (YYYY-MM-DD)
- スライダー値（0-365）を日付に変換

【コードスタイル】
- 日本語コメント
- 日付計算は関数で分離
- 初心者にもわかりやすい実装
```

**実装のコアアイディア：**
```javascript
var wmsLayer = L.tileLayer.wms('https://example.com/wms', {
    layers: 'temperature',
    format: 'image/png',
    transparent: true,
    time: '2024-01-01'
}).addTo(map);

var slider = document.getElementById('timeSlider');
var dateDisplay = document.getElementById('currentDate');

slider.addEventListener('input', function(e) {
    var days = parseInt(e.target.value);
    var date = new Date('2024-01-01');
    date.setDate(date.getDate() + days);

    var dateString = date.toISOString().split('T')[0];

    wmsLayer.setParams({
        time: dateString
    }, false);  // noRedraw=false で即座に更新

    dateDisplay.textContent = dateString;
});
```
</details>

### 演習3：複数WMSレイヤーの透明度制御（上級）

**課題：** 複数のWMSレイヤーを重ねて表示し、各レイヤーの透明度を個別にスライダーで制御できるシステムを実装してください。

**要件：**
- 3つのWMSレイヤー（地形、道路、建物など）
- 各レイヤー用の透明度スライダー
- チェックボックスでレイヤーのオン/オフ
- 透明度の値（0-100%）を表示
- 設定をLocalStorageに保存

<details>
<summary>💡 LLM活用プロンプト例</summary>

**段階的なアプローチ：**

```
【ステップ1: レイヤー管理構造】
「複数のWMSレイヤーと設定を管理するオブジェクト構造を設計してください。

【データ構造】
var wmsLayers = {
    'terrain': {
        layer: L.tileLayer.wms(...),
        opacity: 1.0,
        visible: true
    },
    'roads': {
        layer: L.tileLayer.wms(...),
        opacity: 0.7,
        visible: true
    }
};

この構造で透明度と表示/非表示を管理する方法を教えてください。」

【ステップ2: UI作成】
「各WMSレイヤー用のコントロールパネルを動的に生成してください：
- レイヤー名のチェックボックス
- 透明度スライダー（0-100）
- 現在の透明度表示
- for...in ループで wmsLayers から自動生成
- CSSでスタイリング」

【ステップ3: イベント処理】
「スライダーとチェックボックスのイベント処理を実装してください：
- スライダー変更時に layer.setOpacity()
- チェックボックス変更時に map.addLayer() / removeLayer()
- リアルタイムで透明度値を更新表示
- デバウンス処理は不要」

【ステップ4: 永続化】
「レイヤー設定をLocalStorageに保存し、復元してください：
- 各変更時に自動保存
- ページ読み込み時に復元
- JSONとして保存（opacity, visible）
- 保存失敗時のエラーハンドリング」
```

**重要な概念：**
```javascript
var wmsConfig = {
    'terrain': {
        url: 'https://example.com/wms',
        options: {layers: 'terrain', transparent: true},
        opacity: 1.0,
        visible: true
    }
};

function initializeWMSLayers() {
    for (var key in wmsConfig) {
        var config = wmsConfig[key];
        var layer = L.tileLayer.wms(config.url, config.options);

        layer.setOpacity(config.opacity);
        if (config.visible) {
            layer.addTo(map);
        }

        wmsConfig[key].layer = layer;

        createLayerControl(key, config);
    }
}

function updateOpacity(layerKey, opacity) {
    var config = wmsConfig[layerKey];
    config.opacity = opacity;
    config.layer.setOpacity(opacity);
    saveToLocalStorage();
}

function saveToLocalStorage() {
    var settings = {};
    for (var key in wmsConfig) {
        settings[key] = {
            opacity: wmsConfig[key].opacity,
            visible: wmsConfig[key].visible
        };
    }
    localStorage.setItem('wmsSettings', JSON.stringify(settings));
}
```
</details>

### 🎓 学習のポイント

**WMS/TMSの使い分け：**

1. **WMS**: 動的、カスタマイズ可能、サーバー負荷高
2. **TMS**: 高速、キャッシュ可能、固定スタイル
3. **選択基準**: リアルタイム性 vs パフォーマンス

**パフォーマンス最適化：**
- タイルのキャッシュ戦略
- 不要なレイヤーは削除
- 画像フォーマットの選択（PNG vs JPEG）
- maxZoom 設定でリクエスト制限

### 📝 LLM活用：外部サービス統合のプロンプト

**CORS問題の解決：**
```
「LeafletでWMSレイヤーを読み込む際にCORSエラーが発生します。

【エラーメッセージ】
Access to XMLHttpRequest at 'https://example.com/wms' from origin 'http://localhost'
has been blocked by CORS policy

【状況】
- ローカル開発環境で実行中
- 外部のWMSサービスを使用
- ブラウザはChrome

【質問】
1. CORS問題の原因と仕組み
2. 開発環境での回避方法（プロキシサーバーなど）
3. 本番環境での対処法
4. Leafletでの設定方法

実用的な解決策を教えてください。」
```

**GetFeatureInfo の実装：**
```
「LeafletのWMSレイヤーをクリックして、その地点の詳細情報を取得したいです。

【技術的な要求】
- WMS GetFeatureInfo リクエストを使用
- クリック座標からピクセル座標を計算
- XMLまたはJSONレスポンスをパース
- ポップアップで情報を表示

【知りたいこと】
1. GetFeatureInfo リクエストのパラメータ構築方法
2. Leafletでの座標変換（LatLng → Pixel）
3. レスポンス形式の選択（GML, JSON, HTML）
4. エラーハンドリング

実装例とベストプラクティスを教えてください。」
```

## 次のステップ

- [08_overlays.md](08_overlays.md)：画像、ビデオ、SVGオーバーレイ
- [09_map_panes.md](09_map_panes.md)：マップペインの操作

## 参考リンク

- [Leaflet WMS/TMS Documentation](https://leafletjs.com/reference.html#tilelayer-wms)
- [OGC WMS Specification](https://www.ogc.org/standards/wms)
- [国土地理院タイル一覧](https://maps.gsi.go.jp/development/ichiran.html)
- [OpenLayers](https://openlayers.org/)：WMS/WMTSの高度な機能
