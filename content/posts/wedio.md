---
title: "音声ライブ配信ができるWebアプリを作成した"
date: 2023-03-05T15:33:00+09:00
tags: ["golang", "ffmpeg"]
cover:
  image: "media/covers/wedio/cover.jpg"
---

## デモ

本Webアプリは以下からアクセスできる。

<iframe class="hatenablogcard" style="width:100%;height:155px;margin:15px 0;max-width:680px;" title="WEDIO.JP | 音声ライブ配信" src="https://hatenablog.com/embed?url=https://www.wedio.jp/" frameborder="0" scrolling="no"></iframe>

動作時の動画は以下の通り。

![デモ動画](/media/photos/wedio/out.gif)

## 機能一覧

* WebRTCによる音声のインジェスト
  * 連続稼働は30分まで
* HLSによる音声の配信
* 各配信ごとのチャットルームの作成・参加
* 配信終了1時間後のデータ自動削除

## 音声データの流れ

本アプリケーションにおける音声データの流れを図示すると以下のようになる。

![全体構成](/media/photos/wedio/wedio.png)

## 付属機能補足

### チャット

チャットは簡易的にWebSocketを利用して実装している。

配信ごとのルーム管理のため、配信開始時に別プロセスでチャット用プログラムを都度実行する形になっている("管理"といえるほどのものではない)。

### データ自動削除

サーバー側で、音声ファイルが格納されているディレクトリの最終更新日時をcronで定期的に監視し、最終更新日時が1時間より前のものを削除している。