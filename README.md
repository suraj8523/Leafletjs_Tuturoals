# Leaflet.js 日本語チュートリアル

Leaflet.jsの初心者から上級者まで対応した包括的な日本語チュートリアル集です。各チュートリアルは独立したマークダウンファイルで提供され、実用的なサンプルコードとともに段階的に学習できます。

## 📚 チュートリアル一覧

### 🌱 超初心者向け

#### [01. クイックスタート：最初のLeafletマップを作成する](01_quick_start.md)
**難易度：** ⭐

Leaflet.jsの基礎を学びます。HTMLファイルのセットアップから、地図の表示、マーカーの追加、ポップアップ、図形の描画まで、最初の一歩を踏み出すための完全ガイドです。

**学習内容：**
- Leaflet.jsの基本的なセットアップ
- 地図の初期化と表示
- マーカー、円、ポリゴン、ポリラインの追加
- ポップアップの実装

---

### 🌿 初心者向け

#### [02. カスタムアイコンでマーカーをカスタマイズする](02_markers_custom_icons.md)
**難易度：** ⭐⭐

デフォルトマーカーから脱却し、独自のアイコンを使用してマップを個性的にします。カスタムアイコンの作成、複数アイコンの管理、DivIconの活用方法を学びます。

**学習内容：**
- カスタムアイコンの定義と使用
- アイコンのサイズとアンカーポイントの設定
- 複数の異なるアイコンの管理
- DivIcon（HTML/CSSベース）の活用
- Font Awesomeアイコンの統合

#### [03. モバイル対応のフルスクリーンマップ](03_mobile_map.md)
**難易度：** ⭐⭐

スマートフォンやタブレットで最適に動作するマップを作成します。フルスクリーン表示、現在位置の取得、リアルタイム追跡など、モバイル特化の機能を実装します。

**学習内容：**
- フルスクリーンマップの設定
- レスポンシブデザインの実装
- ユーザーの現在位置の取得と表示
- 位置情報のリアルタイム追跡
- カスタムコントロールの追加

---

### 🌳 初級〜中級向け

#### [04. GeoJSONデータを使って地図を表示する](04_geojson.md)
**難易度：** ⭐⭐⭐

GeoJSON形式の地理空間データをLeafletで扱う方法を学びます。外部データの読み込み、スタイルのカスタマイズ、フィルタリング、インタラクティブな機能の実装まで網羅します。

**学習内容：**
- GeoJSONフォーマットの基礎
- GeoJSONデータの読み込みと表示
- フィーチャーごとのスタイルカスタマイズ
- ポップアップとツールチップの追加
- 外部GeoJSONファイルの読み込み
- データのフィルタリングとインタラクション

---

### 🌲 中級向け

#### [05. レイヤーグループとレイヤーコントロール](05_layer_groups_control.md)
**難易度：** ⭐⭐⭐

複数のレイヤーを効率的に管理し、ユーザーが切り替えられるコントロールを実装します。ベースマップの切り替えやオーバーレイの表示/非表示など、高度なレイヤー管理を学びます。

**学習内容：**
- レイヤーグループの作成と管理
- レイヤーコントロールの追加
- ベースマップの切り替え
- オーバーレイレイヤーの管理
- 動的なレイヤー追加と削除
- レイヤーイベントの処理

#### [06. インタラクティブなコロプレスマップ](06_choropleth_map.md)
**難易度：** ⭐⭐⭐

データに基づいた色分け地図（コロプレスマップ）を作成します。統計データの可視化、インタラクティブなホバー効果、情報パネル、凡例の実装を学びます。

**学習内容：**
- コロプレスマップの概念
- データに基づいた色分け
- インタラクティブなホバー効果
- 情報パネルの実装
- 凡例（レジェンド）の追加
- データ可視化のベストプラクティス

---

### 🌴 中級〜上級向け

#### [07. WMSとTMSサービスの統合](07_wms_tms.md)
**難易度：** ⭐⭐⭐⭐

Web Map Service（WMS）とTile Map Service（TMS）を統合し、外部の地図サービスを活用します。地理院タイルなどの実用的なサービスとの連携方法を学びます。

