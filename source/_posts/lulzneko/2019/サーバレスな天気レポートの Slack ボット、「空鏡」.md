---
title: サーバレスな天気レポートの Slack ボット、「空鏡」
permalink: serverless-weather-report-slack-bot-sora-kagami
alias: /2019/06/24/serverless-weather-report-slack-bot-sora-kagami/index.html
date: 2019-06-24
author: lulzneko
categories: アプリ
tags:
- TypeScript
- Serverless Framework
- Chart.js
- Slack
- Bot
- AWS Lambda
---


「空模様を図表に写して、天気をお伝えする「空鏡」」と題して、Slack で簡単に現在の天気情報を得られる Slack Slash Commands を作りました。そのサーバーレスなアーキテクチャを紹介します。

![](/articles/assets/lulzneko/app/sora-kagami/cover.jpg)


梅雨入りシーズン、天気予報を確認したり、お守り傘を持ったりしているかと思います。天気予報を確認するのは、いまの梅雨シーズンに限らず、次のゲリラ豪雨シーズンにも必要でしょう。普段はアプリやウェブサイトで確認しますが、Slack を使っている場合は Slack 上で確認できるとさら便利なのではないでしょうか。それを実現するのが「空鏡」です。

「空鏡(Sora-Kagami)」は Slack の Slash Commands として動作する天気レポートのサービスです。Slack で `/sora 東京` のような簡単な入力で天気情報が得られます。下図のような「現在の天気」「今後１時間の降水強度グラフ」「降水レーダー地図」で天気情報をお伝えします。(デモ環境については [後述](#デモ環境-＆-My-空鏡のセットアップについて))
![](/articles/assets/lulzneko/app/sora-kagami/01-001.png)


## アーキテクチャ
「空鏡」は AWS 上に AWS Lambda と Amazon S3 を活用したサーバーレスなサービスとして構築されています。

![](/articles/assets/lulzneko/app/sora-kagami/01-003.png)

以下、各パートについての概要です。別途詳細に解説する記事を書きたいと思います。今回はアーキテクチャとしての概要説明にとどめます。(いっぺんに書ききれない)


### Slack Slash Commands のリクエスト受付
Slack Slash Commands は ３秒以内に応答する必要があります。最近でこそ Lambda の初期起動(コールドスタート)は速くなってきましたが、それでも外部 API 連携をしているサービスではかなり厳しい条件です。今回は遅延応答、[Sending delayed responses - Slash Commands](https://api.slack.com/slash-commands#responding_response_url) を使ってレスポンスしています。これは Slack からのリクエストにいったん `200 OK` をレスポンスし、リクエストに含まれていた `response_url` へ JSON を HTTP POST して返すというものです。この形式なら仕様上 30分まではレスポンスを遅らせることができます。(30分後にレスが突然来てもビックリかもしれませんが)


### Serverless Framework による「Amazon API Gateway」と「非同期 AWS Lambda」の構成
Slack Slash Commands 遅延レスポンスを使うには、最初に `200 OK` を返すため、HTTP レスポンスとは別に非同期で本処理を行う必要があります。それを Lambda で行うには Amazon SNS や Amazon SQS へ処理を積むなどの工夫が必要だったのですが、Amazon API Gateway による非同期 Lambda の呼出しがサポートされました。`X-Amz-Invocation-Type` が `Event` の Lambda です。今回はこちらを使い `200 OK` の即時レスポンスと、非同期の本体処理を実現しています。

ただし `X-Amz-Invocation-Type` が `Event` の Lambda は、ちゃんと設定すると大変みたいです(途中で挫折した)。ここは [Serverless](https://serverless.com/) を使うと簡単です。以下は Serverless での空鏡の定義抜粋ですが `async: true` と属性を設定するだけですべてを構成してくれます！
```javascript
module.exports = {
  functions: {
    Command: {
      name: '${ self:custom.names.lambda.command }',
      handler: 'src/aws-lambda-handler/sora-kagami-command.handler',
      events: [{ http: { path: 'sora-kagami', method: 'post', cors: true, async: true }}]
    }
  }
}
```


### 非同期 AWS Lambda での処理
非同期な Lambda で構成されているとはいえ、基本的な Lambda の実装に変わりはありません。
以下は Lambda のメイン処理部分の TypeScript 実装です。
```typescript
export const handler: Handler<APIGatewayProxyEvent, void> = async (event: APIGatewayProxyEvent): Promise<void> => {
  console.debug('Starting Lambda handler: event=%s', JSON.stringify(event));

  // tslint:disable-next-line: no-any - 'cus to parse non-JSON, JavaScript object literal like strings by Slack
  const command = event.body as any as SlashCommand;
  if (Env.SLACK_TOKENS.length !== 0 && !Env.SLACK_TOKENS.includes(command.token)) {
    return console.error('Forbidden: team=%s, command=%s', command.team_domain, JSON.stringify(command, undefined, 2));
  }

  try {
    const geo = await getGeometry(command.text);
    if (!geo) { return apis.slack.response(command, { text: `場所の検索に失敗しました。\`${command.command} [郵便番号 または 地名]\` を入力してください。` }); }

    const place = await getPlace(geo);
    const weathers = await getWeathers(geo);

    const filenames = Config.FILENAMES(geo, weathers.current);
    await Promise.all([
      apis.map.get(Config.REQUEST_MAP(geo)).then(async (value: Buffer) => {
        await apis.aws.s3PutObject(Env.S3_IMAGES_BUCKET, filenames.map, value, Config.CONTENT_TYPE_MAP);
      }),
      Config.CHART_CANVAS().renderToBuffer(createChartOps(weathers.data), Config.CONTENT_TYPE_CHART).then(async (value: Buffer) => {
        await apis.aws.s3PutObject(Env.S3_IMAGES_BUCKET, filenames.chart, value, Config.CONTENT_TYPE_CHART);
      })]
    );

    const message = createMessage(place, weathers, filenames, geo);
    await apis.slack.response(command, message);
  } catch (err) { await handleError(err as object, command); }
};
```

一番大きな違いは、１行目のメソッドの戻り値 `void`(またはレスポンスを返す `context/callback` を使わない点)です。API Gateway が `200 OK` をレスポンスしているため、Lambda からはレスポンスは返せません。そのためレスポンスにかかわる処理はありません。

変わって `await apis.slack.response(command, message);` で Slack Slash Commands の遅延レスポンス `response_url` へ HTTP POST して終了しています。また12行目のエラーレスポンスも同様に Slack へ返しています。


### 天気データは、Yahoo!デベロッパーネットワーク YOLP(地図)
天気や地点の検索は Yahoo! の [YOLP(地図) API](https://developer.yahoo.co.jp/webapi/map/) を使っています。地図に関する API が豊富に用意されていて、利用方法も簡単です。
API の [利用制限](https://developer.yahoo.co.jp/appendix/rate.html) は １日 50,000回までです。今回１リクエストで４回 API を使うので、デモ環境がもしかしたら辛そうです(このブログの PV から考えると十分耐えられると思いますが)。なお商用利用する場合は「[お問い合わせください](https://www.yahoo-help.jp/app/answers/detail/p/537/a_id/43405)」とのことです。

今回は以下の API を使っています。

| 空鏡での機能                       | YOLP API 名                                                                                                    |
|:-----------------------------------|:---------------------------------------------------------------------------------------------------------------|
| 郵便番号が入力された場合の地点検索 | [郵便番号検索API](https://developer.yahoo.co.jp/webapi/map/openlocalplatform/v1/zipcodesearch.html)            |
| 郵便番号でない場合の地点検索       | [場所情報API](https://developer.yahoo.co.jp/webapi/map/openlocalplatform/v1/placeinfo.html)                    |
| 地点からのエリア名、周辺情報取得   | [コンテンツジオコーダAPI](https://developer.yahoo.co.jp/webapi/map/openlocalplatform/v1/contentsgeocoder.html) |
| 天気情報                           | [気象情報API](https://developer.yahoo.co.jp/webapi/map/openlocalplatform/v1/weather.html)                      |
| 降水レーダー地図の生成             | [スタティックマップAPI](https://developer.yahoo.co.jp/webapi/map/openlocalplatform/v1/static.html)             |


### グラフの描画は Chart.js
天気レポートの中段にある、今後１時間の降水強度予報グラフは [Chart.js](https://www.chartjs.org/) を使っています。
![](/articles/assets/lulzneko/app/sora-kagami/01-002.png)

Chart.js は簡単なコードで綺麗なグラフを描画してくれます。以下はグラフ作成部分のコードです。(必要箇所のみ抜粋)
```typescript
const createChartOps = (weathers: Weather[]): ChartConfiguration => {
  const data = weathers.map((value: Weather) => value.Rainfall);
  const borderColor = createChartColor(weathers, Config.ALPHA_BORDER);
  const backgroundColor = createChartColor(weathers, Config.ALPHA_BACKGROUND);
  return {
    type: 'bar',
    data: {
      labels: weathers.map((value: Weather) => dayjs(value.Date).format('H:mm')),
      datasets: [{ data, borderColor, backgroundColor }]
    },
    options: {
      legend: { display: false },
      scales: { yAxes: [{ ticks: { beginAtZero: true }}]}
    }
  };
};
const createChartColor = (weathers: Weather[], opacity: number): string[] =>
  weathers.map((value: Weather) => hexToRgba(WeatherForecastApi.getLevelColor(value.Rainfall), opacity));
