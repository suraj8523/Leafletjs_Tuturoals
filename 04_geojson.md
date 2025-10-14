# GeoJSONデータを使って地図を表示する

**難易度：初級〜中級** ⭐⭐⭐

## このチュートリアルで学ぶこと

- GeoJSONフォーマットの基礎
- GeoJSONをLeafletに読み込む方法
- フィーチャーごとにスタイルをカスタマイズ
- GeoJSONデータにポップアップを追加
- 外部GeoJSONファイルの読み込み
- フィルタリングとインタラクション

## 必要な前提知識

- [01_quick_start.md](01_quick_start.md)の基礎知識
- JavaScriptのオブジェクトと配列

## GeoJSONとは？

**GeoJSON**は地理空間データを表現するためのJSON形式です。ポイント、ライン、ポリゴンなどの地理的特徴（フィーチャー）と、それに関連するプロパティを格納できます。

### GeoJSONの基本構造

```json
{
  "type": "Feature",
  "properties": {
    "name": "東京タワー",
    "category": "観光地"
  },
  "geometry": {
    "type": "Point",
    "coordinates": [139.7454, 35.6586]
  }
}
```

## 1. 基本的なセットアップ

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>GeoJSONマップ</title>
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
    <h1>GeoJSONマップ</h1>
    <div id="map"></div>

    <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"
            integrity="sha256-20nQCchB9co0qIjJZRGuk2/Z9VM+kNiyxNV1lvTlZBo="
            crossorigin=""></script>
    <script>
        var map = L.map('map').setView([35.6812, 139.7671], 11);

        L.tileLayer('https://tile.openstreetmap.org/{z}/{x}/{y}.png', {
            maxZoom: 19,
            attribution: '© OpenStreetMap contributors'
        }).addTo(map);

        // ここにコードを追加
    </script>
</body>
</html>
```

## 2. シンプルなGeoJSONポイントを表示

### 単一のポイント

```javascript
// GeoJSONデータ（1つのポイント）
var geojsonFeature = {
    "type": "Feature",
    "properties": {
        "name": "東京タワー",
        "popupContent": "高さ333mの電波塔"
    },
    "geometry": {
        "type": "Point",
        "coordinates": [139.7454, 35.6586]
    }
};

// GeoJSONレイヤーとして追加
L.geoJSON(geojsonFeature).addTo(map);
```

**重要**：GeoJSONの座標は `[経度, 緯度]` の順序（Leafletとは逆）です！

## 3. 複数のフィーチャーを表示

### FeatureCollection形式

```javascript
var places = {
    "type": "FeatureCollection",
    "features": [
        {
            "type": "Feature",
            "properties": {
                "name": "東京タワー",
                "category": "観光地"
            },
            "geometry": {
                "type": "Point",
                "coordinates": [139.7454, 35.6586]
            }
        },
        {
            "type": "Feature",
            "properties": {
                "name": "東京スカイツリー",
                "category": "観光地"
            },
            "geometry": {
                "type": "Point",
                "coordinates": [139.8107, 35.7101]
            }
        },
        {
            "type": "Feature",
            "properties": {
                "name": "皇居",
                "category": "史跡"
            },
            "geometry": {
                "type": "Point",
                "coordinates": [139.7528, 35.6852]
            }
        }
    ]
};

L.geoJSON(places).addTo(map);
```

## 4. ポップアップを追加する

### onEachFeatureオプション

```javascript
var places = {
    "type": "FeatureCollection",
    "features": [
        {
            "type": "Feature",
            "properties": {
                "name": "東京タワー",
                "description": "高さ333mの電波塔",
                "year": 1958
            },
            "geometry": {
                "type": "Point",
                "coordinates": [139.7454, 35.6586]
            }
        },
        {
            "type": "Feature",
            "properties": {
                "name": "東京スカイツリー",
                "description": "高さ634mの電波塔",
                "year": 2012
            },
            "geometry": {
                "type": "Point",
                "coordinates": [139.8107, 35.7101]
            }
        }
    ]
};

