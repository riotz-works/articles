---
title: JAWS DAYS 2019 にて頂いた QA まとめ
permalink: summary-of-qa-at-jawsdays2019
alias: /2019/02/24/summary-of-qa-at-jawsdays2019/index.html
date: 2019-02-24
author: lulzneko
categories: プレゼンテーション
tags:
- JAWS DAYS
- AWS Lambda
- DynamoDB
- Gridsome
- Hexo
- JAMstack
- Serverless Framework
---

![](/articles/assets/lulzneko/presentation/jawsdays-2019/01.png)

JAWS DAYS 2019 で『AWS x JAMstack で構築・運用するサーバーレスな Web Front』のお話をした後に頂きました QA をまとめます。

頂いた質問は要点のみを一般化して書いている部分がります。背景などが入っていないので若干わかりにくい部分がありますが、ご了承ください。

**シリーズの記事**
- [JAWS DAYS 2019 で AWS x JAMstack なサーバーレス Web Front について発表をしました](https://riotz.works/articles/lulzneko/2019/03/01/made-presentation-about-JAMstack-with-aws-at-jawsdays2019/)
- **JAWS DAYS 2019 で頂いた QA まとめ**（本記事）
- [発表者は、その日何をしていたのか - 発表の舞台裏 JAWS DAYS 2019 編](https://riotz.works/articles/lulzneko/2019/02/23/backstage-of-presentation-at-jawsdays2019/)


## 発表資料と Togetter
<div class="slide"><iframe src="https://riotz.works/slides/2019-jaws-days"></iframe></div>

[2019/02/23(土) JAWS DAYS 2019 <7> 15:10～ #jawsug #jawsdays - Togetter](https://togetter.com/li/1322142)
たくさんのツイートありがとうございます！！


## 1. DynamoDB の Attribute を変えたい場合に、どうやっているのか？
発表の QA タイムで [@yoshidashingo](https://twitter.com/yoshidashingo) さんに、頂いた質問になります。
[AWS Serverless Hero](https://aws.amazon.com/jp/developer/community/heroes/shingo-yoshida/) 直々の質問に緊張し、質問の要点を上手く汲み取れず、しっかり回答を返せませんでした。すみません。QA 対応力を磨かねば。
（ところで振り返ると、これって「この分野はあんまり詳しくないんですが」事案ではないでしょうか💦）

この QA については、本記事投稿時追記の形で、QA を振り返り整理します。

質問の主旨としては、このセクションのタイトルにした通り「DynamoDB の Attribute を変えたい場合に、どうやっているのか？」の質問と改めて整理しました。

DynamoDB でテーブル作成後に変えられないものは下記になります。（他にもテーブル名や暗号化タイプなどありますが質問のスコープとして）
- 主キーの構成と型 (パーティションキー、ソートキー)
- Attribute の名前と型（ただし Attribute の追加はいつでも自由にできる）

主キーはどうにもならないので、テーブルの作り直しになります。

Attribute の名前と型、こちらは変えたいとなるとどうにもなりませんが、Attribute は後から追加できるので新しく Attribute を足して、そちらを使う手も取れそうです。
しかし、既存データを新しい Attribute に移動するのか、またはプログラムでカバーするのかが出てきます。また混ざった状態が発生すると、後々困りそうです。

質問の前提となる事象がわかったら、もう少し踏み込めそうですがざっくりこんな感じの回答になります。

発表時に「あまり大きなものを作ってないので」と回答しましたが、マイクロサービスで作っているので、個々のシステムは小さく、また変化もあまりないので上手く想定できなかったのもあったかもしれません。
それでも Attribute はいつでも自由に足せるので、キレイではないものの逃げ方もあるような 🤔
「ディスカスしましょう」とおっしゃっていたので、このあたりの逃げ方含め議論したかったのかな。

※ Serverless Framework との質問でしたが、DynamoDB の定義は Serverless Framework の構成ファイル `serverless.yml` に書きます。しかし CloudFormation のパートになるので、Serverless Framework 固有より、CloudFormation での管理になることと、むしろ DynamoDB そのものの話になるかと思い Serverless Framework は外しました。

参考情報
- [Amazon DynamoDB（フルマネージド型 No SQL データベース） ｜AWS](https://aws.amazon.com/jp/dynamodb/)
- [DynamoDB のベストプラクティス - Amazon DynamoDB](https://docs.aws.amazon.com/ja_jp/amazondynamodb/latest/developerguide/best-practices.html)


## 2. WordPress の静的化に JAMstack は有効なの？
![](/articles/assets/lulzneko/presentation/jawsdays-2019/10.png)
何件か質問をいただきました。

WordPress へ投降者以外は直接アクセスできないようにしたり、投稿通知をするための仕組みを作ってあげる必要はありますが、WordPress を使った既存の運用は残したまま、まったく新しいサイトへ生まれ変わらせることができます。

それにより、セキュリティ問題やバージョンアップ対応の負担を減らすことはできますが、この場合でもセキュリティ問題は内部からの攻撃は避けられません。脅威はインターネット側だけにあるとは限らないこと注意が必要です。

そして静的化するのでパフォーマンスは、とてつもなく向上します。
まさに今日お話ししました、高パフォーマンス、セキュリティただし局所化、スケーリング、運用の軽減が手に入ります。

発表資料では [Gridsome](https://gridsome.org/) を使ったアーキテクチャ図になっていますが、まだまだ開発中なので [GatsbyJS](https://www.gatsbyjs.org/) を使うのが現実的です。

またカスタム API を持たず、WordPress のコンテンツを新しいサイトとして配信するだけなら [Netlify](https://www.netlify.com/) に配置するのも手です。

GatsbyJS で WordPress 移行は、多くの情報があるので取り組みやすいです。
私も機会があったらブログとかを書きます。


## 3. SSR(Server Side Rendering) との違いは何か？
SSR はブラウザからのリクエストを受けて、サーバー側で HTML を生成してレスポンスする形になります。
これはリクエストを受けてから処理を行うので静的サイトを作る SSG(Static Site Generator) より、以下の点で不利です。
- パフォーマンス、これは HTML 生成処理がある、また DB アクセスすることもあるかもしれません
- セキュリティ、仮に HTML だけを返すとしても AP サーバが存在するので攻撃対象になります
- スケーラビリティ、AP サーバをスケールするのは大変です

ただし、開発者フレンドリーは、JAMstack としては良いとしていますが、一概に言えないかもしれません。Web API から取得したデータを JavaScript で HTML を変化させるのは開発者によっては苦痛かもしれない。
私は [Vue.js](https://vuejs.org/) や [Nuxt.js](https://nuxtjs.org/) が気に入っていて、JavaScript で値を設定するのは、楽しいです。

![](/articles/assets/lulzneko/presentation/jawsdays-2019/11.png)
そして SEO の観点でお話ししました通り、SEO のためのヘッダー出力は HTML である必要があります。
自分がコンテンツの発信量をコントロールできている場合は SSG で問題ないのですが、リアルタイムで大量のコンテンツが変化していくものには SSR が必要となります。

SSG では CI などを使ってビルドを回す必要があります。これは遅い処理になります。リアルタイムでユーザーを待たせることができないくらい時間がかかり、また大量に受け付けることができないものになります。

この場面においては SSR が必要となります。
それ以外で SSR 必須、または SSR が良いシーンは今のところ浮かばないです。もっと考えてみます。


## 4. SPA(Single Page Application) はどうなるのか？
これは、SSG が生成した HTML の形式になるので、ツールによります。

ブログツールの [Hexo](https://hexo.io/) は SSG で MPA(Multiple Page Application) です。

私たちが作ったラップ・バトルは [Nuxt.js](https://nuxtjs.org/) で SSG ＆ SPA で動かしています。
[Nuxt.js](https://nuxtjs.org/) は SSR もできるので、設定で指定します。

ツールの考え方とかもあるので一概に何とも言えないところがあります。
ただし高パフォーマンスの観点で考えると、ページの先読みやキャッシュなどが重要になっきます。そして、これらも SSG がどのような機能を持っているかによります。

その点で、私たちは [Gridsome](https://gridsome.org/) が気に入っています。
まだ開発中なので、普通に使うには [GatsbyJS](https://www.gatsbyjs.org/) が良いかもしれません。
また、アプリ開発用途でしたら、[Nuxt.js](https://nuxtjs.org/) をオススメします。


## 5. JAMstack でも Lambda や DynamoDB は必須なのか？
![](/articles/assets/lulzneko/presentation/jawsdays-2019/12.png)
"俺の満漢全席" を想定されての質問と考えます。
こちらは JAMstack で作ったアプリ全体像としてのアーキテクチャ図になります。

JAMstack の要件はざっくり言うと「HTML を事前ビルドして CDN に置くこと」なので、Lambda や DynamoDB は JAMstack とは関係ないものになります。

![](/articles/assets/lulzneko/presentation/jawsdays-2019/13.png)
JAMstack の要件だけの図としては "満漢ミニ席" または "WordPress リプレース席" をイメージしていただけるとよいです。
"満漢ミニ席" では、ブログなどの情報発信サイトをイメージしていて、記事を Markdown などのファイルに書き、それを HTML として出力、CDN へ配置する形になっています。
ここには Lambda や DynamoDB は登場しません。純粋にウェブサイトだけになります。


## 6. "俺の満漢全席" で Lambda/DynamoDB は EC2/RDS にしてもよいか？
![](/articles/assets/lulzneko/presentation/jawsdays-2019/12.png)
Lambda/DynamoDB を EC2/RDS へ変えても構いません。JAMstack の要件としては「再利用可能な API」なので、Web API 提供できれば大丈夫です。

ですが、せっかくなのでサーバーレスで作ってほしいです。

JAMstack は SSG を使うことで、HTML の生成を事前に行います。
そして CDN に配置するだけで運用するので、言わばフロントエンドのサーバーレスともいえるでしょう。

Web API をサーバーレスで作ってきても、フロントエンドで SSR するからインスタンスが欲しいとなって、全体でサーバーレスになれなかったケースなどもあり、フロントエンドのサーバーレス化も重要だと思ったことがあります。

CDN に配置するだけのことをサーバーレスと言うのは、ちょっと苦しいですが、全体をサーバーレスで作りたい撮った時に「JAMstack で、フロントもサーバーレスで」って言いたいのもあります。

最後のまとめ「名前が付き、認識されることで、伝わる」はまさにそんな思いからになります。
むしろ「JAMstack で、フロントもサーバーレスで」って書いたほうが良かったですね。



----

質問いただき、ありがとうございました。

QA や議論ができ、とても楽しかったです。そしてお話しいただくことで気付きを得たり、考えの整理にもつながり、とても勉強になりました。

できる限り思い出して書きましたが、もし入ってなかったり、別途気になることなどがありましたら Twitter [@lulzneko](https://twitter.com/lulzneko) へ DM やメンションいただけたら幸いです。