```

`data` に表示するグラフの配列を渡します。YOLP のレスポンスから `Rainfall` の値を取り出して配列化しただけです。`borderColor` と `backgroundColor` は、[雨雲レーダー - Yahoo!天気・災害](https://weather.yahoo.co.jp/weather/zoomradar/) の降水量の凡例からグラフの色を付けるための処理で、これも配列です。
基本的に配列を渡して、その配列群のインデックスが合うところを使う形です。

今回は１つしか描画しませんが `datasets` に複数の配列セットを渡すことで複数のグラフを描画できます。


### Chart.js のグラフをサーバーサイドで画像化して Amazon S3 へ
Slack は、画像 URL を渡すと自動的に展開してくれます。逆に言うと URL がないと画像が表示できないということです。

画像をウェブサイトとしてホスティングするには S3 を使うのが AWS を使う上での常套手段でしょう。しかしながら、Chart.js が描画する Canvas を画像化するところがハマりました。

今回は [SeanSobey/ChartjsNodeCanvas](https://github.com/SeanSobey/ChartjsNodeCanvas) を使い、Node.js 上で Canvas を扱えるようにしています。
実態は [Automattic/node-canvas](https://github.com/Automattic/node-canvas) なのですが、こちらと Lambda の実行ランタイム環境との組み合わせの問題か、 Node.js 10.x では動作しませんでした。いったん Node.js 8.10 で動かしています。組合せとかもいろいろあるので、今後しっかりと調べたいと思います。


### CircleCI
CI/CD に [CircleCI](https://circleci.com/) を使っています。
基本的に Serverless がすべてをやってくれ `serverless deploy` だけなので難しいビルドプロセスはありません。

１つだけポイントがあります。デプロイ・ステージとブランチをマッピングしています。

今回、実行環境として Develop, QAS, Production の３つを用意しています。Develop は言わずもがなですが、デモ環境が必要なので分離して QAS を使っています。Production は使う予定ないですが、ソース公開にあたって設定しています。

それに応じてステージのキーワードを３文字で `dev`、`qas`、`prd` に。対応するブランチを `master`、`stable`、`production` としています。

各ブランチにプッシュされると自動的にビルドが走り、ステージに応じた環境へデプロイされます。
継続的に開発を行うには CI/CD は必須であるとともに、今回のように複数環境へ対応できるようにすると便利です。


### Slack へ、画像をポストする際のテクニック
Slack で、画像や URL をポストするときのテクニックを２つ。

**画像 URL はランダムにする**
URL の最後に `?ランダムな値` を付けます。パラメーター名が必要な場合は適当なものを付けて構いませんが、とにかくランダムな値(= URL) を作ってポストします。

これは Slack が同じ URL の文字列の場合、折り畳みで表示するためです。今回は日時と地点を使ってファイル名を作っていますが、YOLP が 10分おきのデータを作るため、たとえば同じ時間ゾーンだと画像 URL が同じなります。そうすると画像が展開されません。それを防止するためにランダムな値を付けます。

**メッセージフォーマットを使う**
これは[公式ドキュメント](https://api.slack.com/docs/message-formattinga)にもあるのでテクニックまではいかないですが、使うと便利です。
画像などの URL をポストすると、生の URL が投稿されて、続いて画像が表示されます。ちょっと見た目が良くないので `<URL|表示文字列>` と `<|>` 形式でポストします。

これにより、生 URL が表示されるのを避けられます。もっと言ってしまうと `<URL| >` と半角スペースにしてしまうと何も表示されません。(画像サイズの 129kB のような文字列はどうしても残ってしまいますが)


### 今後の展望
ゲリラ豪雨シーズンに向けて、着実にバージョンアップしていきたいと思います。

- 指定地点で１時間以内の降水予報があったら自動通知
  現在は自分で Slash Commands をたたかないと表示されませんが、自動通知してくれるようにしたいです。
  ハードコードでよければ Amazon CloudWatch Events で cron 処理からの、Slack Incoming Webhooks で簡単ですが、地点は登録できるようにしたいですし、複数ユーザーに対応したい。そうなると結構複雑で今回は落としました。

- Slack 以外のツールへ対応
  あまり需要がないかもしれませんが、何か他のツールにも対応してみたいです。
  ただアプリとウェブを使わないが、そのツールを使うというシーンが難しいです。ゆえに Slack として作りました。

- ボット化
  今回「Slack ボット」と名乗っていますが、実態は Slash Commands です。ちゃんと会話の中から天気のキーワードを拾って返せるようにしたいです。これは WebSocket **クライアント** を作る必要があり、サーバーレスにしにくいというハードルがあります。サーバーレスの形態は崩したくないので、チャレンジですね。

その他、組み込んだらおもしろい機能などがあったら、ぜひ Issues や Pull Request をいただけたら幸いです。(もちろん Twitter DM [@lulzneko](https://twitter.com/lulzneko) でも)
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://github.com/riotz-works/sora-kagami" data-iframely-url="//cdn.iframe.ly/bSh0JOz?iframe=card-small"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>


## デモ環境 ＆ My 空鏡のセットアップについて
「空鏡」はデモ環境があります。空鏡デモを利用するには、お使いの Slack チームへ [Slash Commands](https://riotz.slack.com/apps/A0F82E8CA-slash-commands) を導入します。

[コマンドを選択する] を `/sora` で、以下の Slack Slash Commands を設定します。

| 項目                     | 設定値                                                                        |
|:-------------------------|:------------------------------------------------------------------------------|
| [URL]                    | `https://4tvr294f4h.execute-api.ap-northeast-1.amazonaws.com/qas/sora-kagami` |
| [アイコンをカスタマイズ] | 任意の画像/絵文字 (※ <img src="/articles/assets/lulzneko/app/sora-kagami/icon.jpg" width="24" style="display: inline" /> もご利用いただけます)                      |
| [名前をカスタマイズ]     | 任意の文字列 ( e.g. `空鏡` )                                                  |
| [説明]                   | 任意の文字列 ( e.g. `現在の空模様をレポートします` )                          |
| [使い方のヒント]         | 任意の文字列 ( e.g. `[郵便番号 or 地名]` )                                    |

