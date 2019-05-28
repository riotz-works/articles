---
title: Serverless Meetup Tokyo 12 にて「サーバーレスなウェブフロントを実現する JAMStack」について発表をしました
permalink: made-presentation-about-jamstack-at-serverless-meetup-tokyo-12
date: 2019-05-27
author: lulzneko
categories: プレゼンテーション
tags:
- JAMStack
- Gridsome
- Nuxt.js
- Hexo
- AWS Lambda
---

2019年5月27日に開催された「Serverless Meetup Tokyo #12」で『サーバーレスなウェブフロントを実現する JAMStack』と題して、ウェブフロントをサーバーレスで実現する世界について発表をしました。その発表サマリーです。

![](/articles/assets/lulzneko/presentation/serverless-meetup-tokyo-12/serverless-meetup-tokyo-12.png)


## Serverless Meetup Tokyo 概要
発表したイベント [Serverless Meetup Tokyo #12](https://serverless.connpass.com/event/130509/) は、サーバーレス・アーキテクチャや周辺技術に関するコミュニティ [Serverless](https://serverless.connpass.com/) のイベントで「東京」「大阪」「札幌」「福岡」で定期的に、また「沖縄」でも開催されました。

会場は [Speee](https://speee.jp/company/) さん。六本木一丁目駅近くのステキな場所＆素晴らしいオフィスです！
![](/articles/assets/lulzneko/presentation/serverless-meetup-tokyo-12/04.jpg)


## 発表資料
『[サーバーレスなウェブフロントを実現する JAMStack](https://riotz.works/slides/?2019-serverless-meetup-tokyo12)』の発表資料はこちらになります。(下記、スライド埋め込み)
<div class="slide"><iframe src="https://riotz.works/slides/?2019-serverless-meetup-tokyo12"></iframe></div>


## サマリー
発表の主旨は「フロントも含めて、まるっとサーバーレスにしよう」です。

最近 JAMStack な話でプレゼンする機会が多いのですが、私が得意としている領域はサーバーレスなサーバーサイドの開発で、フロント系はハッカソンなどで使うための必要最小限スキルセットになります。そんな私が、なぜフロントに関する JAMStack の話をするのかというと、今回の主旨「フロントも含めて、まるっとサーバーレスにしよう」を広めたいからです。

### 発表の背景
その「**フロントも含めて**、まるっとサーバーレスに」を広めたい理由を説明したく、今回の発表の背景となるスライドを作りました。
![](/articles/assets/lulzneko/presentation/serverless-meetup-tokyo-12/01.png)

この図の下にあるスライドのキャプチャですが、左側は私たちが開発している IoT のプラットフォーム概要で、右側がアーキテクチャ概略図です。(もっとキャッチーな製品名があるのですが、資料公開の都合上とても分かりにくい名称で申し訳ない💦)

工場、販売、配送の各機能からバッチによるデータ連携や、Web API からも同様のデータ投入される機能があります。そして、それらのデータを加工して IoT 機器がアクセスする機能や、IoT のサービス・プロバイダーへデータ提供を行っています。
サーバーレス、とくに AWS Lambda の FaaS を中心とした実装になっています。

ここに関連機能としてウェブサイトを持つことになり環境構築依頼がきました。「AWS だし、ちょっと簡単に 💎 サーバー立てておいてよ。そんで運用もヨロー」と。私たちは AWS を使っていますが、FaaS を中心に DynamoDB や SNS、SQS といったフルマネージドのサービスを使っているチームです。EC2 はおろか、コンテナーさえも持っていないし運用していません。持ちたくないから FaaS にこだわって開発をしているのです。

そのチームに環境構築はともかく、運用をさせるというのは難しいものがあります。一概に AWS といっても、機能はたくさんあるわけで必ずしもすべてを使いこなせるというわけではないということです。

では、どのような形だったら良かったのか。それが JAMStack になります。


### JAMStack とは
では、JAMStack とは何でしょうか。
![](/articles/assets/lulzneko/presentation/serverless-meetup-tokyo-12/02.png)

「クライアントサイド JavaScript」「再利用可能な API」「構築済みのマークアップ」で構築されたサイトのことになります。要素だけだと分かりにくいですが、ざっくりいうと HTML に静的化されたされたサイトで、動的要素はブラウザ上の JavaScript から Web API を呼び出す形にしましょうという考え方になります。

そうすることで、さまざまなメリットが生まれます。
HTML として静的化されているので、すべてを CDN に配置することができパフォーマンスに優れ、スケールも CDN に任せることができます。そして、ただの HTML で動的要素がないので攻撃対象を局所化できてセキュリティを高められます。
また、開発者の視点としても開発対象が分離できるので役割分担が明確化します。テンプレートエンジンを使っていると、ちょっとした修正もフロントの担当なのか、サーバー側が修正するのかといった部分が出てきます。JAMStack ではフロントはフロントの担当です。サーバー側のロジックは Web API として分離しているので Web API のレスポンスまでになります。そのデータが適切でなければサーバーサイド(Web API)の担当です。役割責務が明確です。これは、とても大事なことです。

そんな JAMStack のサイトを作るには、Static Site Generator(SSG) を使います。代表的な Static Site Generator は [StaticGen | Top Open Source Static Site Generators](https://www.staticgen.com/) で知ることができます。利用している開発言語やフレームワークに合わせて選べます。

その中でも私たちがよく使っているのは以下です。
- [Nuxt.js](https://nuxtjs.org/) - Vue.js ベースで、アプリを作る時によく使います。とても使いやすく入門しやすいです。
- [Gridsome](https://gridsome.org/) - こちらも Vue.js ベース。完全に静的化して情報発信サイトを作るのに向いています。


### アーキテクチャ紹介
Nuxt.js で、アプリを作ったパターンになります。
![](/articles/assets/lulzneko/presentation/serverless-meetup-tokyo-12/03.png)

Nuxt.js を Static Site Generator として動作させて静的サイトを構築します。ホスティングは CloudFront/S3 になります。これにより CDN によるパフォーマンスとスケーリングのメリットを出します。

動的な要素としては、API Gateway/Lambda で JSON を返す REST な Web API を提供します。こちらはサーバーレスでシステムを構築する場合に使われるパターンと変わりはありません。私たちが REST API を作ることが多いだけで、ここは GraphQL などで API を作っても問題ありません。ここでのポイントはデータを返すことであり、HTML を生成して返すわけではないということです。

このような形にすることで、ウェブフロントもマネージドサービスだけで構築できます。サーバーレスのアーキテクチャを崩さずに済みます。


### JAMStack の可能性
JAMStack は、情報発信サイトをもっとも得意としますが、上記例のようにアプリのフレームとしても活用できます。アプリを Web API 呼出しのベースとすることで、さまざまなパターンで JAMStack は適用できます。

ただし唯一苦手な領域があります。**SEO 重視で変化の激しいコンテンツには向かない** です。
SEO というと検索エンジン向けの話になりますが、ここでは SEO ヘッダー、埋め込み用の情報として考えてください。(OGP と言ったほうがよいのかな。)

たとえば Twitter のようなサイトを想像してください。CGM(Consumer Generated Media) で、とてつもない量の情報が作られています。そして、それぞれの情報にはツイートの埋め込みができるように SEO ヘッダーが必要となります。SEO ヘッダーは HTML の必要があり JavaScript からは設定できません。

JAMStack は、HTML を作る場合は事前ビルドする必要があります。もし Twitter を JAMStack にすると、ツイートするたびにビルドして HTML を生成する必要があります。それではツイートしてから表示するまでに時間がかかってしまいますし、ツイート数が多すぎてビルドが回らないかもしれません。

このようなケースでは Server Side Rendering(SSR) が必要となります。
とはいえ、リアルタイムの膨大なソーシャルでなければ JAMStack でサイトを作るメリットは多くあるでしょう。


### まとめ
![](/articles/assets/lulzneko/presentation/jawsdays-2019/22.png)


## QA

### US で JAMStack Conf がありました、日本でも広がるでしょうか
はい。JAMStack は急速に広がっています。日本でも広がると思います。むしろ JAMStack という用語が広がっていないだけで、実際には JAMStack なサイトを作っているケースがたくさんあると考えています。用語が認知されるだけで広がるのではないでしょうか。
また Serverless WordPress の [Shifter](https://www.getshifter.io/japanese/) さんと、JAMStack や Static Site Generator を広めていくことをしたいとお話をしていています。これからも JAMStack について情報発信していきたいと思います。
JAMStack Conf は、私も参加したかったのですが個人では難しかったです。。。(あと、英語力もね)
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="http://jamstackconf.com" data-iframely-url="//cdn.iframe.ly/lDHEtZe?iframe=card-small"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>

### アトミックなデプロイは難しそうですが、どうしていますか
アトミックといわれると難しいイメージがありますが、私たちは AWS の CloudFormation に任せています。
現在のところ複数のビルドが走っていても、問題が発生していないので大丈夫でしょう。

### 認証はどうしますか、すべて HTML でしょうか
API の呼出しは制限されていませんし、認証後のプロフィール画面が外部サイトの情報の場合は API から取得した情報で構築できます。内部でしたら HTML へ静的化しておくとよいですが、HTML ファイル名ベースでの URL アクセスをされないような工夫が必要です。
JAMStack は「静的化」にフォーカスしているので、すべてを静的化(=HTML化)するように感じますが、ブラウザから JavaScript を使って Web API を呼び出すことを禁止していません。引用させていただいている資料 [The JAM Stack](https://speakerdeck.com/biilmann/the-jam-stack) に下図のスライドがあります。動的なシステムを Web API で呼び出して活用します。その際に必ずしも静的化する必要はありません。(どうしても静的化のメリットの話が中心になるので、誤解させてしまったかもしれません。すみません。説明の塩梅が難しい 😅)
![](/articles/assets/lulzneko/seminar/shifter/03-13.png)


他に確認したいことなどありましたら、ぜひ [[lulzneko | Twitter](https://twitter.com/lulzneko)] へ DM 他、気軽にいただけましたら幸いです。



----

本業でお世話になっているサーバーレスのコミュニティで発表させていただきました。
みなさんの集中力がすごく、ビシッとした視線に緊張しましたが、発表しきることができ良かったです。

サーバーレスというと、やはり FaaS などが話題の中心となりますが、ウェブサイトまで含めて「まるっとサーバーレス」として JAMStack への注目が高まるきっかけになったら幸いです。
