---
title: "ReactでYouTubeのプレーヤーを制御する"
date: 2022-01-15T13:15:00+09:00
tags: ["React", "youtube"]
cover:
  image: "media/covers/react-youtube/react_thumb.png"
---

[react-youtube](https://github.com/tjallingt/react-youtube)というReactコンポーネントを利用したサンプルサイトを作成したので、紹介する。

## 作成したサンプルサイト

<iframe class="hatenablogcard" style="width:100%;height:155px;margin:15px 0;max-width:680px;" title="https://youtube-iframe.kouya17.com" src="https://hatenablog.com/embed?url=https://youtube-iframe.kouya17.com/" frameborder="0" scrolling="no"></iframe>

ページを開くと、特定のYouTube動画が読みこまれる。YouTubeプレーヤーを操作する毎に操作情報(+操作時の再生位置)が記録される。記録された操作は、YouTubeプレーヤー下にテキストで表示される。操作情報は、「download」ボタンを押すことでファイルとして出力できる。

上記サンプルサイトでは、現状以下の操作のみ記録する。

- 再生準備完了(ready)
- 再生開始(play)
- 一時停止(pause)
- 再生終了(end)

## 視聴者毎の動画視聴状況を確認したいケースを考える

今回のサンプルサイトは、「視聴者ごとに、動画の視聴状況詳細を確認したいケース」を想定して作成した。視聴者は操作情報をファイルとして出力し、管理者へ提出する。管理者は提出されたファイルから、視聴状況を確認するという想定である。サンプルでは操作情報をファイルで出力しているが、これを「ユーザー情報を付与してサーバー上に保存」とかすれば、ファイルをやり取りするという手続きは不要になる。

今回想定したケースのように、YouTubeから情報を取得したい場合の手段としては、[YouTube Data API](https://developers.google.com/youtube/v3/getting-started?hl=ja)を活用するという方法がある。ただ、`YouTube Data API`には、「特定のユーザーの、各視聴動画に対する操作情報」という細かい情報までは用意されていない。取得できても「特定のユーザーの視聴履歴」までである。

少し調べたところ、YouTubeの[IFrame Player API](https://developers.google.com/youtube/iframe_api_reference)を使うと、YouTubeプレーヤーに対する操作を取得出来るようだったので、今回はそちらを利用した。

## YouTubeのIFrame Player APIを利用する

`IFrame Player API`を利用することで、再生中の動画に関する情報を取得できる。情報の取得はJavaScript関数を使って行う。また、特定のプレーヤーイベントに対して、イベントリスナーを設定できる。今回のサンプルサイトを例にすると、再生準備完了(onReady)、再生開始(onPlay)、一時停止(onPause)、再生終了(onEnd)に対してイベントリスナーを設定している。

今回のサンプルサイトは[Next.js](https://nextjs.org/)を使って作成したため、Reactコンポーネントとして`IFrame Player API`を利用できる[react-youtube](https://github.com/tjallingt/react-youtube)を利用した。

## サンプルサイトの作成とデプロイ

サンプルサイトの実装内容としては、`react-youtube`のサンプルに、プレーヤーイベント発生時のイベントリスナーを設定し、操作情報を画面に出力及びファイルとしてダウンロード出来るようにしたくらいである。

ソースコードは以下に公開している。

<iframe class="hatenablogcard" style="width:100%;height:155px;margin:15px 0;max-width:680px;" title="kouya17/youtube-iframe" src="https://hatenablog.com/embed?url=https://github.com/kouya17/youtube-iframe" frameborder="0" scrolling="no"></iframe>

サンプルサイトは`GitHub Pages`を使って稼働させている。`GitHub Pages`へのデプロイは、`GitHub Actions`を利用して、`main`ブランチ更新時に自動で行われるようにしている。ここら辺は、以下のサイトを参考にさせていただいた。

<iframe class="hatenablogcard" style="width:100%;height:155px;margin:15px 0;max-width:680px;" title="Next.js アプリを GitHub Actions でビルドして GitHub Pages で公開する｜まくろぐ" src="https://hatenablog.com/embed?url=https://maku.blog/p/au8ju6g/" frameborder="0" scrolling="no"></iframe>

<div class="kattene">
    <div class="kattene__imgpart"><a target="_blank" rel="noopener" href="https://www.amazon.co.jp/gp/product/4815601577/ref=as_li_tl?ie=UTF8&camp=247&creative=1211&creativeASIN=4815601577&linkCode=as2&tag=kouya17-22&linkId=c54ecf00a6641c6437a91860270e6b6e"><img src="https://ws-fe.amazon-adsystem.com/widgets/q?_encoding=UTF8&MarketPlace=JP&ASIN=4815601577&ServiceVersion=20070822&ID=AsinImage&WS=1&Format=_SL160_&tag=kouya17-22"></a></div>
    <div class="kattene__infopart">
      <div class="kattene__title"><a target="_blank" rel="noopener" href="https://www.amazon.co.jp/gp/product/4815601577/ref=as_li_tl?ie=UTF8&camp=247&creative=1211&creativeASIN=4815601577&linkCode=as2&tag=kouya17-22&linkId=c54ecf00a6641c6437a91860270e6b6e">確かな力が身につくJavaScript「超」入門 第2版</a></div>
      <div class="kattene__description">狩野 祐東</div>
      <div class="kattene__btns __four">
        <div><a class="kattene__btn __orange" target="_blank" rel="noopener" href="https://www.amazon.co.jp/gp/product/4815601577/ref=as_li_tl?ie=UTF8&camp=247&creative=1211&creativeASIN=4815601577&linkCode=as2&tag=kouya17-22&linkId=c54ecf00a6641c6437a91860270e6b6e">Amazon</a></div>
        <div><a class="kattene__btn __blue" target="_blank" rel="noopener" href="https://www.amazon.co.jp/gp/product/B07Y3FJ885/ref=as_li_tl?ie=UTF8&camp=247&creative=1211&creativeASIN=B07Y3FJ885&linkCode=as2&tag=kouya17-22&linkId=57296264873d846b93a0b2a640e3856f">Kindle</a></div>
        <div><a class="kattene__btn __red" target="_blank" rel="noopener" href="https://hb.afl.rakuten.co.jp/ichiba/1585b2d3.e3af76f2.1585b2d4.494d3f80/?pc=https%3A%2F%2Fitem.rakuten.co.jp%2Fbook%2F16014712%2F&link_type=hybrid_url&ut=eyJwYWdlIjoiaXRlbSIsInR5cGUiOiJoeWJyaWRfdXJsIiwic2l6ZSI6IjI0MHgyNDAiLCJuYW0iOjEsIm5hbXAiOiJyaWdodCIsImNvbSI6MSwiY29tcCI6ImxlZnQiLCJwcmljZSI6MSwiYm9yIjoxLCJjb2wiOjAsImJidG4iOjEsInByb2QiOjB9">楽天</a></div>
        <div><a class="kattene__btn __green" target="_blank" rel="noopener" href="https://hb.afl.rakuten.co.jp/ichiba/1592b466.7f5ea7c8.1592b467.70471b78/?pc=https%3A%2F%2Fitem.rakuten.co.jp%2Frakutenkobo-ebooks%2F4996251ea89337fe8e18836b84946b16%2F&link_type=hybrid_url&ut=eyJwYWdlIjoiaXRlbSIsInR5cGUiOiJoeWJyaWRfdXJsIiwic2l6ZSI6IjI0MHgyNDAiLCJuYW0iOjEsIm5hbXAiOiJyaWdodCIsImNvbSI6MSwiY29tcCI6ImxlZnQiLCJwcmljZSI6MSwiYm9yIjoxLCJjb2wiOjAsImJidG4iOjEsInByb2QiOjB9">楽天Kobo</a></div>
      </div>
    </div>
</div>
