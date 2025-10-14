# クイックスタート：最初のLeafletマップを作成する

**難易度：超初心者向け** ⭐

## このチュートリアルで学ぶこと

- Leaflet.jsの基本的なセットアップ方法
- 簡単な地図を表示する方法
- マーカーの追加
- ポップアップの表示
- 図形（円、ポリゴン）の描画

## 必要な前提知識

- HTML/CSSの基本
- JavaScriptの基本的な構文

## 1. HTMLファイルの準備

まず、基本的なHTMLファイルを作成します。

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>私の最初のLeafletマップ</title>

    <!-- Leaflet CSSの読み込み -->
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css"
          integrity="sha256-p4NxAoJBhIIN+hmNHrzRCf9tD/miZyoHS5obTRR9BMY="
          crossorigin=""/>

    <!-- マップのスタイル -->
    <style>
        #map {
            height: 600px;
            width: 100%;
        }
    </style>
</head>
<body>
    <h1>私の最初のLeafletマップ</h1>

    <!-- マップを表示する要素 -->
    <div id="map"></div>

    <!-- Leaflet JavaScriptの読み込み -->
    <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"
            integrity="sha256-20nQCchB9co0qIjJZRGuk2/Z9VM+kNiyxNV1lvTlZBo="
            crossorigin=""></script>

    <!-- 地図の初期化スクリプト -->
    <script>
        // ここにコードを書いていきます
    </script>
</body>
</html>
```

### 重要なポイント

1. **Leaflet CSSとJavaScriptの読み込み**：CDN経由でLeafletを読み込んでいます
2. **マップコンテナ**：`<div id="map"></div>`が地図を表示する場所です
3. **高さの指定**：マップには必ず高さ（height）を指定する必要があります

## 2. 地図の初期化

最も基本的な地図を表示してみましょう。

```javascript
// 地図を初期化（東京の座標で中心を設定）
var map = L.map('map').setView([35.6812, 139.7671], 13);

// タイルレイヤー（地図画像）を追加
L.tileLayer('https://tile.openstreetmap.org/{z}/{x}/{y}.png', {
    maxZoom: 19,
    attribution: '© OpenStreetMap contributors'
}).addTo(map);
```

### コードの解説

- **`L.map('map')`**：`id="map"`の要素に地図を作成
- **`.setView([35.6812, 139.7671], 13)`**：
  - `[35.6812, 139.7671]`：緯度・経度（東京の座標）
  - `13`：ズームレベル（0〜19、数字が大きいほど拡大）
- **`L.tileLayer()`**：地図タイル（画像）を読み込む
- **`.addTo(map)`**：地図に追加する

## 3. マーカーを追加する

地図上に目印（マーカー）を配置してみましょう。

```javascript
// 東京タワーの位置にマーカーを追加
var marker = L.marker([35.6586, 139.7454]).addTo(map);
```

## 4. ポップアップを追加する

マーカーをクリックしたときに情報を表示できます。

```javascript
// マーカーにポップアップを追加
marker.bindPopup("<b>東京タワー</b><br>高さ333m").openPopup();
```

### ポップアップのオプション

```javascript
// 別の場所に独立したポップアップを配置
var popup = L.popup()
    .setLatLng([35.6812, 139.7671])
    .setContent("東京駅周辺エリア")
    .openOn(map);
```

## 5. 円を描画する

```javascript
// 半径500mの円を描画
var circle = L.circle([35.6812, 139.7671], {
    color: 'red',        // 枠線の色
    fillColor: '#f03',   // 塗りつぶしの色
    fillOpacity: 0.5,    // 塗りつぶしの透明度
    radius: 500          // 半径（メートル）
}).addTo(map);