// 各フィーチャーにポップアップを追加
L.geoJSON(places, {
    onEachFeature: function (feature, layer) {
        if (feature.properties) {
            var popupContent = "<h3>" + feature.properties.name + "</h3>" +
                               "<p>" + feature.properties.description + "</p>" +
                               "<p>完成年: " + feature.properties.year + "</p>";
            layer.bindPopup(popupContent);
        }
    }
}).addTo(map);
```

## 5. スタイルをカスタマイズする

### ポイントのスタイル（pointToLayer）

```javascript
var places = {
    "type": "FeatureCollection",
    "features": [
        {
            "type": "Feature",
            "properties": {
                "name": "公園A",
                "category": "park"
            },
            "geometry": {
                "type": "Point",
                "coordinates": [139.7671, 35.6812]
            }
        },
        {
            "type": "Feature",
            "properties": {
                "name": "レストランB",
                "category": "restaurant"
            },
            "geometry": {
                "type": "Point",
                "coordinates": [139.7454, 35.6586]
            }
        }
    ]
};

// カテゴリー別に色を変える
function getColor(category) {
    switch(category) {
        case 'park': return 'green';
        case 'restaurant': return 'red';
        case 'hotel': return 'blue';
        default: return 'gray';
    }
}

L.geoJSON(places, {
    pointToLayer: function (feature, latlng) {
        return L.circleMarker(latlng, {
            radius: 8,
            fillColor: getColor(feature.properties.category),
            color: "#fff",
            weight: 2,
            opacity: 1,
            fillOpacity: 0.8
        });
    },
    onEachFeature: function (feature, layer) {
        layer.bindPopup("<b>" + feature.properties.name + "</b>");
    }
}).addTo(map);
```

### ポリゴンのスタイル

```javascript
var areas = {
    "type": "FeatureCollection",
    "features": [
        {
            "type": "Feature",
            "properties": {
                "name": "エリアA",
                "density": 200
            },
            "geometry": {
                "type": "Polygon",
                "coordinates": [[
                    [139.7, 35.68],
                    [139.75, 35.68],
                    [139.75, 35.65],
                    [139.7, 35.65],
                    [139.7, 35.68]
                ]]
            }
        }
    ]
};

// スタイル関数
function style(feature) {
    return {
        fillColor: getColor(feature.properties.density),
        weight: 2,
        opacity: 1,
        color: 'white',
        fillOpacity: 0.7
    };
}

function getColor(d) {
    return d > 1000 ? '#800026' :
           d > 500  ? '#BD0026' :
           d > 200  ? '#E31A1C' :
           d > 100  ? '#FC4E2A' :
           d > 50   ? '#FD8D3C' :
           d > 20   ? '#FEB24C' :
           d > 10   ? '#FED976' :
                      '#FFEDA0';
}

L.geoJSON(areas, {
    style: style
}).addTo(map);
```

## 6. ラインを表示する

```javascript
var routes = {
    "type": "FeatureCollection",
    "features": [
        {
            "type": "Feature",
            "properties": {
                "name": "山手線（一部）",
                "line": "yamanote"
            },
            "geometry": {
                "type": "LineString",
                "coordinates": [
                    [139.7671, 35.6812],  // 東京駅
                    [139.7638, 35.6809],  // 有楽町駅
                    [139.7606, 35.6652],  // 新橋駅
                    [139.7407, 35.6555]   // 浜松町駅
                ]
            }
        }
    ]
};

