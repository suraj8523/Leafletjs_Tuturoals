# モバイル対応のフルスクリーンマップ

**難易度：初心者向け** ⭐⭐

## このチュートリアルで学ぶこと

- フルスクリーンマップの作成方法
- モバイルデバイスでの表示最適化
- ユーザーの現在位置の取得と表示
- 位置情報の追跡（リアルタイム更新）
- レスポンシブデザインの実装

## 必要な前提知識

- [01_quick_start.md](01_quick_start.md)の基礎知識

## モバイル対応が重要な理由

多くのユーザーがスマートフォンやタブレットで地図を利用します。モバイル対応することで：

- より良いユーザー体験を提供
- 画面サイズに応じた最適な表示
- 現在位置の活用によるパーソナライズ
- タッチ操作への対応

## 1. フルスクリーンマップの基本

### 基本的なHTML構造

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>モバイル対応マップ</title>

    <!-- Leaflet CSS -->
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css"
          integrity="sha256-p4NxAoJBhIIN+hmNHrzRCf9tD/miZyoHS5obTRR9BMY="
          crossorigin=""/>

    <style>
        /* ページ全体のマージンとパディングをリセット */
        body {
            padding: 0;
            margin: 0;
        }
        html, body, #map {
            height: 100%;
            width: 100%;
        }
    </style>
</head>
<body>
    <!-- マップがページ全体を占める -->
    <div id="map"></div>

    <!-- Leaflet JavaScript -->
    <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"
            integrity="sha256-20nQCchB9co0qIjJZRGuk2/Z9VM+kNiyxNV1lvTlZBo="
            crossorigin=""></script>
    <script>
        // 地図の初期化
        var map = L.map('map').setView([35.6812, 139.7671], 13);

        L.tileLayer('https://tile.openstreetmap.org/{z}/{x}/{y}.png', {
            maxZoom: 19,
            attribution: '© OpenStreetMap contributors'
        }).addTo(map);
    </script>
</body>
</html>
```

### ビューポートメタタグの重要な設定

```html
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
```

| 属性 | 説明 |
|------|------|
| `width=device-width` | 画面の幅をデバイスの幅に合わせる |
| `initial-scale=1.0` | 初期ズームレベルを100%に設定 |
| `maximum-scale=1.0` | 最大ズームを100%に制限（オプション） |
| `user-scalable=no` | ピンチズームを無効化（オプション） |

**注意**：`user-scalable=no`はアクセシビリティの観点から推奨されない場合があります。

## 2. 現在位置を取得する

### 基本的な位置情報の取得

```javascript
var map = L.map('map').setView([35.6812, 139.7671], 13);

L.tileLayer('https://tile.openstreetmap.org/{z}/{x}/{y}.png', {
    maxZoom: 19,
    attribution: '© OpenStreetMap contributors'
}).addTo(map);

// 現在位置を取得
map.locate({setView: true, maxZoom: 16});
```

### `locate()`メソッドのオプション

| オプション | 説明 | デフォルト |
|-----------|------|-----------|
| `setView` | 自動的に現在位置に地図を移動 | false |
| `maxZoom` | 位置表示時の最大ズームレベル | Infinity |
| `watch` | 位置を継続的に追跡 | false |
| `enableHighAccuracy` | 高精度モードを使用 | false |
| `timeout` | タイムアウト時間（ミリ秒） | 10000 |
| `maximumAge` | キャッシュされた位置の有効期限 | 0 |

## 3. 位置情報のイベント処理

### 成功時の処理

```javascript
// 位置情報取得を開始
map.locate({setView: true, maxZoom: 16});

// 位置情報取得成功時
function onLocationFound(e) {
    var radius = e.accuracy / 2;

    // 現在位置にマーカーを追加
    L.marker(e.latlng).addTo(map)
        .bindPopup("現在位置から" + radius.toFixed(0) + "m以内にいます").openPopup();

    // 精度を示す円を追加
    L.circle(e.latlng, radius).addTo(map);
}

map.on('locationfound', onLocationFound);

// 位置情報取得失敗時
function onLocationError(e) {
    alert(e.message);
}

map.on('locationerror', onLocationError);
```

### イベントオブジェクトの内容

`locationfound`イベントで取得できる情報：

```javascript
{
    latlng: LatLng,        // 緯度・経度
    bounds: LatLngBounds,  // 精度範囲
    accuracy: Number,      // 精度（メートル）
    altitude: Number,      // 高度（メートル）
    altitudeAccuracy: Number, // 高度の精度
    heading: Number,       // 方向（度）
    speed: Number,         // 速度（m/s）
    timestamp: Number      // タイムスタンプ
}
```

## 4. リアルタイム位置追跡

ユーザーの移動をリアルタイムで追跡する場合：

```javascript
var map = L.map('map').setView([35.6812, 139.7671], 13);

L.tileLayer('https://tile.openstreetmap.org/{z}/{x}/{y}.png', {
    maxZoom: 19,
    attribution: '© OpenStreetMap contributors'
}).addTo(map);