以降、設定した Slack で　/[コマンド名] [郵便番号 or 地名] を入力すると、空鏡デモから天気レポートが返信されます。

また「空鏡」は MIT ラインセスの元 OSS として公開しています。
利用している AWS の環境へデプロイすることもできます。

詳しくはリポジトリの README を、ご参照ください。
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://github.com/riotz-works/sora-kagami#readme" data-iframely-url="//cdn.iframe.ly/bSh0JOz?iframe=card-small"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>


## 参考情報

### Slack 関連
- [Slash Commands | Slack](https://api.slack.com/slash-commands#app_command_handling)
- [Formatting messages | Slack](https://api.slack.com/docs/message-formatting)
- [Attaching content and links to messages | Slack](https://api.slack.com/docs/message-attachments)

### Yahoo! JAPAN 関連
- [ご利用ガイド - Yahoo!デベロッパーネットワーク](https://developer.yahoo.co.jp/start/)
- [YOLP(地図):気象情報API - Yahoo!デベロッパーネットワーク](https://developer.yahoo.co.jp/webapi/map/openlocalplatform/v1/weather.html)
- [YOLP(地図):場所情報API - Yahoo!デベロッパーネットワーク](https://developer.yahoo.co.jp/webapi/map/openlocalplatform/v1/placeinfo.html)
- [YOLP(地図):郵便番号検索API - Yahoo!デベロッパーネットワーク](https://developer.yahoo.co.jp/webapi/map/openlocalplatform/v1/zipcodesearch.html)
- [YOLP(地図):コンテンツジオコーダAPI - Yahoo!デベロッパーネットワーク](https://developer.yahoo.co.jp/webapi/map/openlocalplatform/v1/contentsgeocoder.html)
- [YOLP(地図):Yahoo!スタティックマップAPI - Yahoo!デベロッパーネットワーク](https://developer.yahoo.co.jp/webapi/map/openlocalplatform/v1/static.html)



----

非同期 AWS Lambda は Serverless から設定すると属性１つなので、とても簡単です。HTTP Request からの非同期処理はあまり作らないかもしれませんが、Serverless のこの機能は覚えておくとよいでしょう。

「空鏡」によって少しでも雨をよけられたら幸いです。
