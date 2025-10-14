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

## 💪 練習問題

GeoJSONデータの扱いを習得するための演習です。

### 演習1：カテゴリー別フィルタリング（初級）

**課題：** GeoJSONデータから特定のカテゴリーのフィーチャーのみを表示する機能を実装してください。

**要件：**
- 「観光地」「飲食店」「宿泊施設」のボタンを追加
- ボタンをクリックすると該当カテゴリーのみ表示
- 「すべて表示」ボタンも追加

<details>
<summary>💡 実装のヒント</summary>

```
1. GeoJSONデータの各フィーチャーに category プロパティを持たせる

2. filter オプションを使用：
   L.geoJSON(data, {
       filter: function(feature) {
           return feature.properties.category === selectedCategory;
       }
   }).addTo(map);

3. ボタンクリック時に：
   - 既存のGeoJSONレイヤーを削除
   - フィルター条件を変更
   - 新しいGeoJSONレイヤーを追加

4. ボタンの配置はHTML内に <div id="filter-buttons"></div> を用意
```
</details>

### 演習2：外部GeoJSONファイルの読み込みと検索（中級）

**課題：** 外部GeoJSONファイルを読み込み、名前で検索できる機能を実装してください。

**要件：**
- fetch APIで外部GeoJSONファイルを読み込み
- 検索ボックスで場所の名前を検索
- 検索結果をリスト表示
- リストをクリックするとその場所にズーム

<details>
<summary>💡 LLM活用プロンプト例</summary>

**ChatGPT/Claude等へのプロンプト：**

```
Leaflet.jsで外部GeoJSONファイルから場所を検索する機能を実装したいです。

【現在の状態】
- 基本的なLeafletマップは実装済み
- places.geojson に複数の場所データあり

【実現したい機能】
1. 検索ボックスにテキスト入力
2. GeoJSONデータから部分一致で検索
3. 検索結果を以下の形式でリスト表示：
   - 場所名
   - カテゴリー
   - クリックで地図移動＋ポップアップ表示

【技術的な要求】
- fetch() で GeoJSON を読み込み
- Array.filter() で検索
- 検索結果は <ul> リストで表示
- クリック時は map.setView() と popup.openOn()

【データ構造】
GeoJSON の properties に以下が含まれる：
- name: 場所名
- category: カテゴリー
- description: 説明

【コードスタイル】
- 日本語コメント
- 検索のデバウンス処理を含める（300ms）
- 初心者にもわかりやすい実装
```

**実装のコアアイディア：**
```javascript
var geojsonData = null;
var geojsonLayer = null;

// GeoJSONを読み込み
fetch('places.geojson')
    .then(response => response.json())
    .then(data => {
        geojsonData = data;
        displayGeoJSON(data);
    });

// 検索機能
var searchInput = document.getElementById('search');
var searchDebounce = null;

searchInput.addEventListener('input', function(e) {
    clearTimeout(searchDebounce);
    searchDebounce = setTimeout(function() {
        var query = e.target.value.toLowerCase();
        var results = geojsonData.features.filter(function(feature) {
            return feature.properties.name.toLowerCase().includes(query);
        });
        displaySearchResults(results);
    }, 300);
});
```
</details>

### 演習3：動的GeoJSON生成とリアルタイム更新（上級）

**課題：** ユーザーがマップ上をクリックすると、その位置にポイントを追加し、GeoJSON形式でエクスポートできる機能を実装してください。

**要件：**
- マップクリックでポイント追加
- 各ポイントに名前とカテゴリーを設定するフォーム
- ポイントの編集・削除機能
- GeoJSONをファイルとしてダウンロード
- LocalStorageに保存して再読み込み時に復元

<details>
<summary>💡 LLM活用プロンプト例</summary>

**段階的なアプローチ：**

