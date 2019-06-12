---
title: httpstat.us で、簡単 HTTP クライアントのテスト
permalink: httpstatus-makes-http-client-testing-easy
date: 2019-06-12
author: lulzneko
categories: 開発環境
tags:
---

Web API などを利用するコードを書くとき、異常系の動作確認はどのようにしていますでしょうか。
サービス側でテスト用のエンドポイントが用意されているとよいのですが常にあるとは限りませんし、想定していないステータスコードもあるかもしれません。何よりレスポンスが遅い場合の確認などは難しいでしょう。そんな時に便利なサービス httpstat.us を紹介します。

![](/articles/assets/lulzneko/develop/develop.jpg)


## httpstat.us とは
[httpstat.us](https://httpstat.us/) は、多様な HTTP Status Code をレスポンスしてくれるだけのシンプルなサービスです。

`https://httpstat.us/200` のように httpstat.us の URL に欲しいステータスコードの番号を付けてアクセスするだけで、そのステータスコードのレスポンスを返してくれます。https://httpstat.us/200 なら `200 OK` のステータスコード。https://httpstat.us/404 なら `404 Not Found`。
![](/articles/assets/lulzneko/develop/httpstatus/01-001.png)

３桁の数字であれば `001` や `999` のように、[Hypertext Transfer Protocol (HTTP) Status Code Registry](https://www.iana.org/assignments/http-status-codes/http-status-codes.xhtml) に定義されていないコードも画面表示こそ `Unknown Code` ですが、ステータスコードはしっかり返してくれます。

なお対応しているのは３桁のみで、２桁以下や４桁以上でリクエストするとナイスな**ティーポット**画像が楽しめます。(ステータスコード [418 I'm a teapot - HTTP | MDN](https://developer.mozilla.org/ja/docs/Web/HTTP/Status/418) のジョークにかけているのかな？https://httpstat.us/418 は `418 I'm a teapot` と、あっさりな実装だから)
![](/articles/assets/lulzneko/develop/httpstatus/01-002.png)

一応最後に "WE DON'T CAPTURE OR STORE ANY DATA ABOUT THE REQUESTS YOU MAKE." とメッセージされていますが、大事なデータは送らないようにしましょう。


## 遅延応答
クエリーパラメーター `sleep` で、ミリ秒単位の遅延応答をリクエストできます。プログラム側のタイムアウトを先に起こさせたい場合などに使えます。(遅延なしでもレスポンスに２秒かかってる)
```
$ SECONDS=0; curl https://httpstat.us/200; run_time=$SECONDS; echo -e "\n"$run_time
200 OK
2

$ SECONDS=0; curl https://httpstat.us/200?sleep=5000; run_time=$SECONDS; echo -e "\n"$run_time
200 OK
7
```


## JSON レスポンス
ヘッダーに `Accept: application/json` を付けると JSON でレスポンスを返してくれる、らしいのですが `"200 OK"` とダブルクォーテーションで囲ってくれるだけのようです。(`{ "status": 200, "message": "OK" }` などを期待したかった)
```console
$ curl https://httpstat.us/200
200 OK

$ curl -H 'Accept: application/json' https://httpstat.us/200
"200 OK"
```



----

簡単ながら HTTP Status Code をいろいろと試せるサービスの紹介でした。

実開発のテストに組み込むことはさすがにできないでしょうが、新しいライブラリの利用方法の確認だったり、エラーハンドリングの確認などを行う場合に便利です。

JSON レスポンスは期待値と異なるので [OSS-Friday 案件](/articles/2019/06/05/summary-of-oss-friday-activities-in-2019-05/) としてプルリクエストを出したいと思います。
