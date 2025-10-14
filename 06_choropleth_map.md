# インタラクティブなコロプレスマップ

**難易度：中級** ⭐⭐⭐

## このチュートリアルで学ぶこと

- コロプレスマップとは何か
- データに基づいた色分け地図の作成
- インタラクティブなホバー効果
- 情報パネルの実装
- 凡例（レジェンド）の追加
- データの可視化テクニック

## 必要な前提知識

- [04_geojson.md](04_geojson.md)のGeoJSON基礎
- JavaScriptの関数と条件分岐

## コロプレスマップとは？

**コロプレスマップ**（Choropleth Map）は、地理的な領域を統計データに基づいて色分けした地図です。人口密度、GDP、気温など、さまざまなデータの分布を視覚化できます。

### 使用例
- 都道府県別の人口密度
- 選挙結果の地域別分布
- 地域別の売上データ
- 疾病の発生率マップ

## 1. 基本的なセットアップ

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>コロプレスマップ</title>
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css"
          integrity="sha256-p4NxAoJBhIIN+hmNHrzRCf9tD/miZyoHS5obTRR9BMY="
          crossorigin=""/>
    <style>
        body {
            margin: 0;
            padding: 0;
        }
        #map {
            height: 100vh;
            width: 100%;
        }
    </style>
</head>
<body>
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

## 2. サンプルデータの準備

架空の東京都内の区のデータ：

```javascript
var tokyoWards = {
    "type": "FeatureCollection",
    "features": [
        {
            "type": "Feature",
            "properties": {
                "name": "千代田区",
                "density": 4700
            },
            "geometry": {
                "type": "Polygon",
                "coordinates": [[
                    [139.75, 35.69],
                    [139.77, 35.69],
                    [139.77, 35.68],
                    [139.75, 35.68],
                    [139.75, 35.69]
                ]]
            }
        },
        {
            "type": "Feature",
            "properties": {
                "name": "渋谷区",
                "density": 14500
            },
            "geometry": {
                "type": "Polygon",
                "coordinates": [[
                    [139.68, 35.66],
                    [139.72, 35.66],
                    [139.72, 35.64],
                    [139.68, 35.64],
                    [139.68, 35.66]
                ]]
            }
        },
        {
            "type": "Feature",
            "properties": {
                "name": "新宿区",
                "density": 18500
            },
            "geometry": {
                "type": "Polygon",
                "coordinates": [[
                    [139.68, 35.70],
                    [139.72, 35.70],
                    [139.72, 35.68],
                    [139.68, 35.68],
                    [139.68, 35.70]
                ]]
            }
        }
    ]
};
```

## 3. 色分け関数の作成

データ値に応じて色を返す関数：

```javascript
// 人口密度に応じた色を返す
function getColor(d) {
    return d > 20000 ? '#800026' :
           d > 15000 ? '#BD0026' :
           d > 10000 ? '#E31A1C' :
           d > 8000  ? '#FC4E2A' :
           d > 5000  ? '#FD8D3C' :
           d > 3000  ? '#FEB24C' :
           d > 1000  ? '#FED976' :
                       '#FFEDA0';
}
```

### カラースキームの選び方

- **連続的データ**：グラデーション（人口、気温など）
- **カテゴリカルデータ**：異なる色（選挙結果、土地利用など）
- **二値データ**：2色（はい/いいえ、高/低など）

## 4. スタイル関数の作成

```javascript
// 各地域のスタイルを定義
function style(feature) {
    return {
        fillColor: getColor(feature.properties.density),
        weight: 2,
        opacity: 1,
        color: 'white',
        dashArray: '3',
        fillOpacity: 0.7
    };
}
```

## 5. 基本的なコロプレスマップ

```javascript
var map = L.map('map').setView([35.6812, 139.7671], 11);

L.tileLayer('https://tile.openstreetmap.org/{z}/{x}/{y}.png', {
    maxZoom: 19,
    attribution: '© OpenStreetMap contributors'
}).addTo(map);

// 色分け関数
function getColor(d) {
    return d > 20000 ? '#800026' :
           d > 15000 ? '#BD0026' :
           d > 10000 ? '#E31A1C' :
           d > 8000  ? '#FC4E2A' :
           d > 5000  ? '#FD8D3C' :
           d > 3000  ? '#FEB24C' :
           d > 1000  ? '#FED976' :
                       '#FFEDA0';
}

// スタイル関数
function style(feature) {
    return {
        fillColor: getColor(feature.properties.density),
        weight: 2,
        opacity: 1,
        color: 'white',
        dashArray: '3',
        fillOpacity: 0.7
    };
}

// GeoJSONレイヤーを追加
L.geoJSON(tokyoWards, {
    style: style
}).addTo(map);
```

