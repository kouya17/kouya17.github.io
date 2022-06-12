---
title: "[C#]YouTubeLiveチャットに特定の文字列が書き込まれたことを検知する"
date: 2022-06-12T18:44:00+09:00
tags: ["youtube", "C#", "windows"]
cover:
  image: "media/covers/youtube-chat-hooks1/cover.jpg"
---

## 試作ソフト動作時の様子

APIキー及び対象配信のURLを入力し、チャットを取得する。取得したチャットに対し、特定文字列が含まれているものを抽出して表示する。

![動作時の様子](/media/gif/youtube-chat-hooks1/demo.gif)

上記GIF右側にあるソフトのコードは以下に置いている。

<iframe class="hatenablogcard" style="width:100%;height:155px;margin:15px 0;max-width:680px;" title="kouya17/YouTubeLiveChatHooks" src="https://hatenablog.com/embed?url=https://github.com/kouya17/YouTubeLiveChatHooks" frameborder="0" scrolling="no"></iframe>

## 実装内容の詳細

### YouTubeLiveチャットの取得

YouTubeLiveチャットは[YouTube Data API](https://developers.google.com/youtube/v3/getting-started?hl=ja)を利用して取得する。`YouTube Data API`は、YouTubeが公開している、YouTube上の情報を参照及び変更できるAPIである。[コードサンプルページ](https://developers.google.com/youtube/v3/code_samples)を見ると、以下の言語向けのクライアントライブラリがあることを確認できる。

- Go
- Java
- JavaScript
- .NET
- PHP
- Python
- Ruby

今回は`.NET`用のGoogleAPIクライアントライブラリを使う。

### APIキーの準備

`YouTube Data API`を利用するために、APIキーを作成する必要がある。APIキーの作成方法はここでは詳しく記載しない。以下のサイト等を参考にしてほしい。

<iframe class="hatenablogcard" style="width:100%;height:155px;margin:15px 0;max-width:680px;" title="【Youtube】APIキーの取得手順（2021/04/08時点のキャプチャ） - Qiita" src="https://hatenablog.com/embed?url=https://qiita.com/shinkai_/items/10a400c25de270cb02e4" frameborder="0" scrolling="no"></iframe>

### プロジェクトへのライブラリーの追加

プロジェクトに`Google.Apis.YouTube.v3`をインストールする。以下手順の説明は`Visual Studio 2022`の利用を想定している。

1. `Visual Studio 2022`のタブから プロジェクト > NuGetパッケージの管理 を選択する。

![NuGetパッケージの管理](/media/photos/youtube-chat-hooks1/nuget.png)

2. `Google.Apis.YouTube.v3`を検索し、最新バージョンをインストールする。

![Google.Apis.YouTube.v3のインストール](/media/photos/youtube-chat-hooks1/google_api_install.png)

### プログラムの実装

チャットを取得するコードを実装する。チャットを取得するまでの流れは以下のような形にしている。

1. 配信URLから`Video ID`を取得する。
2. `Video ID`から`Data API`を使って対象配信の`Chat ID`を取得する。
3. `Chat ID`から`Live Streaming API`を使ってチャットのデータを取得する。

#### 配信URLから`Video ID`を取得する

`Video ID`は配信URLのクエリパラメータ中の`v`パラメータに相当する。例えば、配信URLが`https://www.youtube.com/watch?v=fLM31tHrD5M`であれば**fLM31tHrD5M**が`Video ID`である。

`Video ID`抽出のための処理は`System.Web.HttpUtility.ParseQueryString()`を利用した。以下に実装のサンプルを記載する。なお、下記サンプルは`v`パラメータがない場合を想定していない。

```c#
var uri = new Uri(stringUrl);   // stringUrlには配信URLが入る
var query = System.Web.HttpUtility.ParseQueryString(uri.Query);
var videoId = query["v"];
```

### `Video ID`から`Data API`を使って対象配信の`Chat ID`を取得する

`Video ID`が取得できても、`Video ID`から直接チャットデータを取得することはできない。まずは`Chat ID`を取得する必要がある。

`Chat ID`の取得は[`video`リソースの`list`メソッド](https://developers.google.com/youtube/v3/docs/videos/list)を利用した。以下に実装のサンプルを記載する。

```c#
using Google.Apis.Services;
using Google.Apis.YouTube.v3;
using Google.Apis.YouTube.v3.Data;

var apiKey = "xxxxxxx"; // 取得したAPIキーを指定する
var youtubeService = new YouTubeService(new BaseClientService.Initializer()
{
    ApiKey = apiKey
});
var request = youtubeService.Videos.List("liveStreamingDetails");
request.Id = videoId;

var response = await request.ExecuteAsync();
var chatId = response.Items[0].LiveStreamingDetails.ActiveLiveChatId;
```

### `Chat ID`から`Live Streaming API`を使ってチャットのデータを取得する

チャットデータは`Live Streaming API`というライブ配信用のAPIを利用して取得する。こちらのAPIは`Data API`と比較して、公式から提供されている情報に日本語の情報が少ない。

チャットデータの取得は[`liveChatMessag`リソースの`list`メソッド](https://developers.google.com/youtube/v3/live/docs/liveChatMessages/list)を利用した。以下に実装のサンプルを記載する。

```c#
using Google.Apis.Services;
using Google.Apis.YouTube.v3;
using Google.Apis.YouTube.v3.Data;

// チャットのメッセージとnextPageTokenを扱うためのクラス
public class ChatResponse
{
    public IList<LiveChatMessage> liveChatMessages { get; set; }
    public string nextPageToken { get; set; }

    public ChatResponse(IList<LiveChatMessage> liveChatMessages, string nextPageToken)
    {
        this.liveChatMessages = liveChatMessages;
        this.nextPageToken = nextPageToken;
    }
}

// チャットのメッセージとnextPageTokenを取得する
public async Task<ChatResponse> getChat(YouTubeService youtubeService, string chatId, string pageToken = "")
{
    var request = youtubeService.LiveChatMessages.List(chatId, "snippet");
    if (pageToken != "")
    {
        request.PageToken = pageToken;
    }
    var response = await request.ExecuteAsync();
    return new ChatResponse(response.Items, response.NextPageToken);
}
```

チャットデータの取得は繰り返し行う必要があるため関数化(`getChat()`)して記載している。繰り返し呼び出す際、前回の呼び出し時の戻り値`ChatResponse.nextPageToken`を次回呼び出し時の引数`pageToken`に指定することで、更新分のデータを取得できる。

ここまででチャットデータの取得が出来るようになったので、後はチャットのメッセージに対し、特定文字列が入っているものを以下のように抽出できればよい。

```c#
var searchString = textBox.Text; // 検索文字列入力ボックスから検索文字列を取得する
if (searchString != "" && message.Contains(searchString)) {
    // 検索文字列がチャットのメッセージに含まれている時の処理
    ...
}
```

## 今後の拡張内容について

プロジェクト名を"YouTubeLiveChat**Hooks**"としているように、取得したチャットのメッセージに特定文字列が含まれていた場合、任意のWebhookに通知を行えるようにしたい。さらに言えば、Webサーバーをローカルでホストして、上記Webhook通知をトリガーとしてGIF等をブラウザ上で表示出来るようにしたい。