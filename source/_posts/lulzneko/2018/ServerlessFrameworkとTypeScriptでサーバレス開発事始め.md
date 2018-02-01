---
title: Serverless Framework と TypeScript で サーバレス開発事始め
permalink: ServerlessFrameworkとTypeScriptでサーバレス開発事始め
date: 2018-02-01
author: lulzneko
categories: サーバーレス
tags:
- AWS Lambda
---

![](/articles/assets/lulzneko/serverless/serverless.jpg "サーバーレス")

[前回の記事](/articles/2017/12/07/CSVファイルのバッチ連携もAWS-Lambdaでサーバーレス/)から時間が空いてしまいましたが、ちょうど新しいプロダクトの開発を始めるところなので [Serverless Framework](https://serverless.com/) と [TypeScript](https://www.typescriptlang.org/) で 開発する際の事始めについてまとめます。
例によって Hello World になりますが、こんな感じで作り始めますというところを お伝えできればと思います。

## 環境
開発者の環境は以下となります。
- Windows 10 64bit
- Visual Studio Code
- Node.js 9
- TypeScript 2.7
- Serverless Framework 1.26.0

主な実行環境について、今回は準備なので全部稼働まではいきませんが以下を想定しています。
※ Amazon API Gateway などは、Serverless Framework が 自動設定するので省略、明示的に使うものとしてリストになります。
- Amazon S3
- AWS Lambda


## 想定アプリケーション
ブラウザでアクセスするウェブサイトで、アクセス時に JavaScript で Web API を 呼び出し "Hello World" の 挨拶を取得します。 まずは動作させるところを目標にシンプルな形で進めます。

ウェブサイト は [Amazon S3](https://aws.amazon.com/jp/s3/) に HTML を 配置し 静的ウェブサイトのホスティングを使います。
本格稼働するには [Amazon CloudFront](https://aws.amazon.com/jp/cloudfront/) や [Amazon Route 53](https://aws.amazon.com/jp/route53/) などが必要となりますが、まずは稼働を目指し S3 だけで進めます。

Web API は サーバーレスで作りたいので [AWS Lambda](https://aws.amazon.com/jp/lambda/) を 使います。
インスタンス や コンテナ の 管理が不要で、純粋にアプリケーションの開発やメンテに集中できるのサーバーレスにこだわりたいです。
こちらも本格稼働にあたっては [Amazon API Gateway](https://aws.amazon.com/jp/api-gateway/) に カスタム・ドメインを割り当てたりしますが、まずは稼働を目指し自動割り当てで進めます。


## 開発環境の構築
コーディングにためのエディタを用意します。こちらは手慣れたもので大丈夫です。今回は Visual Studio Code を 使いました。
こちら [https://code.visualstudio.com](https://code.visualstudio.com/) から取得できます。
![](/articles/assets/lulzneko/serverless/hello/01.png)

続いて Node.js を インストールします。こちら [https://nodejs.org](https://nodejs.org/) から ダウンロードしてインストールします。今回は 9.5.0 Current を インストールしました。
![](/articles/assets/lulzneko/serverless/hello/02.png)


## プロジェクト の 作成
Node.js が インストールできたら、プロジェクトのディレクトリを作成します。
今回は `C:\Temp\hello-serverless` を プロジェクトのディレクトリとしました。
![](/articles/assets/lulzneko/serverless/hello/03.png)

続いて Serverless Framework を 導入します。今回は Visual Studio Code の ターミナルを使っていますが、コマンド プロンプト からも同様のコマンドになります。
今回はプロジェクトごとに Serverless Framework を 導入したいので、`-g` なしで `npm install serverless` としました。
```console
C:\Temp\hello-serverless> npm install serverless
(省略)
+ serverless@1.26.0
added 302 packages in 44.148s
```

Serverless の モジュール が 導入され、`node_modules` と `package-lock.json` が 作られています。
![](/articles/assets/lulzneko/serverless/hello/04.png)

Serverless の AWS/TypeScript ボイラープレート から プロジェクト の ひな形を作ります。
※ 今回はプロジェクト・ローカル に Serverless Framework を 導入しているため `node_modules\.bin\serverless` コマンドになります。`node_modules\.bin` に パスを通しておくか、めんどくさい場合は `npm install -g serverless` と `-g` を つけてグローバルに入れてしまうのも手です。
```console
C:\Temp\hello-serverless> node_modules\.bin\serverless create --template aws-nodejs-typescript
(省略)
Serverless: Successfully generated boilerplate for template: "aws-nodejs-typescript"
Serverless: NOTE: Please update the "service" property in serverless.yml with your service name
```

Node.js/npm プロジェクト関連のファイルが追加されます。
![](/articles/assets/lulzneko/serverless/hello/05.png)

`package.json` の `devDependencies` に Serverless Framework が 入っていないので、もう一度 `--save-dev` を 付けて `npm install --save-dev serverless` を 実行します。
※ 最初から `--save-dev` しないのは `package.json` が 作られているとボイラープレートが展開できないためです。
![](/articles/assets/lulzneko/serverless/hello/06.png)

他のモジュールもインストールするため、`npm install` を 実行します。
![](/articles/assets/lulzneko/serverless/hello/07.png)


## プロジェクト の 設定
自動生成された設定が入っているので、プロジェクトに合わせた設定を行います。

`package.json` は、最小限 `name` `description` `author` あたりを変更しておきます。
また、作成しているのはアプリケーションなのでモジュール公開の防止のために `"private": true` を 設定しておくとよいでしょう。`private` の 詳細は こちら [package.json | npm Documentation](https://docs.npmjs.com/files/package.json#private) を ご確認ください。
![](/articles/assets/lulzneko/serverless/hello/08.png)

`serverless.yml` は、`service: name:` の 値を変更しておきます。
![](/articles/assets/lulzneko/serverless/hello/09.png)

`tsconfig.json` の `lib` に `esnext` を 追加します。
2018年2月現在、これを追加しておかないとビルド時に参照しているモジュールで以下のようなエラーが発生するためです。
```console
C:\Temp\hello-serverless> node_modules\.bin\serverless package

ERROR in C:\Temp\hello-serverless\node_modules\@types\graphql\subscription\subscribe.d.ts
(17,4): error TS2304: Cannot find name 'AsyncIterator'.

ERROR in C:\Temp\hello-serverless\node_modules\@types\graphql\subscription\subscribe.d.ts
(29,4): error TS2304: Cannot find name 'AsyncIterable'.
```
![](/articles/assets/lulzneko/serverless/hello/10.png)


## プロジェクト の ビルド
`serverless package` コマンド で ビルドします。
```console
C:\Temp\hello-serverless> node_modules\.bin\serverless package
Serverless: Bundling with Webpack...
ts-loader: Using typescript@2.6.2 and C:\Temp\hello-serverless\tsconfig.json
Time: 2085ms
         Asset     Size  Chunks             Chunk Names
    handler.js  2.94 kB       0  [emitted]  handler
handler.js.map  3.18 kB       0  [emitted]  handler
   [0] ./handler.ts 350 bytes {0} [built]
Serverless: Packaging service...
```

TypeScript が コンパイルされ、JavaScript と ソースマップ が 作られ、`.serverless` ディレクトリに AWS CloudFormation の テンプレート と Lambda の デプロイ Zip が 作られます。
![](/articles/assets/lulzneko/serverless/hello/11.png)


## デプロイ と 稼働テスト
ビルドに成功したら `serverless deploy` コマンド で AWS 環境 へ デプロイします。
デプロイにあたっては AWS IAM ユーザー の アクセス・キー が 必要となります。あらかじめ作成アクセス・キーを取得し、`%USERPROFILE%\.aws\credentials` (e.g. `C:\Users\username\.aws\credentials`) に 記述しておきます。
IAM の アクセス権 は `PowerUserAccess` では足りず IAM 周りの権限追加が必要です。まだ絞り込みきれておらず結局 `AdministratorAccess` を 使ってしまっています。頑張らないと。。。
```txt
[hello]
region = us-west-2
aws_access_key_id = ZDEJREC4D1GM3DXXXX
aws_secret_access_key = ce4FH4Dcyh5DGh2dDGG4hDk6DYfhDAjrFhXXXX
```

デプロイを実行します。
`serverless deploy --aws-profile hello` と `--aws-profile` で `credentials` の `[]` で 指定したプロファイル名を指定します。 
```console
C:\Temp\hello-serverless> node_modules\.bin\serverless deploy --aws-profile hello
(省略)
endpoints:
  GET - https://ca3kg0hrXX.execute-api.us-east-1.amazonaws.com/dev/hello
functions:
  hello: hello-serverless-dev-hello
```

無事にデプロイできると 自動生成された API Gateway の エンドポイントが出力されます。
![](/articles/assets/lulzneko/serverless/hello/12.png)

ブラウザでアクセスすると、`handler.ts` の 実装の通り「Go Serverless Webpack (Typescript) v1.0! Your function executed successfully!」の メッセージ と HTTP リクエストの内容が表示されます。
![](/articles/assets/lulzneko/serverless/hello/13.png)


## お掃除
`serverless remove` で AWS 環境をクリーンアップすることができます。
```console
C:\Temp\hello-serverless> node_modules\.bin\serverless remove --aws-profile hello
Serverless: Getting all objects in S3 bucket...
Serverless: Removing objects in S3 bucket...
Serverless: Removing Stack...
Serverless: Checking Stack removal progress...
..........
Serverless: Stack removal finished...
```
![](/articles/assets/lulzneko/serverless/hello/14.png)


## ソースコード
今回作成した部分までのソース を GitHub へ アップしました。
[https://github.com/riotz-works/samples-hello-serverless/tree/0.0.1](https://github.com/riotz-works/samples-hello-serverless/tree/0.0.1)



----
初期設定に多少の作業がありましたが、Serverless Framework を 使うことで簡単にデプロイとクリーンアップができました。

アプリケーションとしては Lambda で メッセージを返すレベルではありますが、インフラ面を考えると、これを全て AWS マネジメントコンソール ではかなりの手間がかかります。それを Serverless Framework が 補ってくれるので、サーバレス開発のインフラ運用が削減できるメリットに加えて、インフラ構築も格段と楽になるのではないでしょうか。

また Try & Error を 繰り返すうちに AWS 環境 が ついつい散かってしまい、よくわからないリソースが放置されるケースが出てきたりします。開発環境とはいえ Serverless Framework で 環境を作るようにすると片付けも簡単ですね。

引き続き、このプロジェクトに設定やコードを加えて もう少し発展させていきたいと思います。