## 6. インタラクションの追加

### ホバー効果

```javascript
var geojsonLayer;

// マウスオーバー時のハイライト
function highlightFeature(e) {
    var layer = e.target;

    layer.setStyle({
        weight: 5,
        color: '#666',
        dashArray: '',
        fillOpacity: 0.9
    });

    layer.bringToFront();
}

// マウスアウト時に元に戻す
function resetHighlight(e) {
    geojsonLayer.resetStyle(e.target);
}

// クリック時のズーム
function zoomToFeature(e) {
    map.fitBounds(e.target.getBounds());
}

// 各フィーチャーにイベントを追加
function onEachFeature(feature, layer) {
    layer.on({
        mouseover: highlightFeature,
        mouseout: resetHighlight,
        click: zoomToFeature
    });
}

// レイヤーを作成
geojsonLayer = L.geoJSON(tokyoWards, {
    style: style,
    onEachFeature: onEachFeature
}).addTo(map);
```

## 7. 情報パネルの追加

```html
<style>
    .info {
        padding: 6px 8px;
        font: 14px/16px Arial, Helvetica, sans-serif;
        background: white;
        background: rgba(255,255,255,0.8);
        box-shadow: 0 0 15px rgba(0,0,0,0.2);
        border-radius: 5px;
    }
    .info h4 {
        margin: 0 0 5px;
        color: #777;
    }
</style>
```

```javascript
// 情報パネルのコントロール
var info = L.control();

info.onAdd = function (map) {
    this._div = L.DomUtil.create('div', 'info');
    this.update();
    return this._div;
};

// 情報を更新するメソッド
info.update = function (props) {
    this._div.innerHTML = '<h4>東京都 人口密度</h4>' +  (props ?
        '<b>' + props.name + '</b><br />' + props.density + ' 人 / km<sup>2</sup>'
        : 'マウスを乗せてください');
};

info.addTo(map);

// highlightFeature関数を更新
function highlightFeature(e) {
    var layer = e.target;

    layer.setStyle({
        weight: 5,
        color: '#666',
        dashArray: '',
        fillOpacity: 0.9
    });

    layer.bringToFront();
    info.update(layer.feature.properties);  // 情報パネルを更新
}

// resetHighlight関数を更新
function resetHighlight(e) {
    geojsonLayer.resetStyle(e.target);
    info.update();  // 情報パネルをリセット
}
```

## 8. 凡例（レジェンド）の追加

```html
<style>
    .legend {
        line-height: 18px;
        color: #555;
        background: white;
        background: rgba(255,255,255,0.8);
        padding: 6px 8px;
        border-radius: 5px;
        box-shadow: 0 0 15px rgba(0,0,0,0.2);
    }
    .legend i {
        width: 18px;
        height: 18px;
        float: left;
        margin-right: 8px;
        opacity: 0.7;
    }
</style>
```

```javascript
var legend = L.control({position: 'bottomright'});

legend.onAdd = function (map) {
    var div = L.DomUtil.create('div', 'legend'),
        grades = [0, 1000, 3000, 5000, 8000, 10000, 15000, 20000],
        labels = [];

    div.innerHTML = '<h4>人口密度<br>(人/km²)</h4>';

    // 各グレードのラベルと色付きボックスを生成
    for (var i = 0; i < grades.length; i++) {
        div.innerHTML +=
            '<i style="background:' + getColor(grades[i] + 1) + '"></i> ' +
            grades[i] + (grades[i + 1] ? '&ndash;' + grades[i + 1] + '<br>' : '+');
    }

    return div;
};

legend.addTo(map);
```

