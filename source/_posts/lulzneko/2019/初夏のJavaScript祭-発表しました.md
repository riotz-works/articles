---
title: 初夏のJavaScript祭 in メンバーズキャリアにて「Gridsome で作る JAMStack なサーバーレス Web Front」の発表をしました
permalink: made-presentation-about-jamstack-with-gridsome-at-javascript-matsuri
alias: /2019/06/01/made-presentation-about-jamstack-with-gridsome-at-javascript-matsuri/index.html
date: 2019-06-01
author: lulzneko
categories: プレゼンテーション
tags:
- JAMStack
- Gridsome
- Nuxt.js
- Hexo
- AWS
---

2019年6月1日に開催された「初夏のJavaScript祭 in メンバーズキャリア」で『Gridsome で作る JAMStack なサーバーレス Web Front』と題して、JAMStack の世界と、Gridsome 導入について発表しました。その発表サマリーです。

![](/articles/assets/lulzneko/presentation/javascript-matsuri/javascript-matsuri.png)


## "JavaScript祭" 概要
発表したイベント [初夏のJavaScript祭 in メンバーズキャリア](https://javascript-fes.doorkeeper.jp/events/90894) は、Web デザイナー、フロントエンドエンジニア、サーバーサイドエンジニアまで JavaScript に関わるすべての人を対象にした JavaScript 好きのコミュニティー、[Javascript祭り](https://javascript-fes.doorkeeper.jp/) が開催するイベントです。初夏と秋の年２回開催されています。

会場は [メンバーズキャリア](https://www.wantedly.com/companies/memberscareer/post_articles/148238) さん。"[もう迷わない！五反田駅からメンバーズキャリア本社への推奨ルート | メンバーズキャリアNOW](https://www.wantedly.com/companies/memberscareer/post_articles/148238)" が公開されていて安心の道筋です。が、迷いました。ぐるりと一周して、サイトを見直して、ようやくたどり着きました。なんと。。。


## 発表資料
『[Gridsome で作る JAMStack な サーバーレス Web Front](https://riotz.works/slides/2019-javascript-matsuri)』の発表資料はこちらになります。(下記、スライド埋め込み)
<div class="slide"><iframe src="https://riotz.works/slides/2019-javascript-matsuri"></iframe></div>


## サマリー
発表の主旨は「JAMStack は JavaScript 祭り！、JavaScript から JAMStack を広めよう！！」です。

「JAMStack、好きだよね」と言われそうなほど JAMStack で発表させていただいていますが、前回の [Serverless Meetup Tokyo 12 にて「サーバーレスなウェブフロントを実現する JAMStack」について発表](https://riotz.works/articles/2019/05/27/made-presentation-about-jamstack-at-serverless-meetup-tokyo-12/) でふれました通り、「フロントも含めて、まるっとサーバーレスにしたい」のです。それを実現できるのが JAMStack です。


### JAMStack とは
では、JAMStack とは何でしょうか。
![](/articles/assets/lulzneko/presentation/javascript-matsuri/02.png)

「クライアントサイド JavaScript」「再利用可能な API」「構築済みのマークアップ」で構築されたサイトのことで、ざっくり言うと HTML に静的化されたされたサイトで、動的要素はブラウザ上の JavaScript から Web API を呼び出す形にしましょうというものになります。

これによって、すべてを CDN に配置できるので、素晴らしいパフォーマンスとスケーラビリティを手に入れることができます。そして HTML が CDN にあるだけなので攻撃対象を局所化できセキュリティを高めることができます。
また開発者の視点として、サーバー内のロジックとテンプレートエンジンが分離できます。これは表示部分とデータについて役割を明確に分離できます。役割が明確なのは開発において重要です。

そんな JAMStack なサイトを作るには「構築済みのマークアップ」を用意します。これは Static Site Generator(SSG) を使います。メジャーな Static Site Generator は [StaticGen | Top Open Source Static Site Generators](https://www.staticgen.com/) から探すことができます。さまざまな Static Site Generator があるので、開発言語やフレームワークに合わせて選べます。

その中でも私たちがよく使っているのは以下です。
- [Nuxt.js](https://nuxtjs.org/) - Vue.js ベース、アプリのフレームとして使いやすい
- [Gridsome](https://gridsome.org/) - 同じく Vue.js ベースで、完全に静的化した情報発信サイトに向いている

JAMStack の "**J**" は、JavaScript の "J"。そして **A**PI も、**M**arkdown も、JavaScript 作ることができるので、まさに JAMStack = JavaScript だったりします。そんなお話から、JavaScript 好きの皆さんと JAMStack で盛り上がりたいとのことで今回の発表に至りました。
![](/articles/assets/lulzneko/presentation/javascript-matsuri/01.png)


### アーキテクチャ紹介
Gridsome で、情報発信サイトを作るパターンです。
![](/articles/assets/lulzneko/presentation/javascript-matsuri/03.png)

ソースコードのセットにある Markdown ファイルを記事として取り込む情報発信サイト、ブログのようなサイトを作る場合のアーキテクチャになります。ホスティングは [AWS](https://aws.amazon.com/) の CloudFront/S3 を使うことでスケールさせています。これは完全に静的なサイトとして作られているので、他にも [Netlify](https://www.netlify.com/) や [GitHub Pages](https://pages.github.com/) などにもデプロイできます。

また、このアーキテクチャ図は Markdown ファイルをソースとしていますが、Gridsome はさまざまなソースを取り扱えるので、ここを変えていくとおもしろいシステムが作れます。Gridsome の Plugin は、こちら [Plugins - Gridsome](https://gridsome.org/plugins) で、`source` とついているものが入力を担う Plugin です。WordPress や、ヘッドレス CMS の Contentful、Google 系などさまざまな入力ソースに対応しています。

ここのでのポイントは、HTML とは異なるソースを Static Site Generator と組み合わせることで HTML 化してデプロイするという点です。あらかじめ HTML 化してしまうので、実行時に変換する必要がないので高速に動作させることができます。


### Gridsome で作る JAMStack サイト
アーキテクチャ図で出てきた Gridsome について、具体的な使い方を紹介します。

[Gridsome](https://gridsome.org/) は、Vue.js ベースのモダンで高速なウェブサイトを構築するためのフレームワークです。
とにかく高速で "builds ultra performance automatically", "almost perfect page speed scores by default." と公式で謳っているほどです。ウルトラ・パフォーマンスやパーフェクト・スコアと明言してくるあたりスゴいです！
![](/articles/assets/lulzneko/presentation/javascript-matsuri/04.png)

そして大きな特徴として GraphQL をデータ管理の中心として持ってきているところです。これにより、さまざまなデータソースを画一的に取り扱うことができます。いろいろな組み合わせを作ることができますし、異なるデータソースを簡単に組み合わせてページを構築できることが魅力です。
![](/articles/assets/lulzneko/presentation/javascript-matsuri/05.png)

サイト構築の開発も簡単です。
`npx @gridsome/cli create <project name>` で、スタータープロジェクトを使って初期構築。
プロジェクトのディレクトリで `yarn gridsome develop` でローカルサーバーを起動して、`http://localhost:8080/` へアクセス。あとは Vue.js のページやコンポーネントを追加していくだけです。ホットリロードが効いているのでコードを変更するたびにブラウザがリフレッシュされるので確認も容易です。

重要な GraphQL も Vue.js と組み合わせて簡単に扱えます。何よりも `http://localhost:8080/___explore` へアクセスすることで GraphQL のクエリーやスキーマを確認できるのが素晴らしいです。これによりデータソースの確認が容易に行えます。

下図は Instagram をソースとして取り扱う [zefman/gridsome-source-instagram](https://github.com/zefman/gridsome-source-instagram) と、その GraphQL のクエリーの例です。こんな感じで簡単にソースが取り扱えますし、クエリやスキーマの確認は `http://localhost:8080/___explore` から確認できます。
![](/articles/assets/lulzneko/presentation/javascript-matsuri/06.png)
※ 猫の写真は [アメショっす！](http://ameshossu.blog58.fc2.com/) さんの [Instagram](https://www.instagram.com/ameshossumomo/) をお借りしました。私の SNS アイコンの猫さんも [アメショっす！](http://ameshossu.blog58.fc2.com/) さんに許可をいただいて使わせて頂いているものになります。ありがとうございす 😸


### JAMStack の可能性
基本的には、どのようなサイトでも JAMStack で作れると考えています。
ただし、どうにもならないのが１つ、**SEO/OGP 重視で変化の激しいコンテンツ**。これは JAMStack では、どうにもならないです。SEO/OGP は HTML で `<meta>` タグを設定する必要があります。JAMStack では HTML を事前ビルドする必要があるので、変化が激しいとビルドが回り切らない。この場合は Server Side Rendaring(SSR) にしざるを得ないです。
とはいえ、すべてがすべて SEO/OGP 重視で変化が激しいわけではないので、まずは JAMStack で作ることを考えるとよいでしょう。


### まとめ
![](/articles/assets/lulzneko/presentation/javascript-matsuri/07.png)


## QA

### Nuxt.js でも SSG できるし、また他にもツールがあるが一番早いのは Gridsome でしょうか
「どれが最速か？」となると、ベンチマークしないと、ちょっとわからないです。すみません。

ただ、たとえば Gridsome はリンクのプリフェッチの機能があります。Nuxt.js は最近導入されました。ちょっと前であれば Gridsome のが速いといえたでしょう。しかしながら、よい技術はどのツールにも取り込まれていくので「最速が必須！」とかでなければ、スキルセットなどに合わせるとよいと思います。
それは Vue.js / React のベースをどちらにするかといったところなどがあるかと思います。


### GraphQL のサーバーは別途立てますか
Gridsome は GraphQL を内蔵しているので別途サーバーを用意する必要はありません。
`gridsome develop` を起動すると GraphQL サーバーも同時に起動するので `http://localhost:8080/___explore` へアクセスするだけで利用できます。また GraphQL サーバーを使うのはビルドするまでで、ビルドしたら HTML ができるので GraphQL のサーバーは不要です。


他に確認したいことなどありましたら、ぜひ[[lulzneko | Twitter](https://twitter.com/lulzneko)]へ DM 他、気軽にいただけましたら幸いです。



----

JavaScript のコミュニティで、お話させていただきました。

さまざまなツールやライブラリが登場する中で、JAMStack の話は緊張しました。とくに Gridsome の話は、コミッターさんの前の時間で発表という(それでいてブログメンティーの先輩でもある)💦

そんな大先輩 [もっと@GatsbyJSとNetlify推し(@mottox2)さん](https://twitter.com/mottox2) の [SSRを検討する際にSSGも検討しませんか？ / ssr or ssg - Speaker Deck](https://speakerdeck.com/mottox2/ssr-or-ssg) の発表。より具体的に SSG の紹介と、SSG で困ったときに "Gatsby => Next.js、Gridsome => Nuxt.js" と移行のパスも説明されています。
こちらのスライドも必見です！
<script async class="speakerdeck-embed" data-id="ba6e19183c2f4272a1ac73c65697dcc1" data-ratio="1.77777777777778" src="//speakerdeck.com/assets/embed.js"></script>

そして [Javascript祭り | Doorkeeper](https://javascript-fes.doorkeeper.jp/)。今回、はじめて参加させていただきましたが、登壇者さんの発表がスゴイのに加えて LT が熱い！とてもすごいイベントです。次回は秋。また参加したいです。
