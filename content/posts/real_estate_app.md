---
title: "PostgreSQL+Echo+cron+Next.jsでWebスクレイピングシステムを構成する"
date: 2023-01-26T20:02:00+09:00
tags: ["web", "golang", "TypeScript"]
cover:
  image: "media/covers/real_estate_app/components.jpg"
---

## 今回作ったシステムのデモ

以下が今回作ったシステムの動作時の動画になる。

![](/media/photos/real_estate_app/regist.gif)

上の動画のように、情報を取得したい物件一覧が表示されるURL及びジョブを識別するためのタグを登録する。登録されたジョブはキューに保存される。上の動画では分からないが、ジョブは毎分1件ずつ実行される。

ジョブが実行され、システムに保存された情報は以下のような一覧画面から確認できる。

![](/media/photos/real_estate_app/list.png)

## システムの全体構成図

本システムの全体構成は以下のようになっている。

![](/media/photos/real_estate_app/scsave.jpg)

以下4つの要素に分けてそれぞれDockerコンテナ化している。

- Next.jsコンテナ
- Echoコンテナ
- cronコンテナ
- PostgreSQLコンテナ

## Webスクレイピングの流れ

本システムにおけるWebスクレイピングの流れは以下のとおりである。

### ジョブを登録する

データの取得先URLを登録する。このURLは、不動産情報サイトの検索結果ページを指定する。今は数種類のサイトに対応している。また、タグを指定することでそのジョブに名前のようなものをつけることができる。

![](/media/photos/real_estate_app/jobs.png)

過去に実行したものと同じ条件であれば再実行ボタンからも実行できる。

### ジョブが実行される

登録されたジョブは1個ずつ順番に実行される。実行が開始されると、ジョブ一覧画面で対象ジョブの`Status`が`running`に変化する。

ジョブは登録されたURLによって検索表示される不動産情報を1個ずつ、ある程度の時間間隔を空けながら取得する。すべての不動産情報が取得出来たら地価情報サイトから対象物件付近の地価(1平米あたり)を取得する。この情報も`AreaPrice`として物件情報に含める。この地価情報と土地面積及び物件価格から建物の想定価格を計算し、`EstimatedBuildingPrice`として物件情報に含める。なお、地価は頻繁に変わる情報ではないのでデータベースに保存しておき再利用する。

実行が終了するとジョブ一覧画面で対象ジョブの`Status`が`completed`に変化する。

### 物件情報一覧画面から取得した物件情報を確認する

物件一覧ページにおいて、最新ジョブで取得された行は赤枠で表示される。検索欄で`JobId`を範囲指定することもできるので、複数ジョブを実行した場合はそれらのジョブによって取得されたものを絞り込むこともできる。

![](/media/photos/real_estate_app/list2.png)

なお、すでに登録されている物件URLと同じURLを持つ物件が取得された場合は、価格を比較する。価格が更新されていた場合は物件情報と`JobId`を上書きする。

物件一覧において、行をクリックすると対象物件ページに遷移する。対象物件をクリックした回数も保持しており、1回以上クリックした物件は黄色で塗りつぶされて表示される。そのため、過去自分が詳細をチェックした物件かどうか一目で見分けがつくようになっている。

## ソースコード

<iframe class="hatenablogcard" style="width:100%;height:155px;margin:15px 0;max-width:680px;" title="kouya17/scsave" src="https://hatenablog.com/embed?url=https://github.com/kouya17/scsave" frameborder="0" scrolling="no"></iframe>