## 9. 完全な実践例

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>東京都区部 人口密度マップ</title>
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css"
          integrity="sha256-p4NxAoJBhIIN+hmNHrzRCf9tD/miZyoHS5obTRR9BMY="
          crossorigin=""/>
    <style>
        body {
            margin: 0;
            padding: 0;
        }
        #map {
            height: 100vh;
            width: 100%;
        }
        .info {
            padding: 6px 8px;
            font: 14px/16px Arial, Helvetica, sans-serif;
            background: white;
            background: rgba(255,255,255,0.8);
            box-shadow: 0 0 15px rgba(0,0,0,0.2);
            border-radius: 5px;
        }
        .info h4 {
            margin: 0 0 5px;
            color: #777;
        }
        .legend {
            line-height: 18px;
            color: #555;
            background: white;
            background: rgba(255,255,255,0.8);
            padding: 6px 8px;
            border-radius: 5px;
            box-shadow: 0 0 15px rgba(0,0,0,0.2);
        }
        .legend i {
            width: 18px;
            height: 18px;
            float: left;
            margin-right: 8px;
            opacity: 0.7;
        }
        .legend h4 {
            margin: 0 0 5px;
            font-size: 14px;
        }
    </style>
</head>
<body>
    <div id="map"></div>

    <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"
            integrity="sha256-20nQCchB9co0qIjJZRGuk2/Z9VM+kNiyxNV1lvTlZBo="
            crossorigin=""></script>
    <script>
        var map = L.map('map').setView([35.6812, 139.7], 11);

        L.tileLayer('https://{s}.basemaps.cartocdn.com/light_all/{z}/{x}/{y}{r}.png', {
            attribution: '© OpenStreetMap contributors © CARTO',
            subdomains: 'abcd',
            maxZoom: 19
        }).addTo(map);

        // サンプルGeoJSONデータ
        var tokyoWards = {
            "type": "FeatureCollection",
            "features": [
                {
                    "type": "Feature",
                    "properties": {"name": "千代田区", "density": 4700},
                    "geometry": {
                        "type": "Polygon",
                        "coordinates": [[[139.75, 35.69], [139.77, 35.69], [139.77, 35.68], [139.75, 35.68], [139.75, 35.69]]]
                    }
                },
                {
                    "type": "Feature",
                    "properties": {"name": "港区", "density": 10800},
                    "geometry": {
                        "type": "Polygon",
                        "coordinates": [[[139.73, 35.66], [139.76, 35.66], [139.76, 35.64], [139.73, 35.64], [139.73, 35.66]]]
                    }
                },
                {
                    "type": "Feature",
                    "properties": {"name": "渋谷区", "density": 14500},
                    "geometry": {
                        "type": "Polygon",
                        "coordinates": [[[139.68, 35.66], [139.72, 35.66], [139.72, 35.64], [139.68, 35.64], [139.68, 35.66]]]
                    }
                },
                {
                    "type": "Feature",
                    "properties": {"name": "新宿区", "density": 18500},
                    "geometry": {
                        "type": "Polygon",
                        "coordinates": [[[139.68, 35.70], [139.72, 35.70], [139.72, 35.68], [139.68, 35.68], [139.68, 35.70]]]
                    }
                },
                {
                    "type": "Feature",
                    "properties": {"name": "豊島区", "density": 22000},
                    "geometry": {
                        "type": "Polygon",
                        "coordinates": [[[139.70, 35.73], [139.74, 35.73], [139.74, 35.71], [139.70, 35.71], [139.70, 35.73]]]
                    }
                }
            ]
        };

        // 色分け関数
        function getColor(d) {
            return d > 20000 ? '#800026' :
                   d > 15000 ? '#BD0026' :
                   d > 10000 ? '#E31A1C' :
                   d > 8000  ? '#FC4E2A' :
                   d > 5000  ? '#FD8D3C' :
                   d > 3000  ? '#FEB24C' :
                   d > 1000  ? '#FED976' :
                               '#FFEDA0';
        }

        // スタイル関数
        function style(feature) {
            return {
                fillColor: getColor(feature.properties.density),
                weight: 2,
                opacity: 1,
                color: 'white',
                dashArray: '3',
                fillOpacity: 0.7
            };
        }

        var geojsonLayer;

        // インタラクション
        function highlightFeature(e) {
            var layer = e.target;
            layer.setStyle({
                weight: 5,
                color: '#666',
                dashArray: '',
                fillOpacity: 0.9
            });
            layer.bringToFront();
            info.update(layer.feature.properties);
        }

        function resetHighlight(e) {
            geojsonLayer.resetStyle(e.target);
            info.update();
        }

        function zoomToFeature(e) {
            map.fitBounds(e.target.getBounds());
        }

        function onEachFeature(feature, layer) {
            layer.on({
                mouseover: highlightFeature,
                mouseout: resetHighlight,
                click: zoomToFeature
            });
        }

        geojsonLayer = L.geoJSON(tokyoWards, {
            style: style,
            onEachFeature: onEachFeature
        }).addTo(map);

        // 情報パネル
        var info = L.control();

        info.onAdd = function (map) {
            this._div = L.DomUtil.create('div', 'info');
            this.update();
            return this._div;
        };

        info.update = function (props) {
            this._div.innerHTML = '<h4>東京都区部 人口密度</h4>' +  (props ?
                '<b>' + props.name + '</b><br />' + props.density.toLocaleString() + ' 人 / km<sup>2</sup>'
                : 'エリアにマウスを乗せてください');
        };

        info.addTo(map);

        // 凡例
        var legend = L.control({position: 'bottomright'});

        legend.onAdd = function (map) {
            var div = L.DomUtil.create('div', 'legend'),
                grades = [0, 1000, 3000, 5000, 8000, 10000, 15000, 20000];

            div.innerHTML = '<h4>人口密度<br>(人/km²)</h4>';

            for (var i = 0; i < grades.length; i++) {
                div.innerHTML +=
                    '<i style="background:' + getColor(grades[i] + 1) + '"></i> ' +
                    grades[i].toLocaleString() + (grades[i + 1] ? '&ndash;' + grades[i + 1].toLocaleString() + '<br>' : '+');
            }

            return div;
        };

        legend.addTo(map);
    </script>