var currentMarker = null;
var accuracyCircle = null;

// リアルタイム追跡を開始
map.locate({
    watch: true,              // 継続的に追跡
    enableHighAccuracy: true, // 高精度モード
    setView: true,
    maxZoom: 16
});

map.on('locationfound', function(e) {
    // 既存のマーカーと円を削除
    if (currentMarker) {
        map.removeLayer(currentMarker);
    }
    if (accuracyCircle) {
        map.removeLayer(accuracyCircle);
    }

    // 新しいマーカーと円を追加
    currentMarker = L.marker(e.latlng).addTo(map)
        .bindPopup("現在位置<br>精度: " + e.accuracy.toFixed(0) + "m");

    accuracyCircle = L.circle(e.latlng, e.accuracy / 2).addTo(map);
});

map.on('locationerror', function(e) {
    console.error('位置情報エラー:', e.message);
});
```

### 追跡を停止する

```javascript
// 位置追跡を停止
map.stopLocate();
```

## 5. カスタムコントロールの追加

位置情報ボタンを追加して、ユーザーが手動で位置を更新できるようにします。

```javascript
// カスタムコントロールを定義
L.Control.MyLocation = L.Control.extend({
    onAdd: function(map) {
        var container = L.DomUtil.create('div', 'leaflet-bar leaflet-control leaflet-control-custom');

        container.style.backgroundColor = 'white';
        container.style.width = '34px';
        container.style.height = '34px';
        container.style.cursor = 'pointer';
        container.style.backgroundImage = 'url(data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHdpZHRoPSIyNCIgaGVpZ2h0PSIyNCIgdmlld0JveD0iMCAwIDI0IDI0Ij48cGF0aCBkPSJNMTIgOGMtMi4yMSAwLTQgMS43OS00IDRzMS43OSA0IDQgNCA0LTEuNzkgNC00LTEuNzktNC00LTR6bTguOTQgM2MtLjQ2LTQuMTctMy43Ny03LjQ4LTcuOTQtNy45NFYxaC0ydjIuMDZDNi44MyAzLjUyIDMuNTIgNi44MyAzLjA2IDExSDF2MmgyLjA2Yy40NiA0LjE3IDMuNzcgNy40OCA3Ljk0IDcuOTRWMjNoMnYtMi4wNmM0LjE3LS40NiA3LjQ4LTMuNzcgNy45NC03Ljk0SDIzdi0yaC0yLjA2ek0xMiAxOWMtMy44NyAwLTctMy4xMy03LTdzMy4xMy03IDctNyA3IDMuMTMgNyA3LTMuMTMgNy03IDd6Ii8+PC9zdmc+)';
        container.style.backgroundSize = '24px 24px';
        container.style.backgroundPosition = 'center';
        container.style.backgroundRepeat = 'no-repeat';

        container.onclick = function(){
            map.locate({setView: true, maxZoom: 16});
        }

        return container;
    },

    onRemove: function(map) {
        // クリーンアップ処理（必要に応じて）
    }
});

// コントロールをマップに追加
L.control.myLocation = function(opts) {
    return new L.Control.MyLocation(opts);
}

L.control.myLocation({ position: 'topleft' }).addTo(map);
```

## 6. モバイル専用機能の検出

```javascript
// モバイルデバイスかどうかを判定
var isMobile = /Android|webOS|iPhone|iPad|iPod|BlackBerry|IEMobile|Opera Mini/i.test(navigator.userAgent);

if (isMobile) {
    // モバイル専用の設定
    map.locate({
        watch: true,
        enableHighAccuracy: true,
        setView: true,
        maxZoom: 16
    });
} else {
    // デスクトップの場合は通常の表示
    map.setView([35.6812, 139.7671], 13);
}
```

## 7. レスポンシブデザイン

デスクトップとモバイルの両方に対応する例：

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>レスポンシブマップ</title>
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css"
          integrity="sha256-p4NxAoJBhIIN+hmNHrzRCf9tD/miZyoHS5obTRR9BMY="
          crossorigin=""/>
    <style>
        body {
            margin: 0;
            padding: 0;
            font-family: Arial, sans-serif;
        }

        /* デスクトップ */
        .container {
            display: flex;
            height: 100vh;
        }

        .sidebar {
            width: 300px;
            padding: 20px;
            background-color: #f4f4f4;
            overflow-y: auto;
        }

        #map {
            flex: 1;
        }

        /* モバイル */
        @media (max-width: 768px) {
            .container {
                flex-direction: column;
            }

            .sidebar {
                width: 100%;
                height: 150px;
            }

            #map {
                height: calc(100vh - 150px);
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="sidebar">
            <h2>場所一覧</h2>
            <button id="locateBtn">現在位置を表示</button>
            <ul id="placeList"></ul>
        </div>
        <div id="map"></div>
    </div>

    <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"
            integrity="sha256-20nQCchB9co0qIjJZRGuk2/Z9VM+kNiyxNV1lvTlZBo="
            crossorigin=""></script>
    <script>
        var map = L.map('map').setView([35.6812, 139.7671], 13);

        L.tileLayer('https://tile.openstreetmap.org/{z}/{x}/{y}.png', {
            maxZoom: 19,
            attribution: '© OpenStreetMap contributors'
        }).addTo(map);

        // 現在位置ボタン
        document.getElementById('locateBtn').addEventListener('click', function() {
            map.locate({setView: true, maxZoom: 16});
        });

        map.on('locationfound', function(e) {
            L.marker(e.latlng).addTo(map)
                .bindPopup("現在位置").openPopup();
            L.circle(e.latlng, e.accuracy / 2).addTo(map);
        });

        map.on('locationerror', function(e) {
            alert('位置情報を取得できませんでした');
        });
    </script>
</body>
</html>
```

