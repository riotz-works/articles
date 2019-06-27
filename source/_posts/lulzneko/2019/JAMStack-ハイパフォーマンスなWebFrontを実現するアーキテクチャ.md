---
title: JAMStack、それはハイパフォーマンスなウェブフロントを実現するアーキテクチャ
permalink: jamstack-an-architecture-to-realize-fine-web-front
alias: /2019/01/23/jamstack-an-architecture-to-realize-fine-web-front/index.html
date: 2019-01-23
author: lulzneko
categories: フロントエンド
tags:
- JAMStack
- Gridsome
- Nuxt.js
- Hexo
---

![](/articles/assets/lulzneko/serverless/jamstack.jpg)

"JAMStack" と呼ばれる新しいウェブの開発手法で「より良いパフォーマンス」「より高いセキュリティ」「より安価で簡単なスケーリング」「より良質な開発者エクスペリエンス」を提供するアーキテクチャが注目を集め始めています。

JAMStack とは何か、どのように使い、どのようなメリットがあるのか、公式サイトの情報をもとに読み解きます。


## JAMStack の定義
![](/articles/assets/lulzneko/serverless/jamstack/01.png)
オフィシャル・サイト [https://jamstack.org](https://jamstack.org) によると、以下の要素に基づく最新のウェブ開発アーキテクチャとされています。
- クライアントサイドのJavaScript
- 再利用可能なWeb API
- 構築済みの Markup (HTML)

HTML に JavaScript そして Web API なので、ウェブフロントの開発をする場合によく使うであろう技術と言えるでしょう。

ここでは動的な要素をサーバーサイドで組み込んだ HTML などをレスポンスするのではなく、クライアントサイドのJavaScript から Web API を通じて取得/更新し、クライアントサイドでダイナミックに描画する方式、いわゆる REST API などを使ったウェブフロントの作り方を指しています。

そして Markup (HTML) の部分がポイントなのですが **"prebuilt at deploy time"** 、デプロイ時に事前ビルドされているとしています。そして多くの場合、サイト・ジェネレーターなどを使って作ります。（ツールの紹介は[後述](#サイト・ジェネレーター)）

![](/articles/assets/lulzneko/serverless/jamstack/02.png)
[The JAM Stack](https://speakerdeck.com/biilmann/the-jam-stack) の [44ページ目](https://speakerdeck.com/biilmann/the-jam-stack?slide=44) のスライドにある、この図が JAMStack のアーキテクチャをシンプルに表現しています。
Git に Push されたコードから、ビルドツール(サイト・ジェネレーターなど)が動作し、Web API などのデータと組み合わせて、構築済みの HTML を生成、CDN へ配置して配信します。


## なぜ JAMStack なのか
なぜ JAMStack、とくに構築済みの HTML をデプロイするのか。

公式サイトでは、大きく４つ「より良いパフォーマンス」「より高いセキュリティ」「より安価で簡単なスケーリング」「より良質な開発者エクスペリエンス」のメリットを上げています。

### より良いパフォーマンス
最初のページを高速に表示するためには、HTML を事前に構築しておき CDN で配信することが最適です。
構築済みでない場合、高速に配信できる CDN を介することができず、また HTML を生成するための処理を待つことにもなります。

### より高いセキュリティ
サーバーサイドの処理をマイクロサービスとして API に抽象化されているため攻撃の対象となる部分を小さくできます。
また、事前に構築済みの HTML をデプロイしているため、HTML を処理するためのプロセスはオフラインにできることもメリットといえるでしょう。

### より安価で簡単なスケーリング
デプロイするものは構築済みの HTML となるため CDN に配置することができ、スケーリングすることが容易になります。
CDN が安価かは悩ましい部分はありますが「Web サーバ」と「AP サーバ」をスケールさせるために用意することを考えると、CDN だけでよいので簡単とはいえるでしょう。

### より良質な開発者エクスペリエンス
疎結合にすることで、よりターゲットを絞った開発とデバッグを可能とします。
構成を簡素化できることは開発者の精神衛生上よいといえるでしょう。


このようにロジックを Web API として分離し、フロントエンドを構築済みの HTML としておくことで、多くのメリットを享受できます。


## ベストプラクティス
JAMStack なプロジェクトを構築する際に、いくつかのベストプラクティスを活用することでスタックのメリットを最大限に発揮できます。

### CDN に全部配置されている
JAMStack なプロジェクトは、サーバサイドのコードに依存しないため、単一のサーバではなく分散して配置できます。
CDN から直接サービス提供することでスピードとパフォーマンスを発揮できます。アプリがネットワークにエッジまで配置されることで、より良いユーザー体験になります。

### すべてが Git に存在する
JAMStack なプロジェクトは、誰もが `git clone` でき、必要な依存関係を `npm install` などのような標準的な手順でインストールし、プロジェクトをローカルで実行する準備ができているはずです。複製するデータベースも、複雑なインストールもありません。
これによってコントリビューターの負担を減らし、ステージングとテストのワークフローも簡略化されます。

### モダンなビルドツール
モダンなビルドツールの世界によるアドバンテージを受けましょう。
それは動きの速い世界でジャングル(無法地帯)を志向するかのようですが、未来のブラウザを待たずに最新のウェブ標準を使うことができるようになります。
それは現時点では Babel、PostCSS、webpack、そしてその仲間です。

### 自動ビルド
JAMStack のマークアップ(HTML) は、事前ビルドされているため、コンテンツの変更はビルドを実行するまで反映されません。このプロセスを自動化するとフラストレーションを軽減できます。
これを webhook で行うことも、自動的にパブリッシュするプラットフォームなどを使うこともできます。

### アトミックなデプロイ
JAMstack なプロジェクトが非常に大きくなるにつれて、新しい変更のために数百ものファイルを再デプロイする必要があるかもしれません。 これらのファイルを一度にアップロードすると、プロセスの完了までの間に不整合を生じる可能性があります。 これを避けるに、すべてのファイルがアップロードされるまで変更が行われない「アトミックデプロイ」を実行できるシステムを使用します。

### キャッシュの即時無効化
ビルドからデプロイへのサイクルが定期的に行われるようになったら、デプロイの実行が反映されるようにします。CDN は側にキャッシュを消去できます。


## サイト・ジェネレーター
JAMStack のメリットはわかったとして、それでサイトを作るには「構築済みの HTML」が必要となってきます。
この構築済みの HTML は手組みの HTML を用意することではなくて、サイト・ジェネレーターを使うとされています。

主なサイト・ジェネレーターは、こちら [StaticGen](https://www.staticgen.com/) に列挙されています。
![](/articles/assets/lulzneko/serverless/jamstack/03.png)

有名どころとしては GitHub Pages でも使われている [Jekyll](https://jekyllrb.com/) などがあり、[Hugo](https://gohugo.io/) や [Hexo](https://hexo.io/) といったブログ構築用の静的ジェネレーターや [Next](https://nextjs.org/), [Nuxt](https://nuxtjs.org/) といった [React](https://reactjs.org/) や [Vue.js](https://jp.vuejs.org/) のアプリ・フレームワークなどもあります。

JAMStack のコンテキストでは [Gatsby](https://www.gatsbyjs.org/)、[VuePress](https://vuepress.vuejs.org/)、[Gridsome](https://gridsome.org/) などを目にすることが多いようにも感じます。


## ヘッドレス CMS
これはとくに JAMStack の要件ではないのですが、JAMStack なブログや情報発信サイトを作る際に使われるものです。

WordPress などの CMS はコンテンツを配信するための部分が含まれ、そのままホスティングすると JAMStack なサイトにはならないので、別の仕組みが必要となりますが、WordPress などのように優れたコンテンツを管理するための仕組みが欲しくなります。
そうしたニーズに応えるのが、このヘッドレス CMS です。最低限の UI でコンテンツを管理することができ、作成したコンテンツを API 経由などで提供し、上記サイト・ジェネレーターなどと組み合わせてサイトを作ります。

主なヘッドレス CMS は、こちら [headlesCMS](https://headlesscms.org/) に列挙されています。
![](/articles/assets/lulzneko/serverless/jamstack/04.png)

前半に Open source なヘッドレス CMS が並び、後半に Closed source があります。
またサービスとして提供されているものは、こちらの Closed source に含まれます。たとえば最近よく目にする [Contentful](https://www.contentful.com/) は Closed source になります。

サイト・ジェネレーターによっては、WordPress の REST API からコンテンツをとってきたり、ローカルの Markdown ファイルを使うこともできるので、サイト・ジェネレーターの機能と管理方法に合わせて選択します。


## 参考情報
- [JAMstack | JavaScript, APIs, and Markup](https://jamstack.org/)
- [The JAM Stack](https://speakerdeck.com/biilmann/the-jam-stack)
- [StaticGen](https://www.staticgen.com/)
- [headlesCMS](https://headlesscms.org/)
- [JAMSTACKってなんなのか問題に立ち向かう](https://slides.com/masayakazama/what-is-jamstack)


----
実際のところ、Web API ベースなアプリ開発をしていると、意識はしてなくとも JAMStack なウェブフロントを作っているなぁというケースもあったりします。

さっくり言ってしまうと、そんな Web API ベースなアプリでウェブフロントを構築済みの HTML として CDN へ配置するものに、JAMStack の名前がついた感じになります。

名前がついたことで認識され、認識されることで広まる流れになっていくのではないでしょうか。