**学習内容：**
- WMS（Web Map Service）の基礎
- TMS（Tile Map Service）の基礎
- WMSレイヤーの追加とパラメータ設定
- 複数のWMS/TMSレイヤーの管理
- GetFeatureInfoの実装
- 地理院タイルの活用

---

### 🌵 上級向け

#### [08. 画像・ビデオ・SVGオーバーレイ](08_overlays.md)
**難易度：** ⭐⭐⭐⭐

地図上に画像、ビデオ、SVGなどのメディアを重ねて表示します。古地図の重ね合わせ、建物のフロアマップ、カスタムグラフィックスなど、高度なオーバーレイ技術を習得します。

**学習内容：**
- 画像オーバーレイの追加と設定
- ビデオオーバーレイの実装
- SVGオーバーレイの使用
- 座標境界の指定方法
- インタラクティブなオーバーレイ
- フロアマップの実装

#### [09. マップペインの操作](09_map_panes.md)
**難易度：** ⭐⭐⭐⭐⭐

Leafletのマップペインシステムを理解し、レイヤーの表示順序を細かく制御します。カスタムペインの作成、z-indexの管理など、高度なレイヤー制御を学びます。

**学習内容：**
- マップペインの概念と仕組み
- デフォルトペインの理解
- カスタムペインの作成
- レイヤーの表示順序の制御
- z-indexの管理
- 透過レイヤーとクリックイベントの制御

#### [10. Leafletの拡張：カスタムクラスとプラグイン](10_extending_leaflet.md)
**難易度：** ⭐⭐⭐⭐⭐

Leafletのクラスシステムを理解し、独自の機能を追加します。カスタムマーカー、レイヤー、コントロール、プラグインの作成方法を学び、Leafletを自在に拡張できるようになります。

**学習内容：**
- Leafletのクラスシステム
- カスタムマーカーの作成
- カスタムレイヤーの実装
- カスタムコントロールの開発
- プラグインの作成方法
- イベントシステムの活用
- ベストプラクティス

---

## 🚀 はじめ方

### 1. 前提条件

- HTML/CSSの基礎知識
- JavaScriptの基本的な理解
- テキストエディタ（VS Code、Sublime Text など）
- モダンなWebブラウザ

### 2. 推奨学習順序

各チュートリアルは独立していますが、以下の順序で学習することをおすすめします：

```
01_quick_start.md（必須）
    ↓
02_markers_custom_icons.md
    ↓
03_mobile_map.md
    ↓
04_geojson.md
    ↓
05_layer_groups_control.md
    ↓
06_choropleth_map.md
    ↓
07_wms_tms.md
    ↓
08_overlays.md
    ↓
09_map_panes.md
    ↓
10_extending_leaflet.md
```

### 3. サンプルコードの実行方法

各チュートリアルには完全に動作するサンプルコードが含まれています。

1. HTMLファイルを作成
2. サンプルコードをコピー＆ペースト
3. ブラウザで開く

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Leafletサンプル</title>
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
    <style>
        #map { height: 600px; }
    </style>
</head>
<body>
    <div id="map"></div>
    <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
    <script>
        // ここにコードを記述
    </script>
