---
title: "raspberryPi Zero W を使ってタイムラプス生成デバイスを作成する"
date: 2022-03-21T13:03:00+09:00
tags: ["raspi", "ffmpeg"]
cover:
  image: "media/covers/timelapse-maker/cover.jpg"
---

## デモ

本デバイスは以下の動画のように操作できる。

<blockquote class="twitter-tweet"><p lang="ja" dir="ltr">操作時の動画は以下のような感じです。 <a href="https://t.co/7l2ocbjlh1">pic.twitter.com/7l2ocbjlh1</a></p>&mdash; 青木晃也 (@aoki_kouya) <a href="https://twitter.com/aoki_kouya/status/1505597869226872838?ref_src=twsrc%5Etfw">March 20, 2022</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

本デバイスを使うと、以下のサイトにあるような動画を生成できる。

<iframe class="hatenablogcard" style="width:100%;height:155px;margin:15px 0;max-width:680px;" title="Cooking Timelapse Vlog" src="https://hatenablog.com/embed?url=https://cook.kouya17.com/" frameborder="0" scrolling="no"></iframe>

動画の出力条件のうち、主な項目は以下の通り。

- 再生速度: 30倍速
- fps: 30
- bps: 3Mbps
- HLS形式
- 音声なし

料理の動画を掲載しているのは、特に他にコンスタントに撮影できる対象が考えつかなかったため。これらの動画の撮影のために、3Dプリンターでプリントした部品を使ってラズパイとカメラを固定し、台所上部に吊り下げている。

<blockquote class="twitter-tweet"><p lang="ja" dir="ltr">Raspberry Pi Zeroを使って、思い付きでタイムラプスカメラを作りました。 <a href="https://t.co/w1fCBfPVzm">pic.twitter.com/w1fCBfPVzm</a></p>&mdash; 青木晃也 (@aoki_kouya) <a href="https://twitter.com/aoki_kouya/status/1500078476971626499?ref_src=twsrc%5Etfw">March 5, 2022</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

## 確認済み動作条件

- ボード
  - Raspberry Pi Zero W
- OS
  - Raspberry Pi OS(Legacy) 2022-01-28

現時点(2022/03/21)で推奨OSとなっているRaspberry Pi OSは`bullseye`だが、`buster`(Legacy)の方を使う。これは、`ffmpeg`(今回利用するマルチメディア処理ソフト)まわりの環境が`bullseye`と`buster`で異なっているため。`bullseye`を利用する場合、ハードウェアエンコードを有効にするために、`ffmpeg`をビルドする必要が出てくる。

## 必要な部品

![部品一覧](/media/photos/timelapse-maker/parts.jpg)