</body>
</html>
```

## 10. 外部データの読み込み

実際のプロジェクトでは、外部GeoJSONファイルを使用します：

```javascript
// 外部GeoJSONファイルを読み込む
fetch('data/prefectures.geojson')
    .then(response => response.json())
    .then(data => {
        L.geoJSON(data, {
            style: style,
            onEachFeature: onEachFeature
        }).addTo(map);
    })
    .catch(error => {
        console.error('データの読み込みエラー:', error);
    });
```

## データソース

### 日本の地理データ

- **[国土地理院](https://www.gsi.go.jp/)**：行政界データ
- **[e-Stat](https://www.e-stat.go.jp/)**：統計データ
- **[Natural Earth](https://www.naturalearthdata.com/)**：世界の地理データ

## よくある質問

### Q: ポリゴンの枠線が重なって見づらい

**A:** 枠線を細くするか、`weight: 1`に設定してください。

### Q: データの範囲に応じて色を自動調整したい

**A:** データの最小値・最大値を計算して、動的に色を決定：

```javascript
var values = tokyoWards.features.map(f => f.properties.density);
var min = Math.min(...values);
var max = Math.max(...values);

function getColor(d) {
    var range = max - min;
    var normalized = (d - min) / range;
    // 0〜1の範囲で色を決定
    return normalized > 0.8 ? '#800026' :
           normalized > 0.6 ? '#BD0026' :
           normalized > 0.4 ? '#E31A1C' :
           normalized > 0.2 ? '#FC4E2A' :
                              '#FED976';
}
```

### Q: クリックで詳細情報を表示したい

**A:** ポップアップを追加：

```javascript
function onEachFeature(feature, layer) {
    layer.on({
        mouseover: highlightFeature,
        mouseout: resetHighlight,
        click: function(e) {
            var props = e.target.feature.properties;
            L.popup()
                .setLatLng(e.latlng)
                .setContent('<h3>' + props.name + '</h3><p>人口密度: ' + props.density + ' 人/km²</p>')
                .openOn(map);
        }
    });
}
```

## 💪 練習問題

データ可視化の理解を深めるための演習です。

### 演習1：カテゴリー別の色分け（初級）

**課題：** 連続的なデータではなく、カテゴリー別（例：土地利用区分）に色分けしたマップを作成してください。

**要件：**
- 5つの異なるカテゴリー（住宅、商業、工業、公園、その他）
- 各カテゴリーに明確に異なる色を割り当て
- 凡例にカテゴリー名と色を表示
- クリックで詳細情報を表示

<details>
<summary>💡 実装のヒント</summary>

```
1. カテゴリーから色へのマッピング：
   function getCategoryColor(category) {
       var colors = {
           '住宅': '#e74c3c',
           '商業': '#3498db',
           '工業': '#95a5a6',
           '公園': '#27ae60',
           'その他': '#f39c12'
       };
       return colors[category] || '#cccccc';
   }

