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

## 💪 練習問題

モバイル対応マップの理解を深めるための演習です。

### 演習1：位置情報ボタンの追加（初級）

**課題：** 現在位置に移動するボタンをマップに追加してください。

**要件：**
- ボタンをクリックすると現在位置に地図が移動
- 現在位置にマーカーを表示
- ボタンはマップの左上に配置

<details>
<summary>💡 実装のヒント</summary>

```
1. L.Control.extend() を使用してカスタムコントロールを作成

2. onAdd メソッドでボタンを作成：
   - L.DomUtil.create() でボタン要素を作成
   - onclick イベントで map.locate() を呼び出し

3. locationfound イベントでマーカーを配置：
   map.on('locationfound', function(e) {
       L.marker(e.latlng).addTo(map);
       map.setView(e.latlng, 16);
   });

4. position: 'topleft' でコントロールを配置
```
</details>

### 演習2：移動経路の記録（中級）

**課題：** ユーザーの移動経路をリアルタイムで記録し、地図上に線で表示してください。

**要件：**
- 位置追跡を開始/停止するボタン
- 移動経路をポリラインで表示
- 移動距離の合計を表示
- 経路の色を変更できる機能

<details>
<summary>💡 LLM活用プロンプト例</summary>

**ChatGPT/Claude等へのプロンプト：**

```
Leaflet.jsでユーザーの移動経路を記録する機能を実装したいです。

【現在の状態】
- 基本的なモバイルマップは実装済み
- map.locate() で位置情報取得は可能

【実現したい機能】
1. 「記録開始」「記録停止」ボタンを追加
2. 記録中は locationfound イベントで位置を配列に保存
3. 保存した位置をつなぐポリラインを描画
4. 各地点間の距離を計算し、合計距離を表示

【技術的な要求】
- map.locate({watch: true}) で継続的に位置を取得
- L.polyline() で経路を描画
- latlng.distanceTo() で距離を計算
- カスタムコントロールでUIを実装

【コードスタイル】
- 日本語コメント
- グローバル変数を避けてオブジェクトにまとめる
- 初心者にもわかりやすい実装
```

**実装のコアアイディア：**
```javascript
var tracking = {
    active: false,
    points: [],
    polyline: null,
    totalDistance: 0
};

function startTracking() {
    tracking.active = true;
    tracking.points = [];
    tracking.totalDistance = 0;
    map.locate({watch: true, enableHighAccuracy: true});
}

map.on('locationfound', function(e) {
    if (!tracking.active) return;

    var newPoint = e.latlng;

    if (tracking.points.length > 0) {
        var lastPoint = tracking.points[tracking.points.length - 1];
        tracking.totalDistance += lastPoint.distanceTo(newPoint);
    }

    tracking.points.push(newPoint);

    if (tracking.polyline) {
        map.removeLayer(tracking.polyline);
    }

    tracking.polyline = L.polyline(tracking.points, {
        color: 'blue',
        weight: 4
    }).addTo(map);
});
```
</details>

### 演習3：オフライン対応マップ（上級）

**課題：** Service Workerを使用して、オフラインでも動作するモバイルマップを実装してください。

**要件：**
- Service Workerでタイル画像をキャッシュ
- オフライン時にキャッシュから表示
- オンライン/オフライン状態の表示
- キャッシュクリア機能

<details>
<summary>💡 LLM活用プロンプト例</summary>

**段階的なアプローチ：**

```
【ステップ1: Service Workerの基本】
「Leaflet.jsのタイルマップをService Workerでキャッシュする方法を教えてください。
以下の情報を含めてください：
- service-worker.js の基本構造
- install イベントでのキャッシュ作成
- fetch イベントでのキャッシュ優先戦略」

【ステップ2: タイルURLのキャッシュ】
「OpenStreetMapのタイルURL（https://tile.openstreetmap.org/{z}/{x}/{y}.png）
を動的にキャッシュする方法を教えてください。
- ワイルドカードパターンのマッチング
- キャッシュサイズの制限
- 古いキャッシュの削除」

【ステップ3: オフライン検知】
「navigator.onLine を使ってオフライン状態を検知し、
ユーザーに通知する機能を追加してください。
- online/offline イベントのリスナー
- 通知バナーの表示/非表示
- キャッシュ利用中のインジケーター」

【ステップ4: キャッシュ管理】
「ユーザーがキャッシュをクリアできるボタンを実装してください：
- caches.delete() の使用
- キャッシュサイズの表示
- 確認ダイアログ」
```

**重要な概念：**
```javascript
// service-worker.js
const CACHE_NAME = 'leaflet-map-v1';

self.addEventListener('fetch', function(event) {
    if (event.request.url.includes('tile.openstreetmap.org')) {
        event.respondWith(
            caches.match(event.request).then(function(response) {
                return response || fetch(event.request).then(function(response) {
                    return caches.open(CACHE_NAME).then(function(cache) {
                        cache.put(event.request, response.clone());
                        return response;
                    });
                });
            })
        );
    }
});
```
</details>

### 🎓 学習のポイント

**モバイル対応の考え方：**

1. **パフォーマンス**: タッチ操作の応答性が重要
2. **バッテリー消費**: 高精度モードと通常モードのバランス
3. **UX**: モバイル特有の操作（ピンチズーム、スワイプ）を考慮
4. **接続環境**: オンライン/オフラインの切り替えに対応

**デバッグのコツ：**
- Chrome DevToolsのモバイルエミュレーター
- 実機でのテスト（iOSとAndroid両方）
- 位置情報のシミュレーション機能を活用
- Network throttling で低速回線をテスト

### 📝 LLM活用：モバイル開発特有のプロンプト

**位置情報関連：**
```
「Leaflet.jsで位置情報が取得できない場合のエラーハンドリングを実装してください。
以下のケースに対応：
1. ユーザーが位置情報を拒否
2. 位置情報APIがサポートされていない
3. タイムアウト
4. 精度が低すぎる場合
それぞれに適切なメッセージを表示してください。」
```

**レスポンシブデザイン：**
```
「Leaflet.jsのマップをレスポンシブにし、画面サイズに応じてコントロールの配置を変更したいです。
- デスクトップ: サイドバー + マップ
- タブレット: 折りたたみ可能なサイドバー
- スマートフォン: フルスクリーンマップ + 引き出しメニュー
CSSメディアクエリとJavaScriptの組み合わせで実装してください。」
```

**タッチ操作最適化：**
```
「モバイルデバイスでのLeaflet.jsマップのタッチ操作を改善したいです：
1. ボタンのタップターゲットを大きくする（最低44x44px）
2. スワイプジェスチャーでサイドパネルを開閉
3. 長押しでコンテキストメニュー表示
4. ダブルタップで詳細情報表示
実装例とベストプラクティスを教えてください。」
```

## 次のステップ

- [04_geojson.md](04_geojson.md)：GeoJSONデータの使用
- [05_layer_groups_control.md](05_layer_groups_control.md)：レイヤーグループとコントロール

## 参考リンク

- [Leaflet Mobile Tutorial](https://leafletjs.com/examples/mobile/)
- [Geolocation API (MDN)](https://developer.mozilla.org/en-US/docs/Web/API/Geolocation_API)
- [Leaflet Locate Control Plugin](https://github.com/domoritz/leaflet-locatecontrol)
