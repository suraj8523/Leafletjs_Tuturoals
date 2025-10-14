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

## 次のステップ

- [07_wms_tms.md](07_wms_tms.md)：WMS/TMSサービスの統合
- [08_overlays.md](08_overlays.md)：画像、ビデオ、SVGオーバーレイ

## 参考リンク

- [Leaflet Choropleth Tutorial](https://leafletjs.com/examples/choropleth/)
- [ColorBrewer](https://colorbrewer2.org/)：色の選択ツール
- [GeoJSON.io](https://geojson.io/)：GeoJSONの作成・編集
