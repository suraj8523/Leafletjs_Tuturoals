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

## 💪 練習問題

このセクションの内容を理解できたか確認しましょう。以下の課題に挑戦してください。

### 演習1：基本マップの作成（初級）

**課題：** 自分の住んでいる地域を中心としたマップを作成してください。

**要件：**
- 自分の地域の座標を調べる
- ズームレベルを14に設定
- 地図タイトルを追加

<details>
<summary>💡 実装のヒント</summary>

```
1. 座標の調べ方：
   - Google Mapsで場所を右クリック→座標をコピー
   - または https://www.latlong.net/ で検索

2. 基本的なコード構造：
   var map = L.map('map').setView([あなたの緯度, あなたの経度], 14);

3. タイトルの追加：
   HTMLの<body>タグ内にh1要素を追加
```
</details>

### 演習2：複数のマーカーと図形（中級）

**課題：** 自分の地域の興味深い場所（学校、公園、駅など）を5つ以上マーカーで表示してください。さらに、それらを囲む円を追加してください。

**要件：**
- 最低5つのマーカー
- 各マーカーに場所の名前をポップアップで表示
- すべてのマーカーを囲む円を追加
- 円の色を緑色にする

<details>
<summary>💡 実装のヒント</summary>

```
1. マーカーの配列を作成すると管理しやすい：
   var places = [
       {name: "場所1", coords: [緯度1, 経度1]},
       {name: "場所2", coords: [緯度2, 経度2]},
       // ...
   ];

2. forEachでループ処理：
   places.forEach(function(place) {
       L.marker(place.coords).addTo(map)
           .bindPopup(place.name);
   });

3. 円の中心座標は複数マーカーの中心点を計算
```
</details>

### 演習3：インタラクティブな要素（上級）

**課題：** ユーザーがマップをクリックした位置にマーカーを追加する機能を実装してください。

**要件：**
- マップクリック時に新しいマーカーを追加
- マーカーのポップアップにクリックした座標を表示
- マーカーの個数を画面に表示

<details>
<summary>💡 実装のヒント（LLM活用プロンプト例）</summary>

**ChatGPT/Claude等へのプロンプト例：**

```
以下のLeafletマップコードに、マップクリック時にマーカーを追加する機能を実装してください：

【現在のコード】
[あなたの現在のコードを貼り付け]

【要件】
1. map.on('click', function(e) { ... }) を使用
2. e.latlng でクリック座標を取得
3. マーカーを動的に追加
4. ポップアップに座標を表示（小数点5桁まで）
5. HTMLに<div id="counter">マーカー数: 0</div>を追加
6. クリックごとにカウンターを更新

【コードスタイル】
- 日本語コメントを含める
- 変数名は英語、説明は日本語
- 初心者にもわかりやすい実装
```

**実装の核心部分：**
```javascript
var markerCount = 0;

map.on('click', function(e) {
    // クリックされた位置の座標
    var lat = e.latlng.lat;
    var lng = e.latlng.lng;

    // マーカーを追加
    L.marker([lat, lng]).addTo(map)
        .bindPopup('座標: ' + lat.toFixed(5) + ', ' + lng.toFixed(5));

    // カウンターを更新
    markerCount++;
    document.getElementById('counter').textContent = 'マーカー数: ' + markerCount;
});
```
</details>

### 🎓 学習のポイント

練習問題を解く際は：

1. **段階的に実装**：まず基本機能、次に追加機能
2. **エラーを恐れない**：ブラウザの開発者ツールでエラーを確認
3. **公式ドキュメント参照**：[Leaflet API](https://leafletjs.com/reference.html)
4. **LLMを活用**：わからない部分は具体的に質問

### 📝 LLM活用のコツ

効果的なプロンプトの書き方：

```
【良い例】
「Leaflet.jsで、マップ上の2点間の距離を計算して表示する機能を
追加したいです。現在のコードは以下です：[コード]
L.latLng.distanceTo()メソッドを使った実装例を教えてください。」

【悪い例】
「マップに機能を追加して」
→ 何を追加したいのか不明確
```

**プロンプトに含めるべき情報：**
1. 現在のコードの状態
2. 実現したい具体的な機能
3. 使用したいLeaflet APIやメソッド（わかる場合）
4. コードスタイルの希望（日本語コメントなど）

## 次のステップ

このチュートリアルで基本をマスターしたら、次のチュートリアルに進みましょう：

- [02_markers_custom_icons.md](02_markers_custom_icons.md)：カスタムアイコンの使用
- [03_mobile_map.md](03_mobile_map.md)：モバイル対応マップ

## 参考リンク

- [Leaflet公式クイックスタート](https://leafletjs.com/examples/quick-start/)
- [Leaflet APIリファレンス](https://leafletjs.com/reference.html)
