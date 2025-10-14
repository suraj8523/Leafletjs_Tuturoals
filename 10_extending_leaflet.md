# Leafletの拡張：カスタムクラスとプラグイン

**難易度：上級** ⭐⭐⭐⭐⭐

## このチュートリアルで学ぶこと

- Leafletのクラスシステムの理解
- カスタムマーカーの作成
- カスタムレイヤーの実装
- カスタムコントロールの開発
- プラグインの作成方法
- イベントシステムの活用

## 必要な前提知識

- JavaScript のオブジェクト指向プログラミング
- [01_quick_start.md](01_quick_start.md)の基礎知識
- Leaflet API の基本理解

## Leafletのクラスシステム

Leafletは`L.Class`を基盤とした独自のクラスシステムを使用しています。

### 基本的な継承

```javascript
var MyClass = L.Class.extend({
    initialize: function(name) {
        this.name = name;
    },

    greet: function() {
        return 'Hello, ' + this.name + '!';
    }
});

var instance = new MyClass('World');
console.log(instance.greet());  // "Hello, World!"
```

## 1. カスタムマーカーの作成

### シンプルなカスタムマーカー

```javascript
// カスタムマーカークラスを定義
L.CustomMarker = L.Marker.extend({
    options: {
        // デフォルトオプション
        customProperty: 'default value'
    },

    initialize: function(latlng, options) {
        // 親クラスのinitializeを呼び出し
        L.Marker.prototype.initialize.call(this, latlng, options);

        // カスタム初期化処理
        console.log('CustomMarker created with:', this.options.customProperty);
    },

    // カスタムメソッド
    customMethod: function() {
        console.log('Custom method called!');
        return this;
    }
});

// ファクトリー関数（小文字）
L.customMarker = function(latlng, options) {
    return new L.CustomMarker(latlng, options);
};

// 使用例
var map = L.map('map').setView([35.6812, 139.7671], 13);

L.tileLayer('https://tile.openstreetmap.org/{z}/{x}/{y}.png', {
    maxZoom: 19,
    attribution: '© OpenStreetMap contributors'
}).addTo(map);

var marker = L.customMarker([35.6812, 139.7671], {
    customProperty: 'custom value'
}).addTo(map);

marker.customMethod();
```

### インタラクティブなカスタムマーカー

```javascript
L.AnimatedMarker = L.Marker.extend({
    initialize: function(latlng, options) {
        L.Marker.prototype.initialize.call(this, latlng, options);
        this._pulseAnimation = null;
    },

    onAdd: function(map) {
        // 親クラスのonAddを呼び出し
        L.Marker.prototype.onAdd.call(this, map);

        // マーカーが地図に追加されたときの処理
        this.startPulse();
    },

    onRemove: function(map) {
        // マーカーが地図から削除されたときの処理
        this.stopPulse();
        L.Marker.prototype.onRemove.call(this, map);
    },

    startPulse: function() {
        var icon = this._icon;
        if (!icon) return;

        icon.style.transition = 'transform 1s ease-in-out';

        this._pulseAnimation = setInterval(function() {
            icon.style.transform = icon.style.transform === 'scale(1.5)'
                ? 'scale(1)'
                : 'scale(1.5)';
        }, 1000);
    },

    stopPulse: function() {
        if (this._pulseAnimation) {
            clearInterval(this._pulseAnimation);
            this._pulseAnimation = null;
        }
    }
});

L.animatedMarker = function(latlng, options) {
    return new L.AnimatedMarker(latlng, options);
};

// 使用例
L.animatedMarker([35.6812, 139.7671]).addTo(map)
    .bindPopup('アニメーションするマーカー');
```

## 2. カスタムレイヤーの作成

### 基本的なカスタムレイヤー

```javascript
L.CustomLayer = L.Layer.extend({
    initialize: function(options) {
        L.setOptions(this, options);
    },

    onAdd: function(map) {
        // レイヤーが地図に追加されたとき
        this._map = map;

        // DOM要素を作成
        this._container = L.DomUtil.create('div', 'custom-layer');
        this._container.style.position = 'absolute';
        this._container.style.top = '0';
        this._container.style.left = '0';
        this._container.innerHTML = '<h2 style="color: red; padding: 10px; background: white;">カスタムレイヤー</h2>';

        // 地図のペインに追加
        map.getPanes().overlayPane.appendChild(this._container);

        // 地図の移動イベントをリスン
        map.on('move', this._update, this);
        this._update();
    },

    onRemove: function(map) {
        // レイヤーが地図から削除されたとき
        L.DomUtil.remove(this._container);
        map.off('move', this._update, this);
    },

    _update: function() {
        // 地図の移動に応じて位置を更新
        var pos = this._map.latLngToLayerPoint([35.6812, 139.7671]);
        L.DomUtil.setPosition(this._container, pos);
    }
});

L.customLayer = function(options) {
    return new L.CustomLayer(options);
};

// 使用例
L.customLayer().addTo(map);
```