// 円にもポップアップを追加できます
circle.bindPopup("東京駅から半径500m");
```

## 6. ポリゴン（多角形）を描画する

```javascript
// 三角形のエリアを描画
var polygon = L.polygon([
    [35.6895, 139.6917],  // 新宿
    [35.6762, 139.6503],  // 渋谷
    [35.6580, 139.7016]   // 六本木
], {
    color: 'blue',
    fillColor: '#30f',
    fillOpacity: 0.3
}).addTo(map);

polygon.bindPopup("新宿・渋谷・六本木エリア");
```

## 7. ポリライン（線）を描画する

```javascript
// 複数の地点を線で結ぶ
var polyline = L.polyline([
    [35.6812, 139.7671],  // 東京駅
    [35.6586, 139.7454],  // 東京タワー
    [35.6762, 139.6503]   // 渋谷
], {
    color: 'green',
    weight: 5,           // 線の太さ
    opacity: 0.7
}).addTo(map);

polyline.bindPopup("東京駅→東京タワー→渋谷");
```

## 完全なサンプルコード

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>私の最初のLeafletマップ</title>
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
            border: 2px solid #333;
            border-radius: 8px;
        }
    </style>
</head>
<body>
    <h1>私の最初のLeafletマップ</h1>
    <div id="map"></div>

    <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"
            integrity="sha256-20nQCchB9co0qIjJZRGuk2/Z9VM+kNiyxNV1lvTlZBo="
            crossorigin=""></script>
    <script>
        // 地図を初期化
        var map = L.map('map').setView([35.6812, 139.7671], 13);

        // タイルレイヤーを追加
        L.tileLayer('https://tile.openstreetmap.org/{z}/{x}/{y}.png', {
            maxZoom: 19,
            attribution: '© OpenStreetMap contributors'
        }).addTo(map);

        // マーカーを追加
        var marker = L.marker([35.6586, 139.7454]).addTo(map);
        marker.bindPopup("<b>東京タワー</b><br>高さ333m").openPopup();

        // 円を追加
        var circle = L.circle([35.6812, 139.7671], {
            color: 'red',
            fillColor: '#f03',
            fillOpacity: 0.5,
            radius: 500
        }).addTo(map);
        circle.bindPopup("東京駅から半径500m");

        // ポリゴンを追加
        var polygon = L.polygon([
            [35.6895, 139.6917],
            [35.6762, 139.6503],
            [35.6580, 139.7016]
        ], {
            color: 'blue',
            fillColor: '#30f',
            fillOpacity: 0.3
        }).addTo(map);
        polygon.bindPopup("新宿・渋谷・六本木エリア");
    </script>
</body>
</html>
```

## よくある質問

### Q: 地図が表示されません

**A:** 以下を確認してください：
1. マップコンテナ（`#map`）に高さが指定されているか
2. Leaflet CSSとJSが正しく読み込まれているか（ブラウザの開発者ツールで確認）
3. `L.map('map')`の`'map'`がHTMLの`id`と一致しているか

### Q: 自分の好きな場所の座標を知るには？

**A:** [https://www.latlong.net/](https://www.latlong.net/) などのサイトで検索できます。または、Google Mapsで場所を右クリックして座標をコピーすることもできます。

### Q: 他の地図スタイルを使いたい

**A:** OpenStreetMap以外にも様々なタイルプロバイダーがあります：

```javascript
// 地理院タイル（日本の地図）
L.tileLayer('https://cyberjapandata.gsi.go.jp/xyz/std/{z}/{x}/{y}.png', {
    attribution: '<a href="https://maps.gsi.go.jp/development/ichiran.html">地理院タイル</a>'
}).addTo(map);
```

## 次のステップ

このチュートリアルで基本をマスターしたら、次のチュートリアルに進みましょう：

- [02_markers_custom_icons.md](02_markers_custom_icons.md)：カスタムアイコンの使用
- [03_mobile_map.md](03_mobile_map.md)：モバイル対応マップ

## 参考リンク

- [Leaflet公式クイックスタート](https://leafletjs.com/examples/quick-start/)
- [Leaflet APIリファレンス](https://leafletjs.com/reference.html)
