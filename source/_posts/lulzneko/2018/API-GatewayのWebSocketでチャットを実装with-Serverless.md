---
title: API Gateway の WebSocket でチャットを実装 w/Serverless Framework
permalink: implement-chat-using-websocket-of-api-gateway-with-serverless-framework
date: 2018-12-22
author: lulzneko
categories: サーバーレス
tags:
- AWS Lambda
- DynamoDB
- Serverless Framework
---

![](/articles/assets/lulzneko/serverless/serverless.jpg "サーバーレス")

この記事は [Serverless Advent Calendar 2018](https://qiita.com/advent-calendar/2018/serverless) の 22日目になります。

AWS re:Invent で発表された API Gateway の WebSocket 対応、ついに利用できるようになりました！
WebSocket がサーバーレスで簡単に利用できるようになるとアプリの幅も広がり、いろいろなことができるようになります。
さっそく API Gateway の WebSocket を試してみます。

※ 今回 Serverless Framework の serverless-websockets-plugin を使いますが 2018年12月現在、暫定の実装になるとのことです。API Gateway の WebSocket 対応が AWS CloudFormation で未サポートのため Serverless Framework 本体には含まれず Plugin になっているとのこと。正式版では構文など変わる可能性があることに注意が必要です。

しかしながら "With all that out of the way, play with our new presents!" ということで、楽しんでみましょう！

**環境**
本記事の開発環境は以下となります。
- Windows 10 64bit + WSL Ubuntu 18.04.1 LTS
- Visual Studio Code
- Node.js 8.10.0
- Yarn 1.12.3
- TypeScript 3.2.2
- Serverless Framework 1.35.1
- Serverless Websockets Plugin 1.0.0

**参考情報**
- [[発表]Amazon API GatewayでWebsocketが利用可能](https://aws.amazon.com/jp/blogs/news/announcing-websocket-apis-in-amazon-api-gateway/)
- [Using API Gateway WebSockets with the Serverless Framework](https://serverless.com/blog/api-gateway-websockets-example/)
- [serverless/serverless-websockets-plugin](https://github.com/serverless/serverless-websockets-plugin)


## WebSocket について
HTTP ベースの Web API ではサーバーに対して接続、リクエストを送り、処理結果のレスポンスを受け取り、切断終了の流れになります。
必要に応じて、このリクエスト/レスポンスのやり取りを繰り返す形になります。

それに対して WebSocket はサーバーに接続後、コネクションを維持したまま双方向でメッセージを送ることができます。
これは通常のリクエスト/レスポンスに当たるやり取りのほかに、サーバー側から任意のタイミングでメッセージを送れます。

たとえば私たちがハッカソンで作った「[ラップ、タップ、アップ 🎶](https://riotz.works/rap-tap-app/)」では、演奏者へのフィードバックとして「👍」を送ることができ、その数をリアルタイムでカウントする機能があります。

この「👍」をリアルタイムでカウントする部分を HTTP ベースの Web API で作るとすると、アプリから「👍」の数を取得する API を繰り返し「リクエスト/レスポンス」して受け取る必要が出てきます。
仮に数に変化がなかったとしてもアプリ側は知るすべがないので、繰り返し「👍」の数を取得しに行かなければなりません。

WebSocket が使える場合、コネクションは維持したまま必要な時に「👍」の数をサーバー側がアプリへメッセージを送ることができるようになります。
「👍」の数に変化がなかった場合は維持されているコネクションの中にメッセージが流れないだけでアプリはとくに何もする必要はありません。変化があった時だけサーバーが教えてくれるので、それに応じてアプリの表示を変えるだけになります。

これにより不要なリクエスト/レスポンスを減らせるほか、よりリアルタイムに近い状態で変化を受け取ることができるようになります。
HTTP ベースでは「リクエスト/レスポンス」で受け取り、次のリクエストを出すまでの間にあった変化は結果しか受け取れません。５だった「👍」を次のリクエスト/レスポンスでは 10になっているかもしれません。

WebSocket では状態の変化を断続的に返すことも可能になるので、５、６、７... とメッセージを返せます。

これにより、よりスムーズに描画を変えていくことができるようになります。
これはチャットのようなコミュニケーションや金融などの取引の価格情報などに使うことができます。


今回 [API Gateway](https://aws.amazon.com/jp/api-gateway/) がこの WebSocket に対応したので、その使い方をチャットを実装することで確認します。
(WebSocket でチャットは定番すぎますが、「👍」のカウントだとさみしいですからね)

※「[ラップ、タップ、アップ 🎶](https://riotz.works/rap-tap-app/)」では、API Gateway がまだ WebSocket が扱えなかったので、 [Firebase Realtime Database](https://firebase.google.com/docs/database/) を使って実現しています。


## Node.js プロジェクトの準備
Serverless Framework のボイラープレートを使って、TypeScript の Node.js プロジェクトを作成します。

プロジェクトのディレクトリを作成しプロジェクトのひな型を作ります。
```console
username@pc:~$ mkdir samples-apigateway-websocket-chat
username@pc:~$ cd samples-apigateway-websocket-chat
username@pc:~/samples-apigateway-websocket-chat$

username@pc:~/samples-apigateway-websocket-chat$ npx serverless create --template aws-nodejs-typescript
Serverless: Generating boilerplate...
(省略)
Serverless: Successfully generated boilerplate for template: "aws-nodejs-typescript"
Serverless: NOTE: Please update the "service" property in serverless.yml with your service name
```

続いて Serverless Framework をローカルで追加し、AWS SDK と WebSocket クライアント wscat、必要なパッケージを最新化してインストールします。
Serverless Framework はグローバルに追加して使うようですが、複数人開発でグローバルのを使っているとバージョンの違いなどでトラブルがあったので `package.json` で明示して、それを使うようにしているためです。サクッと試すにはグローバルでもよいでしょう。
```console
username@pc:~/samples-apigateway-websocket-chat$ yarn add -D serverless serverless-websockets-plugin wscat
username@pc:~/samples-apigateway-websocket-chat$ yarn upgrade --latest
username@pc:~/samples-apigateway-websocket-chat$ yarn add aws-sdk
```

`package.json` をプロジェクトに合わせて修正します。
主に `name` `description` `author` あたりを合わせるとよいでしょう。
また、作成しているのはアプリケーションなのでモジュール公開の防止のために `"private": true` を設定しておくとよいでしょう。private の詳細は、こちらの [package.json | npm Documentation](https://docs.npmjs.com/files/package.json#private) をご確認ください。
```json
{
  "name": "samples-apigateway-websocket-chat",
  "version": "1.0.0",
  "private": true,
  "dependencies": {
    "aws-sdk": "^2.382.0"
  },
  "devDependencies": {
    "@types/aws-lambda": "8.10.17",
    "@types/node": "^10.12.18",
    "serverless": "^1.35.1",
    "serverless-webpack": "^5.1.1",
    "serverless-websockets-plugin": "^1.0.0",
    "source-map-support": "^0.5.6",
    "ts-loader": "^5.3.2",
    "typescript": "^3.2.2",
    "webpack": "^4.5.0",
    "wscat": "^2.2.1"
  },
  "author": "Riotz.works (https://riotz.works)",
  "license": "MIT"
}

```


## Serverless Framework の設定
`serverless.yml` を編集し Serverless Framework の設定を行います。
```yaml
service:
  name: samples-apigateway-websocket-chat

plugins:
  - serverless-webpack
  - serverless-websockets-plugin

provider:
  name: aws
  stage: ${ opt:stage, 'dev' }
  region: ${ opt:region, 'us-west-2' }
  runtime: nodejs8.10
  iamRoleStatements:
    - Effect: Allow
      Action:
        - execute-api:ManageConnections
      Resource:
        - arn:aws:execute-api:*:*:**/@connections/*
    - Effect: Allow
      Action:
        - dynamodb:Scan
        - dynamodb:PutItem
        - dynamodb:UpdateItem
        - dynamodb:DeleteItem
      Resource:
        - Fn::GetAtt: [ ChatConnectionsTable, Arn ]
  environment:
    DYNAMODB_CONNECTIONS:
      Ref: ChatConnectionsTable
  websocketApiName: ${self:service.name}-${self:provider.stage}
  websocketApiRouteSelectionExpression: $request.body.action

functions:
  connect:
    handler: handler.connect
    events:
      - websocket:
          routeKey: $connect
  disconnect:
    handler: handler.disconnect
    events:
      - websocket:
          routeKey: $disconnect
  defaultMessage:
    handler: handler.defaultMessage
    events:
      - websocket:
          routeKey: $default
  sendMessage:
    handler: handler.sendMessage
    events:
      - websocket:
          routeKey: sendMessage

resources:
  Resources:
    ChatConnectionsTable:
      Type: AWS::DynamoDB::Table
      Properties:
        TableName: ${self:service.name}-connections-${self:provider.stage}
        AttributeDefinitions:
          - AttributeName: ConnectionId
            AttributeType: S
        KeySchema:
          - AttributeName: ConnectionId
            KeyType: HASH
        ProvisionedThroughput:
          ReadCapacityUnits: 1
          WriteCapacityUnits: 1
        SSESpecification:
          SSEEnabled: True
        StreamSpecification:
          StreamViewType: NEW_AND_OLD_IMAGES
```

`plugins` に API Gateway WebSocket を使うための `serverless-websockets-plugin` を追加します。

`iamRoleStatements` では、API Gateway の WebSocket API を呼び出すための `execute-api:ManageConnections` に許可を与えます。
また WebSocket に接続しているクライアントのコネクションを管理するための DynamoDB が必要なので、DynamoDB に関する許可も与えます。

30行目、31行目の `websocketApiName` と `websocketApiRouteSelectionExpression` は、serverless-websockets-plugin の設定になります。`websocketApiName` は管理用にわかりやすい名前を設定しておくとよいでしょう。

`functions` は WebSocket のイベントとアクションに合わせて定義を行います。
`events` に `websocket` が追加でき、 `websocketApiRouteSelectionExpression` に設定された Key の値と `routeKey` の文字列のマッチングによって起動する Lambda を振り分けています。
`$connect` `$disconnect` は WebSocket の接続と切断に対応します。また `$default` はマッチするアクションがない場合に呼び出されます。
最後の `sendMessage` が WebSocket で受け取るメッセージのキーになります。具体的には `{ "action":"sendMessage", "data":"hello world" }` のような JSON の `action` に入る文字列のマッチングになります。

`resources` は WebSocket のコネクション管理用 DynamoDB を定義しています。
今回は最低限の `ConnectionId` だけを管理します。名前など無しの完全に匿名のメッセージだけのチャットになります。(WebSocket の動きと実装を試すってことで💦)


## AWS Lambda の実装
`handler.ts` に処理を実装します。
```typescript
import { APIGatewayEventRequestContext, APIGatewayProxyEvent, APIGatewayProxyHandler, APIGatewayProxyResult } from 'aws-lambda';
import { ApiGatewayManagementApi, DynamoDB } from 'aws-sdk';

interface APIGatewayProxyEventWithWebSocket extends APIGatewayProxyEvent {
  requestContext: APIGatewayEventRequestContextWithWebSocket
}
interface APIGatewayEventRequestContextWithWebSocket extends APIGatewayEventRequestContext {
  domainName: string,
  connectionId: string
}


const apigateway = ({ domainName, stage }): ApiGatewayManagementApi => new ApiGatewayManagementApi({ endpoint: `${domainName}/${stage}` });
const dynamodb = new DynamoDB.DocumentClient();

export const connect: APIGatewayProxyHandler = async (event: APIGatewayProxyEventWithWebSocket): Promise<Result> => {
  console.debug('Starting Lambda handler: event=%s', JSON.stringify(event));
  await dynamodb.put({ TableName: process.env.DYNAMODB_CONNECTIONS, Item: { ConnectionId: event.requestContext.connectionId }}).promise();
  return new Result('Connected');
};

export const disconnect: APIGatewayProxyHandler = async (event: APIGatewayProxyEventWithWebSocket): Promise<Result> => {
  console.debug('Starting Lambda handler: event=%s', JSON.stringify(event));
  await dynamodb.delete({ TableName: process.env.DYNAMODB_CONNECTIONS, Key: { ConnectionId: event.requestContext.connectionId }}).promise();
  return new Result('Disconnected');
};

export const defaultMessage: APIGatewayProxyHandler = async (event: APIGatewayProxyEventWithWebSocket): Promise<Result> => {
  console.debug('Starting Lambda handler: event=%s', JSON.stringify(event));
  const params: ApiGatewayManagementApi.Types.PostToConnectionRequest = {
    ConnectionId: event.requestContext.connectionId,
    Data: 'Error: Invalid action type'
  };
  await apigateway(event.requestContext).postToConnection(params).promise();
  return new Result('Error: Invalid action type', 500);
}

export const sendMessage: APIGatewayProxyHandler = async (event: APIGatewayProxyEventWithWebSocket, context, callback): Promise<Result> => {
  console.debug('Starting Lambda handler: event=%s', JSON.stringify(event));
  const connections = await dynamodb.scan({ TableName: process.env.DYNAMODB_CONNECTIONS, ProjectionExpression: 'ConnectionId' }).promise();
  await Promise.all(connections.Items.map(async ({ ConnectionId }) => {
    try {
      const params: ApiGatewayManagementApi.Types.PostToConnectionRequest = {
        ConnectionId: ConnectionId,
        Data: JSON.parse(event.body).data
      };
      await apigateway(event.requestContext).postToConnection(params).promise();
    } catch (e) {
      if (e.statusCode === 410) {
        await dynamodb.delete({ TableName: process.env.DYNAMODB_CONNECTIONS, Key: { ConnectionId: ConnectionId }}).promise();
      } else {
        throw e;
      }
    }
  }));
  return new Result('OK');
}


class Result implements APIGatewayProxyResult {
  public constructor(public body: string, public statusCode: number = 200) {}
}
```

4行目～10行目の interface 宣言ですが、こちらは API Gateway の WebSocket 対応の TypeScript 型定義がまだ入っていないので補完するために宣言しています。 `requestContext` に入ってくる `domainName` と `connectionId` が必要なため追加しています。型定義が更新されたら不要となります。

13行目 `ApiGatewayManagementApi` のインスタンスを取得する関数ですが、API Gateway から WebSocket のメッセージを返す際に `endpoint` が必要となり、これが `domainName` と `stage` から決まってきます。固定値の場合はよいのですが、今回のように動的に決まってくる場合は `requestContext` から受け取った `domainName` と `stage` が必要なため毎回インスタンスを生成しています。

16行目～58行目で `serverless.yml` に定義した Lambda のエントリーポイントと実装があります。

`connect` と `disconnect` は WebSocket の「接続/切断」の際に呼び出されます。
接続時に `connectionId` を DynamoDB へ保存し、切断時に削除しています。切断は呼び出されないこともあるので、注意が必要です。

`defaultMessage` は WebSocket で受け取ったメッセージに対応するアクションがなかった場合に呼び出されます。ここでは "Error: Invalid action type" をクライアントに返しています。
WebSocket でメッセージを返すには `ApiGatewayManagementApi` の `postToConnection()` を呼び出します。その際にクライアントの `connectionId` が必要となります。
この関数のようにメッセージを送ってきたクライアントだけにメッセージを送る場合は `requestContext` の `connectionId` が使えるので、それを使ってエラーメッセージを返します。

最後の `sendMessage` は、チャットのコメントを送る処理になります。リアルタイムに複数クライアントへメッセージを送る WebSocket を使う処理の肝となる部分です。
複数クライアントにメッセージを返すには、メッセージを送るべきクライアントすべての `connectionId` が必要となり、それぞれに `ApiGatewayManagementApi#postToConnection()` を行う必要があります。
つまり API Gateway が接続中のクライアントを知ってくれてるわけではないので、自前で管理する必要があり、そのために DynamoDB を用意しているものになります。

今回は全員参加の簡単な実装なので、DynamoDB のコネクション管理テーブルをスキャンし、全件に対して `ApiGatewayManagementApi#postToConnection()` を呼び出しています。（なんか、もっとスマートな実装できないかなぁ）

`ApiGatewayManagementApi#postToConnection()` を呼び出す際に、コネクションが切断されているクライアントがありえます。先ほど「切断は呼び出されないこともあるので、注意が必要です。」と書きました通り、クライアントが切断されてもイベントが呼び出されないケースもあります。そのため DynamoDB への削除されず `connectionId` が残るケースもありえます。その場合 `statusCode` が `410` のエラーが投げられるので、そのエラーが投げられた場合は DynamoDB から該当する `connectionId` を削除しておき再発防止しておきます。


## デプロイ ＆ 実行！
デプロイは Serverless Framework がしっかりやってくれるので、以下のコマンドで行えます。
あらかじめ AWS の Access Key の用意とプロファイルを設定しておきます。(デフォルト・プロファイルでない場合は `--aws-profile [your profile name]` と利用するプロファイル名を `--aws-profile` で指定します)
```console
username@pc:~/samples-apigateway-websocket-chat$ yarn serverless deploy
(省略)
Serverless: Deploying Websockets API named "samples-apigateway-websocket-chat-dev"...
Serverless: Websockets API named "samples-apigateway-websocket-chat-dev" with ID "6ovkt8XX" has been deployed.
Serverless:   Websocket URL: wss://6ovkt8XX.execute-api.us-west-2.amazonaws.com/dev/
Done in 137.90s.
```

デプロイが完了すると、コマンドの実行結果に WebSocket の URL が出力されます。
その URL に対して `wscat` で接続し API Gateway WebSocket の動きを確認します。
```console
username@pc:~/samples-apigateway-websocket-chat$ yarn wscat -c wss://6ovkt8XX.execute-api.us-west-2.amazonaws.com/dev/
connected (press CTRL+C to quit)
>
```

接続できると `>` で入力待ちになります。 `{ "action":"sendMessage", "data":"hello world" }` とメッセージを送ると、チャットのコメントが帰ってきます。
```console
username@pc:~/samples-apigateway-websocket-chat$ yarn wscat -c wss://6ovkt8XX.execute-api.us-west-2.amazonaws.com/dev/
connected (press CTRL+C to quit)
> { "action":"sendMessage", "data":"hello world" }
< hello world
>
```

複数のコンソールから接続すると、送ったコメントがすべてのコンソールに流れてきます。
WebSocket でリアルタイムに複数クライアントへメッセージが遅れていることが確認できます。

チャットとは名ばかりのコメントだけを送りあうだけの実装(しかも wscat によるコマンドライン)でしたが、API Gateway WebSocket の実装がつかめました。

Serverless Framework は今後の AWS CloudFormation 対応によって変わってくる部分がありますが、こんなに簡単に設定できるのでとても助かります。

WebSocket が簡単に利用できるようになったので、いろいろなアプリづくりに生かせそうで楽しみです。


----

では、ハッピー・サーバーレス・ライフ！ ハッピー・ホリデー！！
