---
title: "NT金沢2023に出展した"
date: 2023-08-11T09:50:00+09:00
tags: ["diary", "NT"]
cover:
  image: "media/covers/nt_kanazawa_2023/tenji.png"
---

少し前の話になってしまうが、2023年6月17日～2023年6月18日の間、[NT金沢](https://wiki.nicotech.jp/nico_tech/?NT%E9%87%91%E6%B2%A22023)というイベントに出展してきた。NT金沢は以前、[リモートという形で出展したことはある](https://kouya17.com/posts/43/)が、現地参加は初めてだった。

## 現地到着まで

1日目の開始時刻は10:00からのため、設営等考えて9:00に現地に到着できるように検討した。特に今回は荷物もそこまで多くなかったのに加え、会場が駅の近くだった(大変ありがたい)ため、前乗りはしなかった。

出展物は以下のスーツケースに詰め込んだ。

<iframe class="hatenablogcard" style="width:100%;height:155px;margin:15px 0;max-width:680px;" title="Amazon | [トラベルハウス] ソフトスーツケース キャリーバッグ 撥水加工 S型機内持込 大容量 超軽量 Tsaロック 容量拡張（１年 | スーツケース" src="https://hatenablog.com/embed?url=https://www.amazon.co.jp/gp/product/B084H8QNF2/ref=ppx_yo_dt_b_d_asin_title_o00?ie=UTF8&psc=1&tag=kouya17-22" frameborder="0" scrolling="no"></iframe>

このスーツケースは以下の折り畳みテーブルを持ち運べるように購入したもの。

<iframe class="hatenablogcard" style="width:100%;height:155px;margin:15px 0;max-width:680px;" title="Amazon | REDCAMP アウトドア テーブル 折りたたみ キャップ ロー ハイ2段高さ調整 持ち運び 軽量 コンパクト ピクニック ビーチ 屋外 | ガーデンテーブル" src="https://hatenablog.com/embed?url=https://www.amazon.co.jp/gp/product/B08BZ429G4/ref=ppx_yo_dt_b_search_asin_title?ie=UTF8&psc=1&tag=kouya17-22" frameborder="0" scrolling="no"></iframe>

これらのものはほぼイベント出展時にしか使っていない。現状自家用車などの移動手段を持っていないため、出展物のサイズ・荷物も上記のスーツケースに収まるように構成を決めている。

## 出展内容

出展物としては、以下のような射的ゲームを出展した。

<blockquote class="twitter-tweet"><p lang="ja" dir="ltr"><a href="https://twitter.com/hashtag/NT%E9%87%91%E6%B2%A2?src=hash&amp;ref_src=twsrc%5Etfw">#NT金沢</a> に持っていく射的ゲームについて、ようやく初めて全体動作の確認をしました。 <a href="https://t.co/CTfRceYFtq">pic.twitter.com/CTfRceYFtq</a></p>&mdash; 青木晃也 (@aoki_kouya) <a href="https://twitter.com/aoki_kouya/status/1668960003133411329?ref_src=twsrc%5Etfw">June 14, 2023</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

9つの的があり、ビンゴ風の射的ゲームになっている。ビンゴが揃うと制限時間が伸びてハイスコアを目指せる仕組み。現状の設定上は、うまくいけば永遠にゲームを続けられるようになっている。

展示時の様子は以下の写真の通り。

<blockquote class="twitter-tweet"><p lang="ja" dir="ltr"><a href="https://twitter.com/hashtag/NT%E9%87%91%E6%B2%A2?src=hash&amp;ref_src=twsrc%5Etfw">#NT金沢</a> 設営完了しました。赤外線を使った射的ゲームを展示しています。 <a href="https://t.co/qLR2GTVWf7">pic.twitter.com/qLR2GTVWf7</a></p>&mdash; 青木晃也 (@aoki_kouya) <a href="https://twitter.com/aoki_kouya/status/1669870266531602432?ref_src=twsrc%5Etfw">June 17, 2023</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

今回展示した作品の構成は以下のようになっている。

![](/media/photos/nt_kanazawa_2023/bingo_st.png)

ラズパイ上でスコアを管理する形は、[Maker Faire Tokyo2022に出展したとき](https://kouya17.com/posts/mft2022/)の構成を流用している。ラズパイ上で動作しているソフトもほぼそのまま流用している。

今回は基板を機能単位で分割して作成した。赤外線送信用基板・赤外線受信用基板・振動用基板などを作成し、それぞれを結合して利用している。以下はパスコンパスさんに各基板の動作について紹介させていただいたときのもの。

<blockquote class="twitter-tweet"><p lang="ja" dir="ltr">モジュール化されたカラフルな基板がかわいい<br>赤外線射的で色々ソレノイドとかブザーとか繋いで拡張ができるの楽しい<a href="https://twitter.com/hashtag/NT%E9%87%91%E6%B2%A2?src=hash&amp;ref_src=twsrc%5Etfw">#NT金沢</a> <a href="https://t.co/7KrWBRRMpT">pic.twitter.com/7KrWBRRMpT</a></p>&mdash; パスコンパス (@pscmps) <a href="https://twitter.com/pscmps/status/1669911203332919302?ref_src=twsrc%5Etfw">June 17, 2023</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

これまでの作品と比較して、今回は「やりこみたくなるもの」を目指した。これまではとりあえず最低限の機能を成り立たせるのが精一杯で、ゲーム性についてはあまり考えられていなかった。今回は「工夫すれば高得点が狙える」ようなゲームルールを設けてハイスコアをとることで達成感を得られるような構成を目指した。(ここらへん書いていて"ゲーム性"についてより調べてみる必要がありそうな気がしてきた…。)ただ、ルール以前に赤外線の受光感度が厳しすぎて難易度が高くなりすぎた印象がある。

展示中、おおむね大きなトラブルはなかった。ただ、トリプルビンゴ後に挙動がおかしくなるバグが見つかった。これはトリプルビンゴの考慮が漏れていたため。

## 食べたもの

金沢駅にあったチャンピオンカレーと8番ラーメンあたりを食べた。写真は特にとっていない。

チャンピオンカレーは名前しか知らない状態だったのだが、思っていたより味が濃かった。おいしかったが、塩分量が少し気になってしまった。

8番らーめんは味噌味を食べた。8番らーめんは小さいときによく食べていたのだが、久しぶりに食べたらなんとなくコレジャナイ感がした。小さいときは醤油味や塩味をよく食べていた気がするので、そのせいかもしれない。

## 泊まったホテル

宿は出展申請した4月の時点で探し、[ビジネスホテル　ナカダ](https://travel.rakuten.co.jp/HOTEL/32379/32379.html?cid=tr_af_1632)というところにした。

金沢駅から徒歩5分ほどで、シングル1泊が4200円と駅近にしては比較的安そうだったのでここにした。なお、この時は全国旅行支援クーポンが利用できたため、840円引きで3360円(+地域クーポン1000円)だった。

室内は狭めだが、寝るためだけの利用だったので十分だった。