L.geoJSON(routes, {
    style: function(feature) {
        return {
            color: "#008000",
            weight: 4,
            opacity: 0.8
        };
    },
    onEachFeature: function(feature, layer) {
        layer.bindPopup("<b>" + feature.properties.name + "</b>");
    }
}).addTo(map);
```

## 7. 外部GeoJSONファイルを読み込む

### fetchを使った読み込み

```javascript
// 外部ファイル: places.geojson
fetch('places.geojson')
    .then(response => response.json())
    .then(data => {
        L.geoJSON(data, {
            onEachFeature: function(feature, layer) {
                if (feature.properties && feature.properties.name) {
                    layer.bindPopup(feature.properties.name);
                }
            }
        }).addTo(map);
    })
    .catch(error => {
        console.error('GeoJSONの読み込みエラー:', error);
    });
```

### jQueryを使った読み込み（オプション）

```javascript
$.getJSON("places.geojson", function(data) {
    L.geoJSON(data).addTo(map);
});
```

## 8. フィルタリング

特定の条件に合うフィーチャーのみを表示：

```javascript
var places = {
    "type": "FeatureCollection",
    "features": [
        {
            "type": "Feature",
            "properties": {
                "name": "場所A",
                "show": true
            },
            "geometry": {
                "type": "Point",
                "coordinates": [139.7671, 35.6812]
            }
        },
        {
            "type": "Feature",
            "properties": {
                "name": "場所B",
                "show": false
            },
            "geometry": {
                "type": "Point",
                "coordinates": [139.7454, 35.6586]
            }
        }
    ]
};

// show: trueのフィーチャーのみ表示
L.geoJSON(places, {
    filter: function(feature, layer) {
        return feature.properties.show === true;
    }
}).addTo(map);
```

## 9. インタラクティブな例

クリックでハイライト表示：

```javascript
var geojsonLayer;

function highlightFeature(e) {
    var layer = e.target;

    layer.setStyle({
        weight: 5,
        color: '#666',
        fillOpacity: 0.9
    });

    layer.bringToFront();
}

function resetHighlight(e) {
    geojsonLayer.resetStyle(e.target);
}

function onEachFeature(feature, layer) {
    layer.on({
        mouseover: highlightFeature,
        mouseout: resetHighlight,
        click: function(e) {
            map.fitBounds(e.target.getBounds());
        }
    });

    layer.bindPopup("<b>" + feature.properties.name + "</b>");
}