- Raspberry Pi Zero W ([スイッチサイエンス](https://www.switch-science.com/catalog/3200/))
- microSDカード ([Amazon](https://amzn.to/3ucAv2G))
- Raspberry Pi カメラモジュール ([スイッチサイエンス](https://www.switch-science.com/catalog/2713/))
- Raspberry Pi Zero用カメラケーブル ([スイッチサイエンス](https://www.switch-science.com/catalog/3195/))
- ELECROW 3.5inch RPi Display ([Amazon](https://amzn.to/3tpFKwy)) or 他のタッチパネルモニター

結構前に購入したタッチパネルモニターを再利用していたので、さすがにもう入手できないかなと思っていたが、まだ販売していて驚いた。

## microSDカードへOSをインストールする

まずはmicroSDカードをフォーマットする。以下のページから、`SD Card Formatter`をダウンロード、インストールする。

<iframe class="hatenablogcard" style="width:100%;height:155px;margin:15px 0;max-width:680px;" title="SDメモリカードフォーマッター | SD Association" src="https://hatenablog.com/embed?url=https://www.sdcard.org/ja/downloads-2/formatter-2/" frameborder="0" scrolling="no"></iframe>

インストールした`SD Card Formatter`を起動する。microSDカードのドライブを選択し、フォーマットを実行する。フォーマットオプションはクイックフォーマットを選択すればよい。

![microSDのフォーマット完了画面](/media/photos/timelapse-maker/sd.png)

SDカードが初期化出来たので、OSをインストールする。以下のページから、`Raspberry Pi Imager`をダウンロード、インストールする。

<iframe class="hatenablogcard" style="width:100%;height:155px;margin:15px 0;max-width:680px;" title="Raspberry Pi OS – Raspberry Pi" src="https://hatenablog.com/embed?url=https://www.raspberrypi.com/software/" frameborder="0" scrolling="no"></iframe>

`Raspiberry Pi Imager`を起動する。OSは Raspberry Pi OS(other) → Raspberry Pi OS(Legacy) を選択する。

![OS選択画面](/media/photos/timelapse-maker/imager.png)

Storageは microSDカード を選択し、書き込みを行う。

## 無線LAN接続・SSH接続が出来るようにする

無線LAN接続及びSSHによる接続ができるように設定する。これらの設定はいくつかやり方があると思うが、microSDカード内のファイルを追加・編集することでも設定できる。ここら辺は以下のような参考になる記事が色々ある。

<iframe class="hatenablogcard" style="width:100%;height:155px;margin:15px 0;max-width:680px;" title="Raspberry PiをHeadlessでセットアップする - Qiita" src="https://hatenablog.com/embed?url=https://qiita.com/rhene/items/828858afae699c5ac0ab" frameborder="0" scrolling="no"></iframe>

<iframe class="hatenablogcard" style="width:100%;height:155px;margin:15px 0;max-width:680px;" title="Raspberry Piの無線LANをmicroSDで簡単に設定するためのツールを作った - Qiita" src="https://hatenablog.com/embed?url=https://qiita.com/mascii/items/a43d71572e1919e56398" frameborder="0" scrolling="no"></iframe>

## タッチパネルモニターの設定を行う

本デバイスはタッチパネルから操作を行う想定のため、タッチパネルモニターの設定が必要になる。各自使用するモニターに応じて設定を行う。

上記で紹介した[ELECROW 3.5inch RPi Display](https://amzn.to/3tpFKwy)を利用する場合は、以下のページを参考にドライバをインストールする。このインストール作業時、`/boot/config.txt`が上書きされるようなので、自分で`/boot/config.txt`に追加設定等を行っている場合は注意。

<iframe class="hatenablogcard" style="width:100%;height:155px;margin:15px 0;max-width:680px;" title="3.5 Inch 480x320 TFT Display with Touch Screen for Raspberry Pi - Elecrow" src="https://hatenablog.com/embed?url=https://www.elecrow.com/wiki/index.php?title=3.5_Inch_480x320_TFT_Display_with_Touch_Screen_for_Raspberry_Pi" frameborder="0" scrolling="no"></iframe>

## カメラモジュールを有効化する

ラズパイにログイン後、以下コマンドを実行し、設定画面を表示する。

```
sudo raspi-config
```

3 Interface Options → P1 Camera → Yes と選択し、カメラモジュールを有効化する。

再起動するかどうか聞かれるので、Yesを選択し、再起動する。

## タイムラプス生成スクリプト一式をインストールする

以下コマンドを実行し、スクリプト一式をインストールする。

```
cd /home/pi
git clone https://github.com/kouya17/raspi_timelapse_maker.git
cd raspi_timelapse_maker
sudo ./install.sh
```

特にエラーが出ていなさそうだったら、以下コマンドでラズパイを再起動する。

```
sudo reboot
```

## 動作確認する

起動後、タッチパネルモニターに操作画面が表示されるので、現在のカメラ画像が表示されることを確認する。

画面の`Start`ボタンを押す。画面に表示されている`status`が`idle`から`recording`に切り替わることを確認する。画面の`time`に録画時間が表示されることを確認する。ラズパイにログインし、`/home/pi/raspi_timelapse_maker/recorded/<日時>/`配下に録画データ(HLS形式)が保存されていることを確認する。

画面の`Finish`ボタンを押す。`/home/pi/raspi_timelapse_maker/recording/`配下に何もファイルがないことを確認する。録画プロセスが終了していることを確認するため、以下コマンドを実行して、何も出力されないことを確認する。

```
ps -aux | grep record | grep -v grep
```

画面の`Shutdown`ボタンを押す。ラズパイがシャットダウンすることを確認する。

## Sambaを設定する(任意)

録画したデータは、大抵の場合PC等に転送することになると思う。[Samba](https://www.samba.org/)をインストールしておくと、WindowsやmacOSからファイルサーバーとしてアクセスできるようになり、データのやり取りがしやすくなる。

自分の場合はWindows PCに動画データをコピーしたかったので、以下のサイトを参考に設定した。

<iframe class="hatenablogcard" style="width:100%;height:155px;margin:15px 0;max-width:680px;" title="Raspberry Pi にSambaを設定して共有フォルダ接続 - Toki Blog（トキブログ）" src="https://hatenablog.com/embed?url=https://toki-blog.com/pi-samba/" frameborder="0" scrolling="no"></iframe>

## [おまけ]MP4ファイルで出力するようにする

現状は、録画データをHLS形式で出力している。これは、あまり大きなサイズのファイルを扱いたくなかったという自分の事情があったためである。

ただ、MP4ファイルとして出力した方が使い勝手がいいパターンもあるだろう。HLS形式だと再生するのにもWebサーバーが必要になるため、再生確認がしずらい。

MP4ファイルで出力したい場合は、タイムラプス生成スクリプト一式(`/home/pi/raspi_timelapse_maker`)の中の`webroot/cgi-bin/record_worker.sh`内で`ffmpeg`を利用している箇所を以下のように修正する。

```
- ffmpeg -r 150 -i http://localhost:8080/?action=stream -f hls -vcodec h264_omx -vf fps=30 -b:v 3000k -hls_time 10 -hls_list_size 0 -hls_segment_filename $STREAM_PATH/%d.ts $STREAM_PATH/playlist.m3u8
+ ffmpeg -r 150 -i http://localhost:8080/?action=stream -vcodec h264_omx -vf fps=30 -b:v 3000k $STREAM_PATH/output.mp4
```

上記の修正を適用して録画をすると、`webroot/recorded/<日時>/`配下に`output.mp4`が生成されるようになる。

```
pi@raspberrypi:~/raspi_timelapse_maker/webroot $ ls recorded/20220320154823/
output.mp4
```

## [おまけ]Rasberry Pi Zero W + カメラ 固定用3Dモデル

ラズパイとカメラ固定用パーツのSTLデータは以下に置いている。

<iframe class="hatenablogcard" style="width:100%;height:155px;margin:15px 0;max-width:680px;" title="Raspi Timelapse Maker by kouya17 - Thingiverse" src="https://hatenablog.com/embed?url=https://www.thingiverse.com/thing:5324370" frameborder="0" scrolling="no"></iframe>

汎用性は全くない。