---
title: Serverless Framework と TypeScript で Hello と Version の Web API を 実装
permalink: ServerlessFrameworkとTypeScriptでHelloとVersionのWebAPIを実装
date: 2018-02-03
author: lulzneko
categories: サーバーレス
tags:
- AWS Lambda
- Serverless Framework
- TypeScript
---

![](/articles/assets/lulzneko/serverless/serverless.jpg "サーバーレス")

前回サーバーレスなアプリケーション の ひな形を作りました。
今回は、ひな形で作られた [AWS Lambda](https://aws.amazon.com/jp/lambda/) の ソースを Hello World にし、また新しくアプリケーションのバージョンを返す Version Web API を 追加します。

### シリーズの記事
- [Serverless Framework と TypeScript で サーバレス開発事始め](/articles/2018/02/01/ServerlessFrameworkとTypeScriptでサーバレス開発事始め/)
- **Serverless Framework と TypeScript で Hello と Version の Web API を 実装** (本記事)

### 環境
開発者の環境は以下となります。
- Windows 10 64bit
- Visual Studio Code
- Node.js 9
- TypeScript 2.7
- Serverless Framework 1.26.0

主な実行環境について、また全部稼働まではいきませんが以下を想定しています。
※ Amazon API Gateway などは、Serverless Framework が 自動設定するので省略、明示的に使うものとしてリストになります。
- Amazon S3
- AWS Lambda


## ベースとなるソースコード
前回の続きとなりますので、こちら [https://github.com/riotz-works/samples-hello-serverless/tree/0.0.1](https://github.com/riotz-works/samples-hello-serverless/tree/0.0.1) を もとに追加開発します。

本記事と一緒に作成する場合は 前回のソース もしくは タグを指定してチェックアウトしたソース を お使いください。
```console
c:\Temp\nlog>git clone --depth 1 -b 0.0.1 https://github.com/riotz-works/samples-hello-serverless.git
Cloning into 'samples-hello-serverless'...
(省略)
```


## Hello Web API の 改修
[Serverless Framework](https://serverless.com/) の ボイラープレート で 生成した Lambda の プログラム は `handler.js` で 以下のようになっています。
これを Hello World の Web API へ 改修するにあたり、このままで `response` の `body` を 変えるだけでよさそうです。今回は `message` を `'Hello Serverless !!'` にし、`input` は 削除することにします。
```ts
import { APIGatewayEvent, Callback, Context, Handler } from 'aws-lambda';

export const hello: Handler = (event: APIGatewayEvent, context: Context, cb: Callback) => {
  const response = {
    statusCode: 200,
    body: JSON.stringify({
      message: 'Go Serverless Webpack (Typescript) v1.0! Your function executed successfully!',
      input: event,
    }),
  };

  cb(null, response);
}
```

そしてプロジェクトのディレクトリ直下で汎用的な名前の `handler.js` だと、このあと追加する Version Web API の 配置などで困ります。
まずはディレクトリ配置を見直し、Lambda の ハンドラ・ファンクションのソースが入っていることがわかるように `src/handler` 配下に置くことにします。
続いて Hello Web API の ハンドラに対応したファンクションのファイル名として `greetings.ts` とすることにします。
![](/articles/assets/lulzneko/serverless/hello/21.png)

ファイル名と配置を変えたので Lambda の 定義をしている `serverless.yml` を 修正します。
`hello` ファンクション の ハンドラ は `handler.hello` と 定義されていましたが、`greetings` ファクション `src/handler/greetings.hello` ハンドラ に 変更します。
![](/articles/assets/lulzneko/serverless/hello/22.png)

Hello Web API が できました。
この時点で `serverless deploy` コマンドでデプロイすることもできます。(--aws-profile [profilename] を 忘れずに)


## Version Web API の 作成
Hello Web API が 作れましたがボイラープレートのままだったので、新しく Web API を 追加したいと思います。
とりあえず、アプリケーションのバージョンを返す Version Web API を 作ることにします。
仕様は「`HTTP GET /version` で `package.json` の `version` を 返す」にします。

まずは、Lambda の ハンドラのソースを配置する `src/handler` に `systems.ts` を 作ります。
他にシステム関連の Web API の 想定はありませんが、システム関連 Web API の ハンドラを配置する `systems.ts` に `version` の ハンドラを追加するイメージにしました。

コードは以下のようにします。(このコードを使う場合は、１行目の Copyright は 書き換えてください)
```ts
/* Copyright 2018 Riotz Works. */
import { APIGatewayEvent, Callback, Context, Handler } from 'aws-lambda';
import { version } from '../../package.json';

/**
 * System Web API's AWS Lambda handler function.
 *
 * @param event – event data.
 * @param context – runtime information of the Lambda function that is executing.
 * @param callback – optional callback to return information to the caller, otherwise return value is null.
 * @see http://docs.aws.amazon.com/lambda/latest/dg/nodejs-prog-model-handler.html
 */
export const handle: Handler = (event: APIGatewayEvent, context: Context, cb?: Callback): void => {

  const response = {
    'body': JSON.stringify({
      'version': version
    }),
    'statusCode': 200
  };

  if (cb !== undefined) {
    cb(null, response);  // tslint:disable-line
  } else {
    context.succeed(response);
  }
};
```

主なキー・ポイントは以下になります。 先の Hello Web API と 若干異なる部分 (`context.succeed()` とか) が ありますが詳細は後日の記事として、基本的には変わりありません。
- ３行目で、バージョンのもとになる `package.json` を `systems.ts` の 相対パスで指定し `version` 要素をインポートします
- 17行目で、インポートした `version` 属性の値 を レスポンス・ボディとしてセットします

Visual Studio Code の 環境によってはエラーが出ないかもしれませんが、３行目 の インポート文でエラーにあります。
![](/articles/assets/lulzneko/serverless/hello/23.png)

何も表示されていなくても `serverless package` を 実行するとエラーが出力されます。
```console
C:\Temp\samples-hello-serverless> serverless package
Serverless: Bundling with Webpack...
(省略)
ERROR in C:\Develop\repos\riotz\samples-hello-serverless\src\handler\systems.ts
(3,25): error TS2307: Cannot find module '../../package.json'.
```

これは `package.json` の モジュール定義が存在しないためになります。
このように型定義などを確認してくれるのが TypeScript のありがたいところです。
一方で今回のは若干無茶な実装でした。標準モジュールには存在しませんし、適切なモジュール定義をとってくることも難しいでしょう。さりとて `package.json` と 何か別ファイルで `version` の ダブルメンテはしたくないです。。。 ということで、モジュール定義を追加することにします。
ソースディレクトリ の `src` に `types.d.ts` を 追加します。`.d.ts` ファイルでしたら特に命名規則はなく `typing.d.ts` も よく見かけます。

コードは以下のようにします。(１行目の Copyright は 書き換えてください)
```ts
/* Copyright 2018 Riotz Works. */

/**
 * Module declaration for JSON file.
 */
declare module '*.json' {

  /** version element of package.json using Version API. */
  const version: string | undefined;
}
```

`*.json` なので、モジュール定義が見つからなかった JSON ファイル の 型定義になります。
その中に `version` が 文字列として存在することを定義しますが、これは 全ての JSON ファイルに当てはまることではないので、型定義は `string | undefined` とし ドキュメントで注釈しました。
頑張ると `package.json` 固有のモジュール定義もできたのですが `version` を取得するだけにしては複雑になりすぎたので、このあたりを落としどころとしました。
![](/articles/assets/lulzneko/serverless/hello/24.png)

最後に `serverless.yml` に Version Web API の Lambda 定義を追加します。
`functions: greetings:` の 下に追加します。
```yml
  systems:
    handler: src/handler/systems.handle
    events:
      - http:
          method: get
          path: version
```


Version Web API が できました。
![](/articles/assets/lulzneko/serverless/hello/25.png)


## デプロイ
AWS 環境へデプロイします。前回の [デプロイ と 稼働テスト](/articles/2018/02/01/ServerlessFrameworkとTypeScriptでサーバレス開発事始め/#%E3%83%87%E3%83%97%E3%83%AD%E3%82%A4-%E3%81%A8-%E7%A8%BC%E5%83%8D%E3%83%86%E3%82%B9%E3%83%88) 同様に、AWS の プロファイを参照して `serverless deploy --aws-profile [profilename]` を 実行します。
![](/articles/assets/lulzneko/serverless/hello/26.png)

`endpoints` に `hello` と `version` の URL が 出力されるので、それぞれブラウザでアクセスすると、プログラムした JSON が 返ってきます。


## ソースコード
今回作成した部分までのソース を GitHub へ アップしました。
[https://github.com/riotz-works/samples-hello-serverless/tree/0.0.2](https://github.com/riotz-works/samples-hello-serverless/tree/0.0.2)



----
Hello Web API は ボイラープレート の レスポンスを改修しただけだったので特に大きな変化はありませんでした。一方で Version Web API は 新しく追加となりましたし、両方のプログラムを配置するためにプロジェクトの構造も変化させました。

Hello と Version で 実装レベルが異なっている部分はありますが、これについては次の記事で確認しながら合わせこみをしていきたいと思います。できれば TypeScript の コンパイラ設定 や Lint にまでふれられたらと思います。