geojsonLayer = L.geoJSON(areas, {
    style: style,
    onEachFeature: onEachFeature
}).addTo(map);
```

## 10. 完全な実践例

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>GeoJSON実践例</title>
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
        }
        .legend {
            background: white;
            padding: 10px;
            line-height: 18px;
            color: #555;
        }
        .legend i {
            width: 18px;
            height: 18px;
            float: left;
            margin-right: 8px;
            opacity: 0.7;
        }
    </style>
</head>
<body>
    <h1>東京の観光地マップ</h1>
    <div id="map"></div>

    <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"
            integrity="sha256-20nQCchB9co0qIjJZRGuk2/Z9VM+kNiyxNV1lvTlZBo="
            crossorigin=""></script>
    <script>
        var map = L.map('map').setView([35.6812, 139.7671], 11);

        L.tileLayer('https://tile.openstreetmap.org/{z}/{x}/{y}.png', {
            maxZoom: 19,
            attribution: '© OpenStreetMap contributors'
        }).addTo(map);

        // GeoJSONデータ
        var tokyoPlaces = {
            "type": "FeatureCollection",
            "features": [
                {
                    "type": "Feature",
                    "properties": {
                        "name": "東京タワー",
                        "category": "観光地",
                        "description": "高さ333mの電波塔",
                        "year": 1958
                    },
                    "geometry": {
                        "type": "Point",
                        "coordinates": [139.7454, 35.6586]
                    }
                },
                {
                    "type": "Feature",
                    "properties": {
                        "name": "東京スカイツリー",
                        "category": "観光地",
                        "description": "高さ634mの電波塔",
                        "year": 2012
                    },
                    "geometry": {
                        "type": "Point",
                        "coordinates": [139.8107, 35.7101]
                    }
                },
                {
                    "type": "Feature",
                    "properties": {
                        "name": "浅草寺",
                        "category": "寺社",
                        "description": "東京最古の寺",
                        "year": 628
                    },
                    "geometry": {
                        "type": "Point",
                        "coordinates": [139.7967, 35.7148]
                    }
                },
                {
                    "type": "Feature",
                    "properties": {
                        "name": "上野公園",
                        "category": "公園",
                        "description": "広大な都市公園",
                        "year": 1873
                    },
                    "geometry": {
                        "type": "Point",
                        "coordinates": [139.7738, 35.7148]
                    }
                }
            ]
        };

        // カテゴリー別の色
        function getColor(category) {
            switch(category) {
                case '観光地': return '#e74c3c';
                case '寺社': return '#9b59b6';
                case '公園': return '#27ae60';
                default: return '#95a5a6';
            }
        }

        // GeoJSONレイヤーを追加
        L.geoJSON(tokyoPlaces, {
            pointToLayer: function (feature, latlng) {
                return L.circleMarker(latlng, {
                    radius: 10,
                    fillColor: getColor(feature.properties.category),
                    color: "#fff",
                    weight: 2,
                    opacity: 1,
                    fillOpacity: 0.8
                });
            },
            onEachFeature: function (feature, layer) {
                var popupContent = "<h3>" + feature.properties.name + "</h3>" +
                                   "<p><strong>カテゴリー:</strong> " + feature.properties.category + "</p>" +
                                   "<p>" + feature.properties.description + "</p>" +
                                   "<p><strong>設立年:</strong> " + feature.properties.year + "年</p>";
                layer.bindPopup(popupContent);
            }
        }).addTo(map);

        // 凡例を追加
        var legend = L.control({position: 'bottomright'});

        legend.onAdd = function (map) {
            var div = L.DomUtil.create('div', 'legend');
            var categories = ['観光地', '寺社', '公園'];

            div.innerHTML = '<h4>カテゴリー</h4>';

            for (var i = 0; i < categories.length; i++) {
                div.innerHTML +=
                    '<i style="background:' + getColor(categories[i]) + '"></i> ' +
                    categories[i] + '<br>';
            }

            return div;
        };

        legend.addTo(map);
    </script>
</body>
</html>
```

## GeoJSONツール

### オンラインツール

- **[geojson.io](https://geojson.io/)**：GeoJSONの作成・編集・可視化
- **[mapshaper](https://mapshaper.org/)**：GeoJSONの簡略化
- **[GeoJSON Validator](https://geojsonlint.com/)**：GeoJSONの検証

### データソース

- **[Natural Earth](https://www.naturalearthdata.com/)**：世界の地理データ
- **[国土地理院](https://www.gsi.go.jp/)**：日本の地理データ
- **[OpenStreetMap](https://www.openstreetmap.org/)**：OSMデータのGeoJSON変換

## よくある質問

### Q: 座標の順序が逆になっている

**A:** GeoJSONは`[経度, 緯度]`、Leafletは`[緯度, 経度]`です。`L.geoJSON()`は自動的に変換します。

### Q: 大きなGeoJSONファイルを扱うには？

**A:** 以下の方法を検討：
1. データを簡略化する（mapshaper使用）
2. クラスタリングプラグインを使用
3. ベクタータイルに変換

### Q: GeoJSONレイヤーを後から削除するには？

**A:**
```javascript
var geojsonLayer = L.geoJSON(data).addTo(map);
// 削除
map.removeLayer(geojsonLayer);
```

## 次のステップ

- [05_layer_groups_control.md](05_layer_groups_control.md)：レイヤーグループとコントロール
- [06_choropleth_map.md](06_choropleth_map.md)：コロプレスマップ

## 参考リンク

- [Leaflet GeoJSON Tutorial](https://leafletjs.com/examples/geojson/)
- [GeoJSON Specification](https://geojson.org/)
- [GeoJSON on Wikipedia](https://en.wikipedia.org/wiki/GeoJSON)