```
【ステップ1: クリックでポイント追加】
「Leaflet.jsでマップクリック時にGeoJSONフィーチャーを動的に追加する機能を実装してください。
- map.on('click') でクリック位置を取得
- モーダルフォームで名前とカテゴリーを入力
- 入力後、GeoJSONオブジェクトに追加
- L.geoJSON() で再描画」

【ステップ2: 編集・削除機能】
「追加したポイントを右クリックで編集または削除できる機能を追加してください：
- layer.on('contextmenu') でコンテキストメニュー表示
- 編集モードでフォームに現在の値を表示
- 削除時は確認ダイアログを表示
- GeoJSONオブジェクトから該当フィーチャーを削除」

【ステップ3: エクスポート機能】
「現在のGeoJSONデータをファイルとしてダウンロードする機能を実装してください：
- JSON.stringify() で文字列化
- Blob オブジェクトを作成
- ダウンロードリンクを動的生成
- ファイル名は 'places_[日付].geojson' 形式」

【ステップ4: LocalStorage 保存】
「GeoJSONデータをLocalStorageに自動保存し、再読み込み時に復元してください：
- データ変更時に localStorage.setItem()
- ページ読み込み時に localStorage.getItem()
- データサイズの制限チェック（5MB程度）
- クリアボタンで保存データを削除」
```

**重要な概念：**
```javascript
var myGeoJSON = {
    type: "FeatureCollection",
    features: []
};

// 新しいポイントを追加
function addPoint(latlng, properties) {
    var newFeature = {
        type: "Feature",
        properties: properties,
        geometry: {
            type: "Point",
            coordinates: [latlng.lng, latlng.lat]
        }
    };
    myGeoJSON.features.push(newFeature);
    updateMap();
    saveToLocalStorage();
}

// GeoJSONをエクスポート
function exportGeoJSON() {
    var dataStr = JSON.stringify(myGeoJSON, null, 2);
    var blob = new Blob([dataStr], {type: 'application/json'});
    var url = URL.createObjectURL(blob);

    var link = document.createElement('a');
    link.href = url;
    link.download = 'my_places.geojson';
    link.click();
}
```
</details>

### 🎓 学習のポイント

**GeoJSONデータ処理の考え方：**

1. **座標の順序**: GeoJSONは [経度, 緯度]、Leafletは [緯度, 経度]
2. **プロパティ活用**: properties にメタデータを保存
3. **フィルタリング**: filter オプションで柔軟な表示制御
4. **パフォーマンス**: 大量データはクラスタリング検討

**デバッグのコツ：**
- [geojson.io](https://geojson.io/) でGeoJSONを可視化
- [geojsonlint.com](https://geojsonlint.com/) で構文チェック
- ブラウザコンソールで `console.log(JSON.stringify(geojson, null, 2))`
- 座標順序の間違いに注意

### 📝 LLM活用：GeoJSONデータ変換プロンプト

**CSV to GeoJSON変換：**
```
「以下のCSVデータをGeoJSON形式に変換するJavaScriptコードを書いてください：

【CSVデータ】
name,lat,lng,category
東京タワー,35.6586,139.7454,観光地
東京駅,35.6812,139.7671,駅

【要件】
1. ヘッダー行をスキップ
2. 各行をGeoJSON Featureに変換
3. FeatureCollectionとして出力
4. エラーハンドリング（不正な座標値など）

ブラウザで動作するコードを提供してください。」
```

**複雑なジオメトリの生成：**
```
「Leaflet.jsで円形のバッファー（指定半径）を持つGeoJSONポリゴンを生成したいです。

【入力】
- 中心点の座標（緯度・経度）
- 半径（メートル）
- 頂点数（デフォルト: 64）

【出力】
- GeoJSON Polygon形式

【技術的な要求】
- 地球を球体として扱う
- Haversine公式で座標計算
- 結果をL.geoJSON()で表示可能な形式に

実装例とともに、どのように動作するか説明してください。」
```

**データマージ：**
```
「2つのGeoJSON FeatureCollectionをマージし、プロパティでグループ化したいです。

【シナリオ】
- geojson1: 2023年のデータ
- geojson2: 2024年のデータ
- 同じ場所（name プロパティで判定）なら properties に年度別データを追加

【期待する出力】
{
  "type": "Feature",
  "properties": {
    "name": "東京タワー",
    "data_2023": {...},
    "data_2024": {...}
  },
  "geometry": {...}
}

JavaScriptでマージする関数を実装してください。」
```

## 次のステップ

- [05_layer_groups_control.md](05_layer_groups_control.md)：レイヤーグループとコントロール
- [06_choropleth_map.md](06_choropleth_map.md)：コロプレスマップ

## 参考リンク

- [Leaflet GeoJSON Tutorial](https://leafletjs.com/examples/geojson/)
- [GeoJSON Specification](https://geojson.org/)
- [GeoJSON on Wikipedia](https://en.wikipedia.org/wiki/GeoJSON)
