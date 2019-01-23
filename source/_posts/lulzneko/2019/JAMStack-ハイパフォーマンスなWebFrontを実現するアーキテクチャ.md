---
title: JAMStack、それは ハイパフォーマンスなウェブフロントを実現するアーキテクチャ
permalink: jamstack-an-architecture-to-realize-fine-web-front
date: 2019-01-23
author: lulzneko
categories: サーバーレス
tags:
- JAMStack
---

![](/articles/assets/lulzneko/serverless/serverless.jpg)

"JAMStack" と 呼ばれる、新しい ウェブの開発手法で、より良いパフォーマンス、より高いセキュリティ、より安価で簡単なスケーリング、より良質な開発者エクスペリエンス を 提供するアーキテクチャ が 注目を集め始めています。

JAMStack とは何か、どのように使い、どのようなメリットがあるのか、公式サイトの情報をもとに読み解きます。


## JAMStack の 定義
![](/articles/assets/lulzneko/serverless/jamstack/01.png)
オフィシャル・サイト [https://jamstack.org](https://jamstack.org) によると、以下の要素に基づく最新のウェブ開発アーキテクチャとされています。
- クライアントサイド の JavaScript
- 再利用可能 な Web API
- 構築済みの Markup (HTML)

HTML に JavaScript そして Web API なので、ウェブフロント の 開発をする場合によく使うであろう技術と言えるでしょう。

ここでは、動的な要素をサーバーサイドで 組み込んだ HTML などをレスポンスするのではなく、クライアントサイド の JavaScript から Web API を 通じて取得/更新し、クライアントサイドでダイナミックに描画する方式、いわゆる REST API などを使ったウェブフロントの作り方を指しています。

そして Markup (HTML) の 部分がポイントなのですが **"prebuilt at deploy time"** 、デプロイ時に事前ビルドされているとしています。そして多くの場合、サイト・ジェネレータなどを使って作ります。（ツールの紹介は[後述](#サイト・ジェネレータ)）

![](/articles/assets/lulzneko/serverless/jamstack/02.png)
[The JAM Stack](https://speakerdeck.com/biilmann/the-jam-stack) の [44ページ目](https://speakerdeck.com/biilmann/the-jam-stack?slide=44) の スライドにある、この図が JAMStack の アーキテクチャをシンプルに表現しています。
Git に Push されたコードから、ビルドツール(サイト・ジェネレータなど)が動作し、Web API などのデータと組み合わせて、構築済みの HTML を生成、CDN へ 配置して配信します。


## なぜ JAMStack なのか
なぜ JAMStack、特に構築済みの HTML を デプロイするのか。

公式サイトでは、大きく４つ「より良いパフォーマンス」「より高いセキュリティ」「より安価で簡単なスケーリング」「より良質な開発者エクスペリエンス」の メリットを上げています。

### より良いパフォーマンス
最初のページを高速に表示するためには、HTML を 事前に構築しておき CDN で 配信することが最適です。
構築済みでない場合、高速に配信できる CDN を 介することができず、また HTML を 生成するための処理を待つことにもなります。

### より高いセキュリティ
サーバーサイドの処理をマイクロサービスとして API に 抽象化されているため攻撃の対象となる部分を小さくできます。
また、事前に構築済みの HTML を デプロイしているため、HTML を 処理するためのプロセスはオフラインにできることもメリットといえるでしょう。

### より安価で簡単なスケーリング
デプロイするものは 構築済みの HTML となるため CDN に 配置することができ、スケーリングすることが容易になります。
CDN が 安価かというと悩ましい部分はありますが、Web サーバ と AP サーバ を スケールさせるために用意することを考えると、CDN だけでよいので簡単とはいえるでしょう。

### より良質な開発者エクスペリエンス
疎結合にすることで、よりターゲットを絞った開発とデバッグを可能とします。
構成を簡素化できることは開発者の精神衛生上よいといえるでしょう。


このようにロジックを Web API として分離し、フロントエンドを構築済みの HTML としておくことで、多くのメリットを享受することができます。


## ベストプラクティス
JAMStack な プロジェクトを構築する際に、いくつかのベストプラクティスを活用することでスタックのメリットを最大限に発揮できます。

### CDN に 全部配置されている
JAMStack な プロジェクトは、サーバサイドのコードに依存しないため、単一のサーバではなく分散して配置することができます。
CDN から 直接サービス提供することでスピードとパフォーマンスを発揮できます。アプリがネットワークにエッジまで配置されることで、より良いユーザー体験になります。

### 全てが Git に 存在する
JAMStack な プロジェクトは、誰もが `git clone` でき、必要な依存関係を `npm install` などのような標準的な手順でインストールし、プロジェクトをローカルで実行する準備ができているはずです。複製するデータベースも、複雑なインストールもありません。
これによってコントリビュータの負担を減らし、ステージングとテストのワークフローも簡略化されます。

### モダンなビルドツール
モダンなビルドツールの世界によるアドバンテージを受けましょう。
それは動きの速い世界でジャングル(無法地帯)を志向するかのようですが、未来のブラウザを待たずに最新のウェブ標準を使うことができるようになります。
それは現時点では Babel、PostCSS、Webpack、そして その仲間です。

### 自動ビルド
JAMStack の マークアップ(HTML) は、事前ビルドされているため、コンテンツの変更はビルドを実行するまで反映されません。このプロセスを自動化するとフラストレーションを軽減できます。
これを webhook で 行うことも、自動的にパブリッシュするプラットフォームなどを使うこともできます。

### アトミックなデプロイ
JAMstack な プロジェクトが非常に大きくなるにつれて、新しい変更のために数百ものファイルを再デプロイする必要があるかもしれません。 これらのファイルを一度にアップロードすると、プロセスの完了までの間に不整合が生じる可能性があります。 これを避けるに、全てのファイルがアップロードされるまで変更が行われない「アトミックデプロイ」を実行できるシステムを使用します。

### キャッシュの即時無効化
ビルドからデプロイへのサイクルが定期的に行われるようになったら、デプロイの実行 が 実際に反映されるようにします。CDN は 側にキャッシュを消去することができます。


## サイト・ジェネレータ
JAMStack の メリットはわかったとして、それでサイトを作るには「構築済み の HTML」が 必要となってきます。
この構築済みの HTML は 手組み の HTML を 用意することではなくて、サイト・ジェネレータを使うとされています。

主なサイト・ジェネレータは、こちら [StaticGen](https://www.staticgen.com/) に 列挙されています。
![](/articles/assets/lulzneko/serverless/jamstack/03.png)

有名どころとしては GitHub Pages でも使われている [Jekyll](https://jekyllrb.com/) などがあり、[Hugo](https://gohugo.io/) や [Hexo](https://hexo.io/) といったブログ構築用の静的ジェネレータ や [Next](https://nextjs.org/), [Nuxt](https://nuxtjs.org/) といった [React](https://reactjs.org/) や [Vue.js](https://jp.vuejs.org/index.html) の アプリ・フレームワークなどもあります。

JAMStack というコンテキストでは [Gatsby](https://www.gatsbyjs.org/)、[VuePress](https://vuepress.vuejs.org/)、[Gridsome](https://gridsome.org/) などを目にすることが多いようにも感じます。


## ヘッドレス CMS
これは特に JAMStack の 要件というわけではないのですが、JAMStack な ブログ や 情報発信サイトを作る際に使われるものです。

WordPress などの CMS は コンテンツを配信するための部分が含まれ、そのままホスティングすると JAMStack な サイトにはならないので、別の仕組みが必要となりますが、WordPress などのように優れたコンテンツを管理するための仕組みが欲しくなります。
そうしたニーズに応えるのが、この ヘッドレス CMS で、最低限の UI で コンテンツを管理することができ、作成したコンテンツを API 軽油などで提供し、上記サイト・ジェネレータなどと組み合わせてサイトを作ります。

主なヘッドレス CMS は、こちら [headlesCMS](https://headlesscms.org/) に 列挙されています。
![](/articles/assets/lulzneko/serverless/jamstack/04.png)

前半に Open source な ヘッドレス CMS が 並び、後半に Closed source があります。
またサービスとして提供されているものは、こちらの Closed source に 含まれます。たとえば最近よく目にする [Contentful](https://www.contentful.com/) は Closed source になります。

サイト・ジェネレータによっては、WordPress の REST API からコンテンツをとってきたり、ローカルの Markdown ファイル を 使うこともできるので、サイト・ジェネレータの機能と管理方法に合わせて選択します。


## 参考情報
- [JAMstack | JavaScript, APIs, and Markup](https://jamstack.org/)
- [The JAM Stack](https://speakerdeck.com/biilmann/the-jam-stack)
- [StaticGen](https://www.staticgen.com/)
- [headlesCMS](https://headlesscms.org/)
- [JAMSTACKってなんなのか問題に立ち向かう](https://slides.com/masayakazama/what-is-jamstack)


----
実際のところ、Web API ベースなアプリ開発をしていると、意識はしてなくとも JAMStack な ウェブフロント を 作っているなぁというケースもあったりします。

さっくり言ってしまうと、そんな Web API ベースなアプリでウェブフロントを構築済みの HTML として CDN に 配置するものに、JAMStack という名前がついたという感じになります。

名前がついたことで認識され、認識されることで広まるという流れになっていくのではないでしょうか。
