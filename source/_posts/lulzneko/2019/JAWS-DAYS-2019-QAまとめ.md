---
title: JAWS DAYS 2019 で 頂いた QA まとめ
permalink: summary-of-qa-at-jawsdays2019
date: 2019-02-24
author: lulzneko
categories: プレゼンテーション
tags:
- JAWS DAYS
- AWS Lambda
- DynamoDB
- Gridsome
- Hexo
- JAMStack
- Serverless Framework
---

![](/articles/assets/lulzneko/presentation/jawsdays-2019/01.png)

JAWS DAYS 2019 で『AWS x JAMStack で構築・運用する サーバーレスな Web Front』の お話をした後に頂きました QA を まとめます。

頂いた質問は要点のみを一般化して書いている部分がります。背景などが入っていないので若干わかりにくい部分がありますが、ご了承ください。

**シリーズの記事**
- [JAWS DAYS 2019 で AWS x JAMStack な サーバーレス Web Front について発表をしました](/articles/2019/03/01/made-presentation-about-jamstack-with-aws-at-jawsdays2019/)
- **JAWS DAYS 2019 で 頂いた QA まとめ**（本記事）
- [発表者は、その日何をしていたのか - 発表の舞台裏 JAWS DAYS 2019 編](/articles/2019/02/23/backstage-of-presentation-at-jawsdays2019/)


## 発表資料 と Togetter
<div class="slide"><iframe src="https://riotz.works/slides/?2019-jaws-days"></iframe></div>