### Canvasベースのカスタムレイヤー

```javascript
L.CanvasLayer = L.Layer.extend({
    initialize: function(drawFunction, options) {
        this._drawFunction = drawFunction;
        L.setOptions(this, options);
    },

    onAdd: function(map) {
        this._map = map;

        // Canvas要素を作成
        this._canvas = L.DomUtil.create('canvas', 'leaflet-canvas-layer');
        this._canvas.style.position = 'absolute';

        // サイズを設定
        var size = map.getSize();
        this._canvas.width = size.x;
        this._canvas.height = size.y;

        this._ctx = this._canvas.getContext('2d');

        // 地図のペインに追加
        map.getPanes().overlayPane.appendChild(this._canvas);

        // イベントリスナーを登録
        map.on('moveend', this._reset, this);
        map.on('resize', this._resize, this);

        this._reset();
    },

    onRemove: function(map) {
        L.DomUtil.remove(this._canvas);
        map.off('moveend', this._reset, this);
        map.off('resize', this._resize, this);
    },

    _resize: function() {
        var size = this._map.getSize();
        this._canvas.width = size.x;
        this._canvas.height = size.y;
        this._reset();
    },

    _reset: function() {
        var topLeft = this._map.containerPointToLayerPoint([0, 0]);
        L.DomUtil.setPosition(this._canvas, topLeft);
        this._draw();
    },

    _draw: function() {
        // キャンバスをクリア
        this._ctx.clearRect(0, 0, this._canvas.width, this._canvas.height);

        // カスタム描画関数を呼び出し
        if (this._drawFunction) {
            this._drawFunction(this._ctx, this._map);
        }
    }
});

L.canvasLayer = function(drawFunction, options) {
    return new L.CanvasLayer(drawFunction, options);
};

// 使用例
var drawFunction = function(ctx, map) {
    // 円を描画
    var center = map.latLngToContainerPoint([35.6812, 139.7671]);

    ctx.beginPath();
    ctx.arc(center.x, center.y, 50, 0, 2 * Math.PI);
    ctx.fillStyle = 'rgba(255, 0, 0, 0.5)';
    ctx.fill();
    ctx.strokeStyle = 'red';
    ctx.lineWidth = 3;
    ctx.stroke();

    // テキストを描画
    ctx.font = '16px Arial';
    ctx.fillStyle = 'black';
    ctx.fillText('カスタム描画', center.x - 40, center.y + 70);
};

L.canvasLayer(drawFunction).addTo(map);
```

## 3. カスタムコントロールの作成

### 基本的なカスタムコントロール

```javascript
L.Control.CustomControl = L.Control.extend({
    options: {
        position: 'topright'
    },

    onAdd: function(map) {
        // コンテナを作成
        var container = L.DomUtil.create('div', 'leaflet-bar leaflet-control');
        container.style.backgroundColor = 'white';
        container.style.padding = '10px';

        // ボタンを作成
        var button = L.DomUtil.create('button', '', container);
        button.innerHTML = 'クリック';
        button.style.cursor = 'pointer';

        // クリックイベント
        L.DomEvent.on(button, 'click', function(e) {
            L.DomEvent.stopPropagation(e);
            alert('カスタムコントロールがクリックされました！');
        });

        // 地図のクリックイベントが伝播しないようにする
        L.DomEvent.disableClickPropagation(container);

        return container;
    },

    onRemove: function(map) {
        // クリーンアップ処理
    }
});

L.control.customControl = function(opts) {
    return new L.Control.CustomControl(opts);
};

// 使用例
L.control.customControl({ position: 'topright' }).addTo(map);
```

### 実用的なカスタムコントロール：座標表示