## 8. 完全なモバイルマップの例

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <meta name="apple-mobile-web-app-capable" content="yes">
    <title>モバイル対応フルスクリーンマップ</title>
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css"
          integrity="sha256-p4NxAoJBhIIN+hmNHrzRCf9tD/miZyoHS5obTRR9BMY="
          crossorigin=""/>
    <style>
        body {
            padding: 0;
            margin: 0;
        }
        html, body, #map {
            height: 100%;
            width: 100%;
        }

        /* カスタムボタンスタイル */
        .locate-button {
            position: absolute;
            top: 80px;
            right: 10px;
            z-index: 1000;
            background: white;
            border: 2px solid rgba(0,0,0,0.2);
            border-radius: 4px;
            padding: 10px;
            cursor: pointer;
            font-size: 18px;
        }
    </style>
</head>
<body>
    <button class="locate-button" onclick="locateUser()">📍</button>
    <div id="map"></div>

    <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"
            integrity="sha256-20nQCchB9co0qIjJZRGuk2/Z9VM+kNiyxNV1lvTlZBo="
            crossorigin=""></script>
    <script>
        var map = L.map('map');
        var currentMarker = null;
        var accuracyCircle = null;

        // タイルレイヤー
        L.tileLayer('https://tile.openstreetmap.org/{z}/{x}/{y}.png', {
            maxZoom: 19,
            attribution: '© OpenStreetMap contributors'
        }).addTo(map);

        // 初期位置を取得
        map.locate({setView: true, maxZoom: 16});

        // ユーザー位置検出関数
        function locateUser() {
            map.locate({setView: true, maxZoom: 16});
        }

        // 位置情報取得成功
        map.on('locationfound', function(e) {
            if (currentMarker) {
                map.removeLayer(currentMarker);
            }
            if (accuracyCircle) {
                map.removeLayer(accuracyCircle);
            }

            currentMarker = L.marker(e.latlng).addTo(map)
                .bindPopup("📍 現在位置<br>精度: ±" + e.accuracy.toFixed(0) + "m").openPopup();

            accuracyCircle = L.circle(e.latlng, {
                radius: e.accuracy / 2,
                color: '#136AEC',
                fillColor: '#136AEC',
                fillOpacity: 0.15,
                weight: 2
            }).addTo(map);
        });

        // 位置情報取得失敗
        map.on('locationerror', function(e) {
            alert('位置情報を取得できませんでした。\n' + e.message);
            // デフォルト位置（東京）を表示
            map.setView([35.6812, 139.7671], 13);
        });
    </script>
</body>
</html>
```

## よくある質問

### Q: 位置情報が取得できない

**A:** 以下を確認：
1. HTTPSで接続しているか（位置情報APIはHTTPSが必須）
2. ブラウザで位置情報の許可がされているか
3. デバイスの位置情報サービスがオンになっているか

### Q: 精度が低い

**A:** `enableHighAccuracy: true`を使用してください。ただし、バッテリー消費が増えます。

```javascript
map.locate({
    setView: true,
    maxZoom: 16,
    enableHighAccuracy: true  // 高精度モード
});
```

### Q: iOSで全画面表示にならない

**A:** 以下のメタタグを追加：

```html
<meta name="apple-mobile-web-app-capable" content="yes">
<meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
```

## セキュリティとプライバシー

位置情報を扱う際の注意点：

1. **HTTPS必須**：位置情報APIはHTTPSでのみ動作します
2. **ユーザーの同意**：必ずユーザーの明示的な同意を得る
3. **データ保存**：位置情報を保存する場合は適切な暗号化を行う
4. **透明性**：なぜ位置情報が必要か、どう使用するかを説明する

## 次のステップ

- [04_geojson.md](04_geojson.md)：GeoJSONデータの使用
- [05_layer_groups_control.md](05_layer_groups_control.md)：レイヤーグループとコントロール

## 参考リンク

- [Leaflet Mobile Tutorial](https://leafletjs.com/examples/mobile/)
- [Geolocation API (MDN)](https://developer.mozilla.org/en-US/docs/Web/API/Geolocation_API)
- [Leaflet Locate Control Plugin](https://github.com/domoritz/leaflet-locatecontrol)
