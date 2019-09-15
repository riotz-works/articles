---
title: デバッグに便利！ブラウザの HTTP 通信ログをアーカイブして共有・確認できる HTTP Archive File / Viewer の紹介
permalink: introduce-http-archive-file-and-viewer
date: 2019-09-15
author: lopburny
categories: フロントエンド
tags:
- Web
- HTTP
- Browser
- Cookie
---

ブラウザと API サーバーの繋ぎこみや画面遷移を伴う他サービスとの連携をする時、予期せぬエラーが起きたり想定外の挙動をしてデバッグに苦労したという経験をお持ちの方は多いのではないでしょうか。

![](/articles/assets/lopburny/img/lopburny_blog_12_thumbnail.jpg)

多くの Web アプリ開発で、API サーバーとの通信がどのように行われているかを把握することは不可欠です。上手く行かない場合、どのようなリクエストが送られたか、どのようなレスポンスが返されたかを確認するために、ブラウザのデベロッパーツール（開発者ツール）を利用したり、Fiddler などのツールを利用してデバックすることが多いと思います。

この記事では、こういった時に役立つ手法として、ブラウザのデベロッパーツールで HTTP 通信をキャプチャしてその内容をアーカイブとして保存する方法と、保存したアーカイブファイル（HARファイル）を閲覧するツールについて紹介します。

## HAR ファイルとは？

HTTP Archive (HAR) File と言います。通常、一つの Web ページを閲覧すると、そのページで使われる各種ファイル（CSS や JS、画像など）に加え、XHR (XMLHttpRequest）を利用した非同期通信に至るまで多くの HTTP 通信が行われます。HARファイルは、このような一連の通信内容のキャプチャをまとめてJSON 形式でエクスポートしたものになります。

開発者同士でこの HAR ファイルを共有することで、トラブルシューティングに役立てます。クライアント開発側とサーバー開発側の間で断片的なスニペットを送り合うより効率がよく、情報を網羅的に共有できるといったメリットがあります。

具体的には、デベロッパーツールの Network タブを開き、ページをロードしてみます。
※ Chrome の例です。
※ ページをロードする前に “Preserve log“ にチェックを入れておきます。

![](/articles/assets/lopburny/img/lopburny_blog_12_pic_1.jpg)

<br>

この状態で HAR ファイルとしてエクスポートすると、下記のような JSON 形式でに出力されます。

<br>

```json
{
  "log": {
    "version": "1.2",
    "creator": {
      "name": "WebInspector",
      "version": "537.36"
    },
    "pages": [
      {
        "startedDateTime": "2019-09-14T15:35:52.131Z",
        "id": "page_1",
        "title": "https://riotz.works/",
        "pageTimings": {
          "onContentLoad": 1157.8740000004473,
          "onLoad": 1449.5640000004641
        }
      }
    ],
    "entries": [
      {
        "startedDateTime": "2019-09-14T15:35:52.128Z",
        "time": 329.85700000062934,
        "request": {
          "method": "GET",
          "url": "https://riotz.works/",
          "httpVersion": "HTTP/1.1",
          "headers": [
          ...
```

## HAR ファイルを取得する方法

HAR ファイル出力は各ブラウザでサポートされており、取得の仕方について大きく異る点はありません。Chrome の場合、前述と同じくデベロッパーツールを開いて Network タブを選択、ログをクリアして “Preserve log“ にチェックを入れ、ページをロードします。

そうすると、ページを構成するファイルのダウンロードや通信が発生していることがわかります。どの項目でもいいので、右クリックして Save all as HAR with content すると保存できます。

![](/articles/assets/lopburny/img/lopburny_blog_12_pic_2.jpg)

## HAR ファイルの中身を確認するためのツール

先程作成した HAR ファイルは、JSON 形式となっているので普通にエディタで開いて内容を確認することもできます。しかしそのままでは分かりづらいので、ビューアーのツールを利用することになります。

### G Suite Toolbox - HAR Analyzer

G Suite Toolbox は本来 G Suite 関連のトラブルシューティングに使われるツールですが、ブラウザ上で利用でき、UI も使い勝手がよく便利なのでおすすめです。

![](/articles/assets/lopburny/img/lopburny_blog_12_pic_3.jpg)

🔗 [G Suite Toolbox - HAR Analyzer](https://toolbox.googleapps.com/apps/har_analyzer/)

### HTTP Archive Viewer (Chrome Extension)

こちらは Chrome の拡張機能として利用できるツールです。画面内で文字列検索（Ctrl/Cmd+F）ができないので、前述の HAR Analyzer の方を使いたくなるかもしれません。デベロッパツールの Network タブで見るのと同じような UI と感覚で操作できることが特徴です。

![](/articles/assets/lopburny/img/lopburny_blog_12_pic_4.jpg)

🔗 [HTTP Archive Viewer](https://chrome.google.com/webstore/detail/http-archive-viewer/ebbdbdmhegaoooipfnjikefdpeoaidml?hl=ja)

いかがだったでしょうか。

HAR ファイルはあまり新しい仕組みとは言えないですが、個人的には複数のチームで開発する現場で役に立ったことがあったので、紹介してみました。最後まで読んでいただきありがとうございました。
