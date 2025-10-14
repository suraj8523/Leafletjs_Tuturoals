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

## 💪 練習問題

カスタムアイコンの理解を深めるための演習です。

### 演習1：カテゴリー別アイコン（初級）

**課題：** 自分の地域の施設を3つのカテゴリー（飲食店、病院、学校）に分けて、それぞれ異なる色のアイコンで表示してください。

**要件：**
- 各カテゴリー最低2つずつのマーカー
- 色分けされたアイコン（赤、青、緑など）
- ポップアップにカテゴリー名と施設名を表示

<details>
<summary>💡 実装のヒント</summary>

```
1. カラーマーカーのURL：
   赤: marker-icon-2x-red.png
   青: marker-icon-2x-blue.png
   緑: marker-icon-2x-green.png

2. データ構造の例：
   var facilities = [
       {name: "〇〇レストラン", category: "restaurant", coords: [緯度, 経度]},
       {name: "△△病院", category: "hospital", coords: [緯度, 経度]},
       // ...
   ];

3. カテゴリーからアイコンへのマッピングを作成
```
</details>

### 演習2：DivIconでカスタムデザイン（中級）

**課題：** DivIconを使って、数字と背景色を持つカスタムマーカーを作成してください。

**要件：**
- 1〜5の番号付きマーカーを作成
- 各マーカーの背景色をグラデーションで変化させる
- マーカーをクリックすると詳細情報を表示

<details>
<summary>💡 LLM活用プロンプト例</summary>

**ChatGPT/Claude等へのプロンプト：**

```
Leaflet.jsのDivIconを使って、以下の要件を満たすカスタムマーカーを作成してください：

【要件】
1. 1〜5の番号を表示する円形マーカー
2. 番号ごとに背景色を変える（1=赤、2=オレンジ、...、5=青のグラデーション）
3. 番号は白色、中央揃え
4. CSS flexboxで中央配置
5. border-radius: 50% で円形にする

【コードスタイル】
- JavaScript関数で番号から色を返す
- HTMLに数字を埋め込む
- 日本語コメント付き
```

**実装のコアアイディア：**
```javascript
function getColorForNumber(n) {
    var colors = ['#e74c3c', '#e67e22', '#f1c40f', '#3498db', '#9b59b6'];
    return colors[n - 1];
}

function createNumberedIcon(number) {
    return L.divIcon({
        html: "<div style='background:" + getColorForNumber(number) +
              "; color: white; width: 30px; height: 30px; " +
              "border-radius: 50%; display: flex; align-items: center; " +
              "justify-content: center; font-weight: bold; border: 2px solid white;'>" +
              number + "</div>",
        iconSize: [30, 30],
        iconAnchor: [15, 15]
    });
}
```
</details>
</details>

### 演習3：アイコンアニメーション（上級）

**課題：** マーカーをクリックすると一時的にサイズが大きくなるアニメーションを実装してください。

**要件：**
- クリック時にアイコンが1.5倍に拡大
- 1秒後に元のサイズに戻る
- CSSトランジションを使用

<details>
<summary>💡 LLM活用プロンプト例</summary>

**複雑な機能のためのプロンプト構成：**

```
Leaflet.jsのカスタムアイコンマーカーに、クリック時のアニメーション効果を追加したいです。

【現在の状態】
[あなたの現在のコードを貼り付け]

【実現したい機能】
1. マーカーをクリックすると、アイコンが1.5倍に拡大
2. 1秒後に元のサイズに戻る
3. CSSの transition を使ってスムーズにアニメーション

【技術的な要求】
- marker.getElement() でDOMにアクセス
- marker.on('click', callback) でイベントリスナーを追加
- CSS transform: scale() を使用
- setTimeout で元に戻す
- transition: transform 0.3s ease でアニメーション

【参考にしたいアプローチ】
マーカーのDOM要素に直接スタイルを適用する方法を教えてください。
```

**実装の重要ポイント：**
```javascript
marker.on('click', function(e) {
    var icon = e.target._icon;

    // 拡大
    icon.style.transform = 'scale(1.5)';
    icon.style.transition = 'transform 0.3s ease';

    // 1秒後に元に戻す
    setTimeout(function() {
        icon.style.transform = 'scale(1)';
    }, 1000);
});
```
</details>

### 🎓 学習のポイント

**アイコンカスタマイズの考え方：**

1. **L.Icon**: 画像ベース、詳細な設定が可能
2. **L.DivIcon**: HTML/CSSベース、柔軟性が高い
3. **トレードオフ**: パフォーマンス vs カスタマイズ性

**デバッグのコツ：**
- ブラウザの開発者ツールで要素を検査
- `console.log(marker._icon)` でDOM要素を確認
- iconAnchorのずれは視覚的に調整

### 📝 LLM活用：段階的な改良プロンプト

複雑なカスタマイズは段階的に：

```
【ステップ1: 基本実装】
「Leaflet.jsでDivIconを使って番号付きマーカーを作成してください」

【ステップ2: スタイル改善】
「前のコードに、グラデーションの背景色と影を追加してください」

【ステップ3: インタラクション追加】
「マーカーにホバー効果（色が薄くなる）を追加してください」

【ステップ4: アニメーション】
「クリック時にバウンスアニメーションを追加してください」
```

**改良を依頼する際のポイント：**
- 前のコードを必ず含める
- 何を変更したいか明確に
- 既存の動作を保持するか指定

### 🔧 よくあるカスタマイズのプロンプト集

**1. SVGアイコンを使用：**
```
「Leaflet.jsのカスタムアイコンで、SVGのカメラアイコンを使いたいです。
SVGコードをiconUrlの代わりにどう指定すれば良いですか？
data URI形式の例を教えてください。」
```

**2. アイコンのツールチップ：**
```
「L.Iconで作成したマーカーに、ホバー時に表示されるツールチップを
追加したいです。bindTooltip()メソッドの使用例を教えてください。」
```

**3. クラスタリング準備：**
```
「多数のカスタムアイコンマーカーを表示する予定です。
パフォーマンスを考慮したアイコン作成のベストプラクティスを教えてください。
（アイコンの再利用、メモリ効率など）」
```

## 次のステップ

- [03_mobile_map.md](03_mobile_map.md)：モバイル対応マップ
- [04_geojson.md](04_geojson.md)：GeoJSONデータの使用

## 参考リンク

- [Leaflet Icon Documentation](https://leafletjs.com/reference.html#icon)
- [Leaflet DivIcon Documentation](https://leafletjs.com/reference.html#divicon)
- [Leaflet Color Markers](https://github.com/pointhi/leaflet-color-markers)
