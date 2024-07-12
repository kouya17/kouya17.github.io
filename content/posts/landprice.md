---
title: "公開されている「地価公示データ」を見てみる"
date: 2024-07-13T03:58:00+09:00
tags: ["python", "GIS"]
---

## 今回利用するデータ

以下の国土交通省国土数値情報ダウンロードサイトから取得できる「地価公示データ(2024年版)」を利用する。

https://nlftp.mlit.go.jp/ksj/gml/datalist/KsjTmplt-L01-2024.html

このデータには毎年国土交通省が実施している「地価公示」に関するデータが入っている。例えば、調査の対象となった土地(標準地)の位置・公示価格・利用現況・用途地域・地積などが整理されている。

データフォーマットは以下の3種類が利用できる。
* GML形式
* GeoJSON形式
* シェープファイル形式

今回はJSONを元にしたデータフォーマットであるGeoJSON形式を利用する。

## 最初の4行だけ見てみる

まずは北海道のデータをダウンロードして、最初の方だけを見てみる。最初の4行は以下のようになっている。

```
{
    "type": "FeatureCollection",
    "name": "L01-24_01",
    "features": [
        { "type": "Feature", "geometry": { "type": "Point", "coordinates": [ 141.31404500, 43.05544889] }, "properties": { "L01_001": "01101",...
```

構造としては"features"キーに各標準地のデータが格納されている。上記データは"..."以降を削除してしまっているが、"..."以降にずらっとその標準値におけるデータが並んでいる。各標準地にはそれぞれ144個のデータが紐づいている。1個1個見ていくとキリがないため、2024年の公示価格(キー名"L01_103")だけ見ていく。

## 公示価格が最も高い場所と最も安い場所を見てみる

今回はPythonを使ってデータを読んでみる。以下のスクリプトを実行し、2024年の公示価格が最も高い場所と公示価格が最も安い場所を調べてみる。

```
import json

# geojsonファイルの読み込み
all_data = {}
with open('L01-24_GML/L01-24.geojson', encoding='utf-8') as input:
  all_data = json.load(input)

features = all_data['features']

# 最初の要素の値で初期化
max_feature = features[0]
min_feature = features[0]

# リストをループして最大値を抽出
for feature in features:
    if feature['properties']['L01_103'] > max_feature['properties']['L01_103']:
        max_feature = feature
    if feature['properties']['L01_103'] < min_feature['properties']['L01_103']:
        min_feature = feature

print("最大値:", max_feature['properties']['L01_103'], "地番:", max_feature['properties']['L01_025'])
print("最小値:", min_feature['properties']['L01_103'], "地番:", min_feature['properties']['L01_025'])
```

実行結果は以下のようになった。

```
最大値: 55700000 地番: 東京都　中央区銀座４丁目２番４
最小値: 470 地番: 北海道　勇払郡厚真町字軽舞２８０番外
```

公示価格が一番高い場所は「東京都　中央区銀座４丁目２番４」で、1㎡あたり5570万円だった。ニュースサイトの情報を見ても同じ情報が出ていたため、正しくデータが取れていそうだった。

公示価格が一番安い場所は「北海道　勇払郡厚真町字軽舞２８０番外」で、1㎡あたり470円だった。この場所は市街化調整区域で、グーグルマップを見ると水田が広がっていた。