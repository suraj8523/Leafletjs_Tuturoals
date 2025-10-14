# カスタムアイコンでマーカーをカスタマイズする

**難易度：初心者向け** ⭐⭐

## このチュートリアルで学ぶこと

- デフォルトマーカーの制限を理解する
- カスタムアイコンの作成方法
- アイコンのサイズとアンカーポイントの設定
- 複数の異なるアイコンを使い分ける方法
- DivIconを使ったテキストベースのアイコン

## 必要な前提知識

- [01_quick_start.md](01_quick_start.md)のマーカー基礎

## なぜカスタムアイコンが必要なのか？

Leafletのデフォルトマーカーは便利ですが、すべてのマーカーが同じ青いアイコンだと区別がつきにくくなります。カスタムアイコンを使用することで：

- 異なる種類の場所を視覚的に区別できる
- ブランドやデザインに合わせられる
- より直感的なUI/UXを提供できる

## 1. 基本的なセットアップ

まず、前回のクイックスタートと同じHTML基盤を用意します。

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>カスタムアイコンマーカー</title>
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css"
          integrity="sha256-p4NxAoJBhIIN+hmNHrzRCf9tD/miZyoHS5obTRR9BMY="
          crossorigin=""/>
    <style>
        #map {
            height: 600px;
            width: 100%;
        }
    </style>
</head>
<body>
    <h1>カスタムアイコンマーカー</h1>
    <div id="map"></div>

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

        // ここにコードを追加していきます
    </script>
</body>
</html>
```

## 2. カスタムアイコンを定義する

### 基本的なカスタムアイコン

```javascript
// カスタムアイコンを定義
var customIcon = L.icon({
    iconUrl: 'https://raw.githubusercontent.com/pointhi/leaflet-color-markers/master/img/marker-icon-2x-red.png',
    shadowUrl: 'https://cdnjs.cloudflare.com/ajax/libs/leaflet/1.9.4/images/marker-shadow.png',

    iconSize:     [25, 41],  // アイコンのサイズ [幅, 高さ]
    shadowSize:   [41, 41],  // 影のサイズ
    iconAnchor:   [12, 41],  // アイコンの基準点（地図上の位置に対応する点）
    shadowAnchor: [12, 41],  // 影の基準点
    popupAnchor:  [1, -34]   // ポップアップの表示位置（アイコンに対する相対位置）
});

// カスタムアイコンを使ってマーカーを作成
L.marker([35.6812, 139.7671], {icon: customIcon})
    .addTo(map)
    .bindPopup('東京駅');
```

### 各プロパティの説明

| プロパティ | 説明 | 必須 |
|----------|------|------|
| `iconUrl` | アイコン画像のURL | ✓ |
| `iconSize` | アイコンのサイズ（ピクセル） | ✓ |
| `iconAnchor` | アイコンの基準点（どの点が座標に対応するか） | 推奨 |
| `popupAnchor` | ポップアップの位置調整 | 任意 |
| `shadowUrl` | 影画像のURL | 任意 |
| `shadowSize` | 影のサイズ | 任意 |
| `shadowAnchor` | 影の基準点 | 任意 |

### iconAnchorの重要性

`iconAnchor`は、アイコンのどの点が地図上の座標に一致するかを指定します。

```
例：マーカーの先端を座標に合わせる場合
アイコンサイズが [25, 41] の場合：
iconAnchor: [12, 41]  // 下中央（横中央、下端）
```

## 3. 複数の異なるアイコンを使う

異なる種類の場所を表現するために、複数のアイコンを定義します。

```javascript
// レストランアイコン
var restaurantIcon = L.icon({
    iconUrl: 'https://raw.githubusercontent.com/pointhi/leaflet-color-markers/master/img/marker-icon-2x-red.png',
    shadowUrl: 'https://cdnjs.cloudflare.com/ajax/libs/leaflet/1.9.4/images/marker-shadow.png',
    iconSize: [25, 41],
    iconAnchor: [12, 41],
    popupAnchor: [1, -34]
});

// ホテルアイコン
var hotelIcon = L.icon({
    iconUrl: 'https://raw.githubusercontent.com/pointhi/leaflet-color-markers/master/img/marker-icon-2x-blue.png',
    shadowUrl: 'https://cdnjs.cloudflare.com/ajax/libs/leaflet/1.9.4/images/marker-shadow.png',
    iconSize: [25, 41],
    iconAnchor: [12, 41],
    popupAnchor: [1, -34]
});

