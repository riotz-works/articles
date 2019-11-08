---
title: Web API のモックサーバーとしても活躍、jsonbox.io
permalink: ideal-as-a-mock-server-of-webapi-jsonboxio
date: 2019-10-07
author: lulzneko
categories: 開発
tags:
- JAMstack
---


jsonbox.io という HTTP ベースで JSON ストレージを扱えるフリーのサービスがあります。
公式ではスモールプロジェクトや、プロトタイピング、ハッカソンなどで最適とありますが、もうひとつ JAMstack 開発時の Web API のモックとしても活用できるのでご紹介。

![](/articles/assets/lulzneko/develop/develop.jpg)


JAMstack でフロント開発をしている際は、フロント担当と Web API 担当に分かれて開発をします。もちろん双方を同時に担当してもよいのですが、多くのケースでは担当が分かれることでしょう。

そうなるとフロントエンドから呼び出したい Web API はフロントエンド側でモック実装するなり、モックサーバーを用意するなどの必要があります。モック実装すると本質的でないコードをソース管理することになりますし、モックサーバーを用意するには手間が発生します。

そんな時に [jsonbox.io](https://jsonbox.io/) を使うと、手軽な Web API モックサーバーとして利用することもできます。


## jsonbox.io の利用方法
まず jsonbox.io のトップページ、[https://jsonbox.io](https://jsonbox.io/) へブラウザでアクセスします。
するとトップページに `https://jsonbox.io/box_b68kwaff800864XXXX` のような URL が表示されます。これはリロードするたびに毎回変わります。これが JSON ストレージの URL になりますので控えておきます。
![](/articles/assets/lulzneko/develop/jsonboxio/01-001.png)

この `box_b68kwaff800864XXXX` の文字列は `BOX_ID` と呼ばれ、JSON ストレージの ID です。jsonbox.io のトップページで生成されたものを使うほか、20文字以上の英数字とアンダーバー(`_`)を使って自分で生成することもできます。事前に設定する必要はなくレコードを作るときに指定したものが、そのまま使われます。

ここからは [jsonbox/README.md](https://github.com/vasanthv/jsonbox/blob/master/README.md) をベースに紹介します。


### レコードの作成
HTTP POST で作成したいレコードの JSON をリクエストします。
```console
curl -X POST 'https://jsonbox.io/demobox_6d9e326c183fde7b' \
    -H 'content-type: application/json' \
    -d '{"name": "Jon Snow", "age": 25}'
```

以下のようなレスポンスが返り `_id` と `_createdOn` が自動生成されていることがわかります。
```json
{"_id":"5d776a25fd6d3d6cb1d45c51","name":"Jon Snow","age":25,"_createdOn":"2019-09-10T09:17:25.607Z"}
```

複数のレコード同時に作成するには `-d` で渡すデータを配列にします。
```console
curl -X POST 'https://jsonbox.io/demobox_6d9e326c183fde7b' \
    -H 'content-type: application/json' \
    -d '[{"name": "Daenerys Targaryen", "age": 25}, {"name": "Arya Stark", "age": 16}]'
```

また URL を `jsonbox.io/${BOX_ID}/${COLLECTION}` のようにしてグループレコードも作れます。
```console
curl -X POST 'https://jsonbox.io/demobox_6d9e326c183fde7b/users' \
    -H 'content-type: application/json' \
    -d '[{"name": "Daenerys Targaryen", "age": 25}, {"name": "Arya Stark", "age": 16}]'
```


### レコードの取得
HTTP GET リクエストでレコードが取得できます。pathname によって返る範囲が変わります。

`BOX_ID` までの場合、グループレコードを含めた、全レコードが返ります。
```console
curl -X GET 'https://jsonbox.io/demobox_6d9e326c183fde7b'
```

`BOX_ID` に加え `COLLECTION` も指定することで、その `COLLECTION` に含まれるグループレコードが返ります。
```console
curl -X GET 'https://jsonbox.io/demobox_6d9e326c183fde7b/users'
```

`BOX_ID` に加え `RECORD_ID`、レコードを作成したとき or リスト取得した際に返ってきた `_id` の値を指定すると、その ID の１レコードが返ります。
```console
curl -X GET 'https://jsonbox.io/demobox_6d9e326c183fde7b/5d776a25fd6d3d6cb1d45c51'
```

またソートやリミット、クエリーなど柔軟な取得方法も備えています。今回は JSON モックサーバーとしての用途を想定していますので簡単な CRUD 処理ができれば十分ですが、アプリなどで実際に使う場合には必要でしょう。詳しくは [Read - jsonbox/README.md](https://github.com/vasanthv/jsonbox/blob/master/README.md#read) をご参照ください。


### レコードの更新
HTTP PUT リクエストを `RECORD_ID` まで指定した URL でレコードを更新します。
PATCH と、バルク更新はサポートされていません。
```console
curl -X PUT 'https://jsonbox.io/demobox_6d9e326c183fde7b/5d776b75fd6d3d6cb1d45c53' \
    -H 'content-type: application/json' \
    -d '{"name": "Arya Stark", "age": 18}'
```


### レコードの削除
HTTP DELETE リクエストを `RECORD_ID` まで指定した URL でレコードを更新します。
またクエリーを使用して複数のレコード削除も可能です。
```console
curl -X DELETE 'https://jsonbox.io/demobox_6d9e326c183fde7b/5d776b75fd6d3d6cb1d45c53'
```


## 制約
フリーで利用できますが、不正利用や乱用を防ぐ(= avoid abuse)ために以下の制限を課しているとのことです。
- リクエストのボディは 10KB まで
- 一度に扱えるレコードは 1,000件まで
- レコード数は 5,000件までを目安に

３つ目のレコード数ほか、高負荷をかけないなどマナーを守りたいですね。これだけの素晴らしいサービスを無償で提供してくださっており、また "小規模向けなのでフリーとしています" とのことです。

原文は以下。
> There is no limit on the number of records you store in a box, but please don't abuse the API by storing large datasets of more than 5000 records. This is meant for small projects and that's why it is offered FREE of cost. - [Limitations](https://github.com/vasanthv/jsonbox/blob/master/README.md#limitations)



----

単純な CRUD 処理は REST の Web API として簡単に操作できます。
グループレコードの機能があるので、単純な JSON モックサーバーとしては十分でしょう。

またクエリーや `skip` と `limit` を使ったページネーション等の高度な機能もあるので、公式で謳っている通りスモールプロジェクトや、プロトタイピング、ハッカソンでも十分な能力を発揮してくれそうです。