2. スタイル関数で適用：
   function style(feature) {
       return {
           fillColor: getCategoryColor(feature.properties.landUse),
           weight: 1,
           color: 'white',
           fillOpacity: 0.7
       };
   }

3. 凡例は categories 配列でループ処理
```
</details>

### 演習2：インタラクティブデータフィルター（中級）

**課題：** スライダーを使って、特定の値以上/以下のエリアのみを表示するフィルター機能を実装してください。

**要件：**
- HTMLのrange inputでスライダーを作成
- スライダー値に応じてGeoJSONをフィルタリング
- フィルター適用中のエリア数を表示
- フィルター条件をテキストで表示

<details>
<summary>💡 LLM活用プロンプト例</summary>

**ChatGPT/Claude等へのプロンプト：**

```
Leaflet.jsのコロプレスマップに、スライダーでデータをフィルタリングする機能を追加したいです。

【現在の状態】
- 人口密度を色分けしたコロプレスマップあり
- GeoJSON データは geojsonData 変数に格納

【実現したい機能】
1. HTML range input スライダー（最小値0、最大値25000）
2. スライダー値以上の人口密度を持つエリアのみ表示
3. リアルタイムで地図を更新
4. 「表示中: X件 / 全Y件」と表示
5. 現在のフィルター値をテキスト表示

【技術的な要求】
- input イベントでスライダー変更を検知
- L.geoJSON の filter オプションでフィルタリング
- 既存レイヤーを削除してから新しいレイヤーを追加
- パフォーマンス考慮（デバウンス不要、即座に反映）

【コードスタイル】
- 日本語コメント
- フィルター条件は関数で分離
- 初心者にもわかりやすい実装
```

**実装のコアアイディア：**
```javascript
var slider = document.getElementById('densitySlider');
var filterValue = document.getElementById('filterValue');
var countDisplay = document.getElementById('count');

var allFeatures = geojsonData.features;
var geojsonLayer = null;

slider.addEventListener('input', function(e) {
    var minDensity = parseInt(e.target.value);
    filterValue.textContent = minDensity.toLocaleString();

    // 既存レイヤーを削除
    if (geojsonLayer) {
        map.removeLayer(geojsonLayer);
    }

    // フィルタリング
    var filteredData = {
        type: 'FeatureCollection',
        features: allFeatures.filter(function(feature) {
            return feature.properties.density >= minDensity;
        })
    };

    // 新しいレイヤーを追加
    geojsonLayer = L.geoJSON(filteredData, {
        style: style,
        onEachFeature: onEachFeature
    }).addTo(map);

    // 件数表示
    countDisplay.textContent = '表示中: ' + filteredData.features.length +
                                ' / 全' + allFeatures.length + '件';
});
```
</details>

### 演習3：時系列データアニメーション（上級）

**課題：** 複数年のデータを持つコロプレスマップで、年度を切り替えるとアニメーションで色が変わる機能を実装してください。

**要件：**
- 2020〜2024年の5年分のデータ
- 再生/停止ボタン
- 年度スライダーで手動操作も可能
- トランジション効果で滑らかに色変化
- 現在表示中の年度を大きく表示

<details>
<summary>💡 LLM活用プロンプト例</summary>

**段階的なアプローチ：**

```
【ステップ1: データ構造の設計】
「複数年度のデータを持つGeoJSON構造を設計してください。

【要件】
- 各フィーチャーの properties に年度別データを格納
  例: data_2020, data_2021, data_2022, data_2023, data_2024
- 年度を指定すると該当年のデータを取得する関数
- エラーハンドリング（データがない年度）

データ構造の例とアクセス関数を提供してください。」

【ステップ2: 年度切り替え機能】
「指定した年度のデータでコロプレスマップを更新する関数を実装してください：
- updateYear(year) 関数
- 既存のGeoJSONレイヤーを削除せずにスタイルのみ更新
- layer.setStyle() で各ポリゴンの色を変更
- パフォーマンスを考慮した実装」