```javascript
L.Control.CoordinateDisplay = L.Control.extend({
    onAdd: function(map) {
        this._map = map;
        this._container = L.DomUtil.create('div', 'leaflet-bar leaflet-control');
        this._container.style.backgroundColor = 'white';
        this._container.style.padding = '5px 10px';
        this._container.innerHTML = '座標: --';

        // マウス移動イベント
        map.on('mousemove', this._onMouseMove, this);

        return this._container;
    },

    onRemove: function(map) {
        map.off('mousemove', this._onMouseMove, this);
    },

    _onMouseMove: function(e) {
        var lat = e.latlng.lat.toFixed(5);
        var lng = e.latlng.lng.toFixed(5);
        this._container.innerHTML = '座標: ' + lat + ', ' + lng;
    }
});

L.control.coordinateDisplay = function(opts) {
    return new L.Control.CoordinateDisplay(opts);
};

// 使用例
L.control.coordinateDisplay({ position: 'bottomleft' }).addTo(map);
```

## 4. プラグインの作成

### シンプルなプラグイン

```javascript
(function() {
    // プラグインのネームスペース
    L.MyPlugin = {
        version: '1.0.0'
    };

    // マップにメソッドを追加
    L.Map.include({
        myPluginMethod: function() {
            console.log('My plugin method called!');
            return this;  // メソッドチェーン用
        }
    });

    // マーカーにメソッドを追加
    L.Marker.include({
        highlight: function() {
            // マーカーをハイライト
            var icon = this._icon;
            if (icon) {
                icon.style.filter = 'brightness(1.5)';
                setTimeout(function() {
                    icon.style.filter = '';
                }, 1000);
            }
            return this;
        }
    });
})();

// 使用例
map.myPluginMethod();

var marker = L.marker([35.6812, 139.7671]).addTo(map);
marker.highlight();
```

## 5. イベントシステムの活用

### カスタムイベントの発火とリスン

```javascript
L.CustomEventLayer = L.Layer.extend({
    initialize: function() {
        // Leaflet.Evented を継承しているのでイベント機能が使える
    },

    onAdd: function(map) {
        this._map = map;

        // 3秒後にカスタムイベントを発火
        setTimeout(function() {
            this.fire('customEvent', {
                message: 'カスタムイベントが発火しました！',
                timestamp: Date.now()
            });
        }.bind(this), 3000);
    },

    triggerEvent: function(data) {
        this.fire('dataUpdated', { data: data });
    }
});

// 使用例
var customLayer = new L.CustomEventLayer();

// カスタムイベントをリスン
customLayer.on('customEvent', function(e) {
    console.log('イベント受信:', e.message);
    alert(e.message);
});

customLayer.on('dataUpdated', function(e) {
    console.log('データ更新:', e.data);
});

customLayer.addTo(map);

// イベントを手動で発火
customLayer.triggerEvent({ value: 42 });
```

## 6. 実践例：カスタム測定ツール

```javascript
L.Control.MeasureTool = L.Control.extend({
    options: {
        position: 'topright'
    },

    onAdd: function(map) {
        this._map = map;
        this._measuring = false;
        this._points = [];
        this._polyline = null;

        // コンテナを作成
        var container = L.DomUtil.create('div', 'leaflet-bar leaflet-control');
        container.style.backgroundColor = 'white';

        // ボタンを作成
        this._button = L.DomUtil.create('button', '', container);
        this._button.innerHTML = '📏 測定開始';
        this._button.style.padding = '5px 10px';
        this._button.style.cursor = 'pointer';

        L.DomEvent.on(this._button, 'click', this._toggleMeasure, this);
        L.DomEvent.disableClickPropagation(container);

        return container;
    },

    _toggleMeasure: function() {
        if (!this._measuring) {
            this._startMeasure();
        } else {
            this._stopMeasure();
        }
    },

    _startMeasure: function() {
        this._measuring = true;
        this._button.innerHTML = '📏 測定終了';
        this._button.style.backgroundColor = '#ffcccc';
        this._points = [];

        this._map.on('click', this._addPoint, this);
        this._map.getContainer().style.cursor = 'crosshair';
    },

    _stopMeasure: function() {
        this._measuring = false;
        this._button.innerHTML = '📏 測定開始';
        this._button.style.backgroundColor = '';

        this._map.off('click', this._addPoint, this);
        this._map.getContainer().style.cursor = '';

        if (this._polyline) {
            this._map.removeLayer(this._polyline);
            this._polyline = null;
        }
        this._points = [];
    },

    _addPoint: function(e) {
        this._points.push(e.latlng);

        // 線を更新
        if (this._polyline) {
            this._map.removeLayer(this._polyline);
        }

        this._polyline = L.polyline(this._points, {
            color: 'red',
            weight: 3,
            dashArray: '5, 10'
        }).addTo(this._map);

        // 距離を計算
        if (this._points.length > 1) {
            var totalDistance = 0;
            for (var i = 1; i < this._points.length; i++) {
                totalDistance += this._points[i - 1].distanceTo(this._points[i]);
            }

            // ポップアップで距離を表示
            var distanceText = totalDistance < 1000
                ? totalDistance.toFixed(2) + ' m'
                : (totalDistance / 1000).toFixed(2) + ' km';

            L.popup()
                .setLatLng(e.latlng)
                .setContent('距離: ' + distanceText)
                .openOn(this._map);
        }
    }
});

L.control.measureTool = function(opts) {
    return new L.Control.MeasureTool(opts);
};

// 使用例
L.control.measureTool({ position: 'topright' }).addTo(map);
```