// 観光地アイコン
var attractionIcon = L.icon({
    iconUrl: 'https://raw.githubusercontent.com/pointhi/leaflet-color-markers/master/img/marker-icon-2x-green.png',
    shadowUrl: 'https://cdnjs.cloudflare.com/ajax/libs/leaflet/1.9.4/images/marker-shadow.png',
    iconSize: [25, 41],
    iconAnchor: [12, 41],
    popupAnchor: [1, -34]
});

// それぞれのアイコンでマーカーを配置
L.marker([35.6812, 139.7671], {icon: restaurantIcon})
    .addTo(map)
    .bindPopup('<b>レストラン</b><br>東京駅グランルーフ');

L.marker([35.6586, 139.7454], {icon: attractionIcon})
    .addTo(map)
    .bindPopup('<b>観光地</b><br>東京タワー');

L.marker([35.6762, 139.6503], {icon: hotelIcon})
    .addTo(map)
    .bindPopup('<b>ホテル</b><br>渋谷エクセルホテル東急');
```

## 4. アイコンをクラスとして再利用する

同じ設定を何度も書くのは面倒です。`L.Icon.extend()`を使って再利用可能なアイコンクラスを作成できます。

```javascript
// カスタムアイコンのベースクラスを作成
var ColoredMarker = L.Icon.extend({
    options: {
        shadowUrl: 'https://cdnjs.cloudflare.com/ajax/libs/leaflet/1.9.4/images/marker-shadow.png',
        iconSize: [25, 41],
        iconAnchor: [12, 41],
        popupAnchor: [1, -34],
        shadowSize: [41, 41]
    }
});

// 色違いのアイコンを簡単に作成
var redMarker = new ColoredMarker({
    iconUrl: 'https://raw.githubusercontent.com/pointhi/leaflet-color-markers/master/img/marker-icon-2x-red.png'
});

var blueMarker = new ColoredMarker({
    iconUrl: 'https://raw.githubusercontent.com/pointhi/leaflet-color-markers/master/img/marker-icon-2x-blue.png'
});

var greenMarker = new ColoredMarker({
    iconUrl: 'https://raw.githubusercontent.com/pointhi/leaflet-color-markers/master/img/marker-icon-2x-green.png'
});

// 使用例
L.marker([35.6812, 139.7671], {icon: redMarker}).addTo(map);
L.marker([35.6586, 139.7454], {icon: greenMarker}).addTo(map);
L.marker([35.6762, 139.6503], {icon: blueMarker}).addTo(map);
```

## 5. DivIconを使う（HTML/CSSベースのアイコン）

画像を使わずに、HTMLとCSSでアイコンを作成することもできます。

```javascript
// シンプルな円形のアイコン
var circleIcon = L.divIcon({
    className: 'custom-div-icon',
    html: "<div style='background-color:#c30; width: 20px; height: 20px; border-radius: 50%; border: 2px solid #fff;'></div>",
    iconSize: [20, 20],
    iconAnchor: [10, 10]
});

L.marker([35.6895, 139.6917], {icon: circleIcon})
    .addTo(map)
    .bindPopup('新宿駅');

// 数字を表示するアイコン
var numberIcon = L.divIcon({
    className: 'number-icon',
    html: "<div style='background-color:#4285F4; color: white; width: 30px; height: 30px; border-radius: 50%; display: flex; align-items: center; justify-content: center; font-weight: bold; border: 2px solid white;'>1</div>",
    iconSize: [30, 30],
    iconAnchor: [15, 15]
});

L.marker([35.6580, 139.7016], {icon: numberIcon})
    .addTo(map)
    .bindPopup('六本木駅');
```

### DivIconの利点

- 画像ファイルが不要
- CSSで自由にスタイルを変更できる
- テキストや数字を動的に表示できる
- ファイルサイズが小さい

## 6. 実践例：カテゴリー別の場所を表示

```javascript
// 場所データ
var places = [
    {
        name: 'レストランA',
        category: 'restaurant',
        coords: [35.6812, 139.7671],
        description: '美味しい和食'
    },
    {
        name: 'ホテルB',
        category: 'hotel',
        coords: [35.6762, 139.6503],
        description: '快適な宿泊施設'
    },
    {
        name: '観光地C',
        category: 'attraction',
        coords: [35.6586, 139.7454],
        description: '人気の観光スポット'
    }
];

// アイコンのマッピング
var icons = {
    restaurant: redMarker,
    hotel: blueMarker,
    attraction: greenMarker
};