【ステップ3: アニメーション制御】
「年度を自動的に切り替えるアニメーション機能を追加してください：
- 再生/停止ボタン
- setInterval で1秒ごとに年度を進める
- 最後の年度に達したら最初に戻る
- 停止時は現在の年度を維持」

【ステップ4: CSS トランジション】
「色の変化を滑らかにするCSSトランジションを追加してください：
- SVG path要素に transition プロパティ
- fill プロパティに0.5秒のトランジション
- easing関数で自然な動き
- Leaflet のペインに CSS を適用する方法」
```

**重要な概念：**
```javascript
var currentYear = 2020;
var animationInterval = null;
var isPlaying = false;

function updateMapForYear(year) {
    geojsonLayer.eachLayer(function(layer) {
        var feature = layer.feature;
        var data = feature.properties['data_' + year];

        if (data !== undefined) {
            var color = getColor(data);
            layer.setStyle({
                fillColor: color
            });
        }
    });

    document.getElementById('current-year').textContent = year + '年';
}

function startAnimation() {
    if (isPlaying) return;
    isPlaying = true;

    animationInterval = setInterval(function() {
        currentYear++;
        if (currentYear > 2024) {
            currentYear = 2020;
        }
        updateMapForYear(currentYear);
    }, 1000);
}

function stopAnimation() {
    isPlaying = false;
    if (animationInterval) {
        clearInterval(animationInterval);
        animationInterval = null;
    }
}

// CSSでトランジション追加
var style = document.createElement('style');
style.textContent = '.leaflet-overlay-pane svg path { transition: fill 0.5s ease; }';
document.head.appendChild(style);
```
</details>

### 🎓 学習のポイント

**データ可視化の原則：**

1. **色選択**: 直感的で色覚障害に配慮したカラーパレット
2. **凡例**: 必ず凡例を提供し、データの意味を明確に
3. **インタラクション**: ホバー、クリックで詳細情報を提供
4. **パフォーマンス**: 大量のポリゴンは簡略化を検討

**カラーパレットツール：**
- [ColorBrewer](https://colorbrewer2.org/): 地図用カラーパレット
- [Coolors](https://coolors.co/): カラーパレットジェネレーター
- [Adobe Color](https://color.adobe.com/): ハーモニーツール

### 📝 LLM活用：データ可視化の高度なプロンプト

**グラデーション生成：**
```
「指定した2色の間を補間する関数を作成し、Leafletのコロプレスマップに適用したいです。

【要件】
- 開始色: #ffffcc (薄い黄色)
- 終了色: #800026 (濃い赤)
- ステップ数: 8段階
- データ値を正規化（0-1）して色を割り当て
- RGB補間で中間色を計算

JavaScript関数と、Leafletでの使用例を提供してください。」
```

**複数変数の可視化：**
```
「コロプレスマップで2つの変数を同時に可視化したいです。

【シナリオ】
- 変数1: 人口密度（色の濃さで表現）
- 変数2: 平均年齢（ハッチングパターンで表現）

【技術的なアプローチ】
- SVGのpattern要素を使用
- 異なる角度のストライプで年齢層を表現
- 色とパターンの組み合わせ
- Leaflet で SVG パターンを適用する方法

実装例を教えてください。」
```

**データ正規化：**
```
「極端な外れ値があるデータセットを、見やすく正規化してコロプレスマップに表示したいです。

【問題】
- 値の範囲: 10 〜 50000
- 外れ値が数個あり、ほとんどは100〜1000の範囲
- 線形スケールだと差が見えにくい

【提案する解決策】
1. 対数スケール
2. パーセンタイルベース
3. 標準偏差ベース

それぞれの実装方法とメリット・デメリットを教えてください。」
```

## 次のステップ

- [07_wms_tms.md](07_wms_tms.md)：WMS/TMSサービスの統合
- [08_overlays.md](08_overlays.md)：画像、ビデオ、SVGオーバーレイ

## 参考リンク

- [Leaflet Choropleth Tutorial](https://leafletjs.com/examples/choropleth/)
- [ColorBrewer](https://colorbrewer2.org/)：色の選択ツール
- [GeoJSON.io](https://geojson.io/)：GeoJSONの作成・編集