## 7. プラグインのベストプラクティス

### プラグインの構造

```javascript
(function(factory, window) {
    // UMD (Universal Module Definition) パターン
    if (typeof define === 'function' && define.amd) {
        // AMD
        define(['leaflet'], factory);
    } else if (typeof exports === 'object') {
        // Node/CommonJS
        module.exports = factory(require('leaflet'));
    } else {
        // ブラウザグローバル
        if (typeof window.L === 'undefined') {
            throw new Error('Leaflet must be loaded first');
        }
        factory(window.L);
    }
}(function(L) {
    'use strict';

    // プラグインのコード
    L.MyPlugin = L.Class.extend({
        options: {
            // デフォルトオプション
        },

        initialize: function(options) {
            L.setOptions(this, options);
        },

        // メソッド
        myMethod: function() {
            // ...
        }
    });

    // ファクトリー関数
    L.myPlugin = function(options) {
        return new L.MyPlugin(options);
    };

    // バージョン情報
    L.MyPlugin.version = '1.0.0';

    return L.MyPlugin;

}, window));
```

## よくある質問

### Q: クラスの継承がうまくいかない

**A:** `L.Class.extend()`を使用し、親クラスのメソッドを正しく呼び出してください：

```javascript
initialize: function(latlng, options) {
    L.Marker.prototype.initialize.call(this, latlng, options);
}
```

### Q: イベントが発火しない

**A:** `L.Evented`を継承しているか確認してください。また、`fire()`メソッドを正しく呼び出しているか確認。

### Q: メモリリークを防ぐには？

**A:** `onRemove`メソッドで必ずイベントリスナーを削除してください：

```javascript
onRemove: function(map) {
    map.off('click', this._onClick, this);
    // その他のクリーンアップ
}
```

## おすすめのLeafletプラグイン

### 人気のプラグイン

1. **[Leaflet.markercluster](https://github.com/Leaflet/Leaflet.markercluster)** - マーカークラスタリング
2. **[Leaflet.draw](https://github.com/Leaflet/Leaflet.draw)** - 図形描画ツール
3. **[Leaflet.heat](https://github.com/Leaflet/Leaflet.heat)** - ヒートマップ
4. **[Leaflet.fullscreen](https://github.com/brunob/leaflet.fullscreen)** - フルスクリーン表示
5. **[Leaflet.awesome-markers](https://github.com/lennardv2/Leaflet.awesome-markers)** - Font Awesomeアイコン

## 参考リンク

- [Leaflet Plugin Tutorial](https://leafletjs.com/examples/extending/extending-1-classes.html)
- [Leaflet API Reference](https://leafletjs.com/reference.html)
- [Leaflet Plugins](https://leafletjs.com/plugins.html)
- [GitHub: Leaflet](https://github.com/Leaflet/Leaflet)

## まとめ

このチュートリアルで、Leafletの拡張方法の基礎を学びました。カスタムクラス、レイヤー、コントロール、プラグインを作成することで、Leafletの機能を大幅に拡張できます。

**次のステップ**：
- 実際のプロジェクトでカスタムクラスを作成してみる
- 既存のプラグインのソースコードを読んで学ぶ
- 自分のプラグインを作成してGitHubで公開する