// 場所をマップに追加
places.forEach(function(place) {
    L.marker(place.coords, {icon: icons[place.category]})
        .addTo(map)
        .bindPopup('<b>' + place.name + '</b><br>' + place.description);
});
```

## 7. 独自の画像を使う

自分で作成した画像やアイコンを使用する場合：

```javascript
// 自分の画像を使用
var myIcon = L.icon({
    iconUrl: 'path/to/your/icon.png',
    iconSize: [32, 32],
    iconAnchor: [16, 32],
    popupAnchor: [0, -32]
});
```

### アイコン画像の推奨仕様

- **ファイル形式**：PNG（透過対応）
- **サイズ**：24x24 〜 48x48ピクセルが一般的
- **解像度**：高DPIディスプレイ用に2倍サイズも用意すると良い
- **背景**：透過背景を使用

## 8. Font Awesomeアイコンを使う

```html
<!-- headにFont Awesomeを追加 -->
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
```

```javascript
// Font Awesomeアイコンを使ったDivIcon
var faIcon = L.divIcon({
    html: '<i class="fas fa-coffee fa-2x" style="color: brown;"></i>',
    iconSize: [30, 30],
    className: 'fa-icon'
});

L.marker([35.6895, 139.6917], {icon: faIcon})
    .addTo(map)
    .bindPopup('カフェ');
```

## 完全なサンプルコード

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>カスタムアイコンマーカー</title>
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
    <h1>カスタムアイコンマーカー</h1>
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

        // カスタムアイコンクラス
        var ColoredMarker = L.Icon.extend({
            options: {
                shadowUrl: 'https://cdnjs.cloudflare.com/ajax/libs/leaflet/1.9.4/images/marker-shadow.png',
                iconSize: [25, 41],
                iconAnchor: [12, 41],
                popupAnchor: [1, -34],
                shadowSize: [41, 41]
            }
        });

        var redMarker = new ColoredMarker({
            iconUrl: 'https://raw.githubusercontent.com/pointhi/leaflet-color-markers/master/img/marker-icon-2x-red.png'
        });

        var blueMarker = new ColoredMarker({
            iconUrl: 'https://raw.githubusercontent.com/pointhi/leaflet-color-markers/master/img/marker-icon-2x-blue.png'
        });

        var greenMarker = new ColoredMarker({
            iconUrl: 'https://raw.githubusercontent.com/pointhi/leaflet-color-markers/master/img/marker-icon-2x-green.png'
        });

        // マーカーを配置
        L.marker([35.6812, 139.7671], {icon: redMarker})
            .addTo(map)
            .bindPopup('<b>レストラン</b><br>東京駅グランルーフ');

        L.marker([35.6586, 139.7454], {icon: greenMarker})
            .addTo(map)
            .bindPopup('<b>観光地</b><br>東京タワー');

        L.marker([35.6762, 139.6503], {icon: blueMarker})
            .addTo(map)
            .bindPopup('<b>ホテル</b><br>渋谷エクセルホテル東急');

        // DivIconの例
        var circleIcon = L.divIcon({
            className: 'custom-div-icon',
            html: "<div style='background-color:#c30; width: 20px; height: 20px; border-radius: 50%; border: 2px solid #fff;'></div>",
            iconSize: [20, 20],
            iconAnchor: [10, 10]
        });

        L.marker([35.6895, 139.6917], {icon: circleIcon})
            .addTo(map)
            .bindPopup('新宿駅');
    </script>
</body>
</html>
```

## よくある質問

### Q: アイコンの位置がずれている

**A:** `iconAnchor`を調整してください。一般的には、マーカーの先端を座標に合わせるため、`[width/2, height]`に設定します。

### Q: アイコン画像が表示されない

**A:** 以下を確認：
1. 画像URLが正しいか
2. CORS（クロスオリジン）の問題がないか
3. ブラウザの開発者ツールで404エラーが出ていないか

### Q: 影が不要な場合は？

**A:** `shadowUrl`を指定しなければ影は表示されません：

```javascript
var noShadowIcon = L.icon({
    iconUrl: 'your-icon.png',
    iconSize: [25, 41],
    iconAnchor: [12, 41]
    // shadowUrlを指定しない
});
```

## 次のステップ

- [03_mobile_map.md](03_mobile_map.md)：モバイル対応マップ
- [04_geojson.md](04_geojson.md)：GeoJSONデータの使用

## 参考リンク

- [Leaflet Icon Documentation](https://leafletjs.com/reference.html#icon)
- [Leaflet DivIcon Documentation](https://leafletjs.com/reference.html#divicon)
- [Leaflet Color Markers](https://github.com/pointhi/leaflet-color-markers)