</body>
</html>
```

## 📖 各チュートリアルの特徴

### ✅ 完全なサンプルコード
すべてのチュートリアルにコピー＆ペーストで動作する完全なコードを掲載

### 💡 実用的な例
実際のプロジェクトで使える実践的なサンプルを提供

### 🔍 詳細な解説
各コードの意味と使い方を丁寧に説明

### ❓ よくある質問
初心者が陥りやすい問題とその解決方法を掲載

### 🔗 参考リンク
公式ドキュメントや関連リソースへのリンクを提供

## 🛠️ 使用するツールとリソース

### 必須ツール

- **[Leaflet.js](https://leafletjs.com/)** - 本チュートリアルのメインライブラリ（v1.9.4）
- **[OpenStreetMap](https://www.openstreetmap.org/)** - 無料の地図タイル

### 推奨ツール

- **[GeoJSON.io](https://geojson.io/)** - GeoJSONの作成・編集
- **[ColorBrewer](https://colorbrewer2.org/)** - カラースキームの選択
- **[Font Awesome](https://fontawesome.com/)** - アイコンライブラリ
- **[国土地理院タイル](https://maps.gsi.go.jp/development/ichiran.html)** - 日本の地図データ

### 開発者ツール

- **ブラウザの開発者ツール** - デバッグに必須
- **[VS Code](https://code.visualstudio.com/)** - 推奨エディタ
- **[Live Server](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer)** - VS Code拡張機能

## 🎯 学習目標

このチュートリアルを完了すると、以下のことができるようになります：

✅ インタラクティブな地図アプリケーションの作成
✅ GeoJSONデータの可視化
✅ モバイル対応のレスポンシブマップの実装
✅ カスタムコントロールとプラグインの開発
✅ 外部地図サービスとの統合
✅ データに基づいた統計地図の作成
✅ Leafletの高度なカスタマイズ

## 💬 よくある質問（FAQ）

### Q: プログラミング初心者でも大丈夫ですか？

A: HTML/CSS/JavaScriptの基礎知識があれば、01_quick_start.mdから始めることで段階的に学習できます。

### Q: どのブラウザがサポートされていますか？

A: Leaflet.jsは以下のブラウザをサポートしています：
- Chrome（推奨）
- Firefox
- Safari
- Edge
- Internet Explorer 11（一部機能制限あり）

### Q: 商用プロジェクトで使用できますか？

A: はい。Leaflet.jsはBSD-2-Clauseライセンスで、商用利用可能です。ただし、使用する地図タイルのライセンスには注意が必要です。

### Q: サンプルコードが動作しません

A: 以下を確認してください：
1. Leaflet CSSとJSが正しく読み込まれているか
2. マップコンテナに高さが指定されているか
3. ブラウザの開発者ツールでエラーが出ていないか

## 🌟 関連リソース

### 公式リソース

- [Leaflet公式サイト](https://leafletjs.com/)
- [Leaflet公式ドキュメント](https://leafletjs.com/reference.html)
- [Leaflet公式チュートリアル](https://leafletjs.com/examples.html)
- [Leaflet GitHub](https://github.com/Leaflet/Leaflet)

### コミュニティ

- [Stack Overflow - Leaflet Tag](https://stackoverflow.com/questions/tagged/leaflet)
- [Leaflet Plugins](https://leafletjs.com/plugins.html)
- [GIS Stack Exchange](https://gis.stackexchange.com/)

### データソース

- [Natural Earth](https://www.naturalearthdata.com/) - 世界の地理データ
- [国土地理院](https://www.gsi.go.jp/) - 日本の地理データ
- [e-Stat](https://www.e-stat.go.jp/) - 日本の統計データ
- [OpenStreetMap](https://www.openstreetmap.org/) - オープンソース地図データ

## 📝 ライセンス

このチュートリアルは教育目的で作成されています。サンプルコードは自由に使用・改変できます。

Leaflet.js自体は[BSD-2-Clauseライセンス](https://github.com/Leaflet/Leaflet/blob/main/LICENSE)で提供されています。

## 🤝 貢献

このチュートリアルは[Leaflet公式サイト](https://leafletjs.com/examples.html)を参考に、日本語話者向けに作成されました。

間違いや改善点がありましたら、GitHubのIssuesまでお知らせください。

## 📅 更新履歴

### 2025-01-14
- **初回リリース** - Leaflet.js v1.9.4対応
- 全10チュートリアルを公開
  - 01. クイックスタート（超初心者向け）
  - 02. カスタムアイコン（初心者向け）
  - 03. モバイル対応マップ（初心者向け）
  - 04. GeoJSONデータの使用（初級〜中級向け）
  - 05. レイヤーグループとコントロール（中級向け）
  - 06. インタラクティブなコロプレスマップ（中級向け）
  - 07. WMS/TMSサービスの統合（中級〜上級向け）
  - 08. 画像・ビデオ・SVGオーバーレイ（上級向け）
  - 09. マップペインの操作（上級向け）
  - 10. Leafletの拡張方法（上級向け）
- README.md作成（チュートリアル一覧と学習ガイド）

---

**Happy Mapping! 🗺️**

Leaflet.jsを使って、素晴らしい地図アプリケーションを作成しましょう！