[2019/02/23(土) JAWS DAYS 2019 <7> 15:10～ #jawsug #jawsdays - Togetter](https://togetter.com/li/1322142)
沢山のツイートありがとうございます！！


## 1. DynamoDB の Attribute を変えたい場合に、どうやっているのか？
発表 の QA タイムで [@yoshidashingo](https://twitter.com/yoshidashingo) さん に、頂いた質問になります。
[AWS Serverless Hero](https://aws.amazon.com/jp/developer/community/heroes/shingo-yoshida/) 直々の質問に緊張し、質問の要点を上手く汲み取れず、しっかり回答を返せませんでした。すみません。QA 対応力を磨かねば。
（ところで 振り返ると、これって「この分野はあんまり詳しくないんですが」事案ではないでしょうか💦）

この QA については、本記事投稿時追記という形で、QA を 振り返り整理したいと思います。

質問の主旨としては、このセクションのタイトルにした通り「DynamoDB の Attribute を変えたい場合に、どうやっているのか？」という質問と改めて整理しました。

DynamoDB で テーブル作成後に変えられないものは下記になります。（他にもテーブル名や暗号化タイプなどありますが質問のスコープとして）
- 主キーの構成と型 (パーティションキー、ソートキー)
- Attribute の 名前 と 型（ただし Attribute の 追加 は いつでも自由にできる）

主キーはどうにもならないので、テーブルの作り直しになります。

Attribute の 名前 と 型、こちらは変えたいとなるとどうにもなりませんが、Attribute は 後から追加できるので新しく Attribute を 足して、そちらを使うという手も取れそうです。
しかし、既存データを新しい Attribute に 移動するのか、またはプログラムでカバーするのかという点が出てきますし、混ざった状態が発生すると、後々困りそうです。

質問の前提となる事象がわかったら、もう少し踏み込めそうですが ざっくりこんな感じの回答になります。

発表時に「あまり大きなものを作ってないので」と 回答しましたが、マイクロサービスで作っているので、個々のシステムは小さく、また変化もあまりないので上手く想定できなかったというのもあったかもしれません。
それでも Attribute は いつでも自由に足せるので、キレイではないものの逃げ方もあるような 🤔
「ディスカスしましょう」と おっしゃっていたので、このあたりの逃げ方含め議論したかったのかな。

※ Serverless Framework との質問でしたが、DynamoDB の 定義は Serverless Framework の 構成ファイル `serverless.yml` に 書きますが、CloudFormation の パートになるので、Serverless Framework 固有のというより、CloudFormation での管理になることと、むしろ DynamoDB そのものの話になるかと思い Serverless Framework は 外しました。

参考情報
- [Amazon DynamoDB（フルマネージド型 No SQL データベース） ｜AWS](https://aws.amazon.com/jp/dynamodb/)
- [DynamoDB のベストプラクティス - Amazon DynamoDB](https://docs.aws.amazon.com/ja_jp/amazondynamodb/latest/developerguide/best-practices.html)


## 2. WordPress の 静的化 に JAMStack は 有効なの？
![](/articles/assets/lulzneko/presentation/jawsdays-2019/10.png)
何件か質問をいただきました。

WordPress へ 投降者以外は直接アクセスできないようにしたり、投稿通知をするための仕組みを作ってあげる必要はありますが、WordPress を使った既存の運用は残したまま、全く新しいサイトへ生まれ変わらせることができます。

それにより、セキュリティ問題やバージョンアップ対応の負担を減らすことはできますが、この場合でもセキュリティ問題は内部からの攻撃は避けられません。脅威はインターネット側だけにあるとは限らないこと注意が必要です。

そして静的化するのでパフォーマンスは、とてつもなく向上します。
まさに今日お話ししました、高パフォーマンス、セキュリティ ただし局所化、スケーリング、運用の軽減が手に入ります。

ただし、コメント機能が使えなくなります。静的サイトになってしまうので仕方がないものになります。
私たちは [Disqus](https://disqus.com/) というサービスを使ってコメント欄を設置しています。広告が出たりするので、もしかしたらサイトのブランディングと合わないかもしれません。

発表資料では [Gridsome](https://gridsome.org/) を 使ったアーキテクチャ図になっていますが、まだまだ開発中なので [GatsbyJS](https://www.gatsbyjs.org/) を 使うのが現実的だと思います。

またカスタム API を 持たず、WordPress の コンテンツを新しいサイトとして配信するだけなら [Netlify](https://www.netlify.com/) に 配置するのも手です。

GatsbyJS で WordPress 移行は、多くの情報があるので取り組みやすいと思います。
私も機会があったらブログとかを書きたいと思います。


## 3. SSR(Server Side Rendering) との違いは何か？
SSR は ブラウザからのリクエストを受けて、サーバー側で HTML を 生成してレスポンスする形になります。
これはリクエストを受けてから処理を行うので静的サイトを作る SSG(Static Site Generator) より、以下の点で不利です。
- パフォーマンス、これは HTML 生成処理がある、また DB アクセスすることもあるかもしれません
- セキュリティ、仮に HTML だけを返すとしても AP サーバが存在するので攻撃対象になります
- スケーラビリティ、AP サーバをスケールするのは大変です

ただし、開発者フレンドリーは、JAMStack としては良いとしていますが、一概に言えないかもしれません。Web API から取得したデータを JavaScript で HTML を 変化させるのは開発者によっては苦痛かもしれない。
私は [Vue.js](https://vuejs.org/) や [Nuxt.js](https://nuxtjs.org/) が 気に入っていて、JavaScript で 値を設定するのは、楽しいです。

![](/articles/assets/lulzneko/presentation/jawsdays-2019/11.png)
そして SEO の 観点でお話ししました通り、SEO のための ヘッダー出力は HTML である必要があります。
自分がコンテンツの発信量をコントロールできている場合は SSG で 問題ないのですが、リアルタイムで大量のコンテンツが変化していくものには SSR が 必要となります。

SSG では CI などを使ってビルドを回す必要があります。これは遅い処理になります。リアルタイムでユーザーを待たせることができない時間がかかり、また大量に受け付けることができないものになります。

この場面においては SSR が 必要となります。
それ以外で SSR 必須、または SSR が 良いというシーンは今のところ浮かばないです。もっと考えてみたいと思います。


## 4. SPA(Single Page Application) は どうなるのか？
これは、SSG が 生成した HTML の 形式になるので、ツールによります。

ブログツールの [Hexo](https://hexo.io/) は SSG で MPA(Multiple Page Application) です。

私たちが作ったラップ・バトル は [Nuxt.js](https://nuxtjs.org/) で SSG ＆ SPA で 動かしています。
[Nuxt.js](https://nuxtjs.org/) は SSR も できるので、設定で指定します。

ツールの考え方とかもあるので一概に何とも言えないところがあると思います。
ただし高パフォーマンスという観点で考えると、ページの先読み や キャッシュ などが重要になってくると思います。そして、これらも SSG が どのような機能を持っているかによります。

その点で、私たちは [Gridsome](https://gridsome.org/) が 気に入っています。
まだ開発中なので、普通に使うには [GatsbyJS](https://www.gatsbyjs.org/) が 良いかもしれません。
また、アプリ開発用途でしたら、[Nuxt.js](https://nuxtjs.org/) を お勧めします。


## 5. JAMstack でも Lambda や DynamoDB は 必須なのか？
![](/articles/assets/lulzneko/presentation/jawsdays-2019/12.png)
"俺の満漢全席" を 想定されての質問かと思います。
こちらは JAMStack で 作ったアプリ全体像としてのアーキテクチャ図になります。

JAMStack の 要件はざっくり言うと「HTML を 事前ビルドして CDN に 置くこと」なので、Lambda や DynamoDB は JAMStack とは 関係ないものになります。

![](/articles/assets/lulzneko/presentation/jawsdays-2019/13.png)
JAMStack の 要件だけの図としては "満漢ミニ席" または "WordPress リプレース席" を イメージしていただけるとよいです。
"満漢ミニ席" では、ブログなどの情報発信サイトをイメージしていて、記事を Markdown などのファイルに書き、それを HTML として出力、CDN に 配置するという形になっています。
ここには Lambda や DynamoDB は 登場しません。純粋にウェブサイトだけになります。


## 6. "俺の満漢全席" で Lambda/DynamoDB は EC2/RDS に してもよいか？
![](/articles/assets/lulzneko/presentation/jawsdays-2019/12.png)
Lambda/DynamoDB を EC2/RDS へ 変えても構いません。JAMStack の 要件としては「再利用可能な API」なので、Web API 提供できれば大丈夫です。

ですが、せっかくなのでサーバーレスで作ってほしいです。

JAMStack は SSG を 使うことで、HTML の 生成を事前に行うようにします。
そして CDN に 配置するだけで運用するので、言わばフロントエンドのサーバーレスともいえるでしょう。

Web API を サーバーレスで作ってきても、フロントエンド で SSR するからインスタンスが欲しいとなって、全体でサーバーレスになれなかったケースなどもあり、フロントエンドのサーバーレス化も重要だと思ったことがあります。

CDN に 配置するだけのことをサーバーレスというのは、ちょっと苦しいですが、全体をサーバーレスで作りたい撮った時に「JAMStack で、フロントもサーバーレスで」って言いたいというのもあります。

最後のまとめ「名前が付き、認識されることで、伝わる」は まさにそんな思いからになります。
むしろ「JAMStack で、フロントもサーバーレスで」って書いたほうが良かったですね。



----

質問いただき、ありがとうございました。

QA や 議論 が でき、とても楽しかったです。そして お話しいただくことで気付きを得たり、考えの整理にもつながり、とても勉強になりました。

できる限り思い出して書きましたが、もし入ってなかったり、別途気になることなどがありましたら Twitter [@lulzneko](https://twitter.com/lulzneko) へ DM や メンションいただけたら幸いです。
