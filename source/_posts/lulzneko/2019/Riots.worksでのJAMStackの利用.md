---
title: Riots.works での JAMStack の利用
permalink: how-jamstack-is-used-in-riots.works
date: 2019-01-29
author: lulzneko
categories: フロントエンド
tags:
- JAMStack
- Gridsome
- Nuxt.js
- Hexo
---

![](/articles/assets/lulzneko/serverless/jamstack.jpg)

最近注目を集め始めている "JAMStack"、より良いパフォーマンス、より高いセキュリティ、より安価で簡単なスケーリング、より良質な開発者エクスペリエンスを提供するアーキテクチャ。

Riotz.works でも注目しており、実際に活用しています。今回は、この JAMStack を Riotz.works でどのように使っているかをご紹介します。


## Riotz.works 公式ウェブサイト
公式ウェブサイトのトップページ [https://riotz.works](https://riotz.works/)、Works ページ、Engineers ページが [Gridsome](https://gridsome.org/) で JAMStack なサイト作りをしています。

[Gridsome](https://gridsome.org/) は 2019年1月現在バージョン 0.4.5 で速いペースで開発が行われておりますが、必要な機能が未実装だったり、実行環境によってはさまざまな準備や設定が必要だったりします。
[StaticGen](https://www.staticgen.com/) では 30位あたりになります。

数あるサイト・ジェネレーターから [Gridsome](https://gridsome.org/) を選んだのは、まずは Node.js で作られていること念頭に置きました。
これは Riotz.works では Node.js、主に TypeScript で開発を行っているため、スキルセットや開発環境を合わせたかったのがあります。

続いて [Vue.js](https://vuejs.org/) ベースであることを選びました。
こちらも開発が [Vue.js](https://vuejs.org/) ベースで行っているので合わせたかったものになります。

そうすると、比較的メジャーと思われる [GatsbyJS](https://www.gatsbyjs.org/) や [Hexo](https://hexo.io/) などが落ちてしまいます。
[GatsbyJS](https://www.gatsbyjs.org/) は非常に興味深いのですが、いつかは使ってみたいと思っていますが、今回は [Vue.js](https://vuejs.org/) ベースとしました。

選択肢としては [Nuxt.js](https://nuxtjs.org/)、[VuePress](https://vuepress.vuejs.org/)、[Gridsome](https://gridsome.org/)、VueWebsite になります。

[Nuxt.js](https://nuxtjs.org/) はアプリ作成で使っていますが、ウェブサイトやブログ構築よりアプリ向けに感じます。
そうなると [Vue.js](https://vuejs.org/) 公式である [VuePress](https://vuepress.vuejs.org/) が最有力になりそうです。

もうひとつの [Gridsom](https://gridsome.org/) も調べたところ、 ["highly inspired by Gatsby.js"](https://gridsome.org/blog/2018/10/10/say-hello-to-gridsome) とあり、また [Vue.js](https://vuejs.org/) ベースであることが、まさに私たちの考えと一致するものでした。

そしてデータソースを GraphQL で扱えるところが魅力的です。
入力ソースは基本的に Markdown を使いたいと考えていますが、エンジニアによっては CMS を使いたいケースもあるでしょう。このような複数のソースを GraphQL で扱えるのが助かります。

![](/articles/assets/lulzneko/serverless/jamstack/10.png)

このような経緯から Riotz.works 公式ウェブサイトは [Gridsom](https://gridsome.org/) で構築することにしました。

ただし、これまでの開発経緯から Articles と Slides は [Gridsom](https://gridsome.org/) とは異なる技術を使っています。
リンクを踏むとヘッダーが異なるのはこのためです。[Gridsom](https://gridsome.org/) へ意向を考えてはいますが、なかなか着手できてない状況です。。


## Riotz.works Articles ページ
[Articles](https://riotz.works/articles/) ページは、[Hexo](https://hexo.io/) で作られた JAMStack なサイトです。

この Articles こそ [Gridsom](https://gridsome.org/) が活用できる場所ではあるのですが、[Gridsom](https://gridsome.org/) が登場する以前に [Hexo](https://hexo.io/) で作ったものになり、いずれは [Gridsom](https://gridsome.org/) へ移行したいと考えてはいますが、[Gridsom](https://gridsome.org/) はまだ初期開発中で Category や Tags などのページを自前で作る必要があり、作りこみができてないです。（いずれ公式でサポートされるとは思うので待ちたいのもあり。。）

[Hexo](https://hexo.io/) は [StaticGen](https://www.staticgen.com/) でもトップ５に入る人気のサイト・ジェネレーターです。

記事は Markdown で書けるので、エンジニア・フレンドリーといえるのではないでしょうか。
一方で表現力は落ちてしまう点がありますが、最後は HTML を直接書いて埋め込めるので、どうしても Markdown で表現しきれない部分は HTML でカバーできます。（あまりやりたくないので奥の手という感じですが）

ブログサイトをとても手軽に作ることができ、テーマも多数用意されているので好みのデザインにすることも簡単ですし、各種 Plugin もあるので簡単に拡張することもできます。

日本語の情報も多いので、比較的簡単にやりたいことや、困りごとを解決できます。

JAMStack なブログサイトをつくり始めるには、よい入口と言えるでしょう。


## Riotz.works Slides ページ
[Slides](https://riotz.works/slides/ ページは、静的サイトではありますが完全に手組の HTML で、"構築済みの HTML" とはなっておらず、アクセスを受けてから JavaScript で HTML を構築しているので JAMStack なサイトではありません。

こちらは、Markdown ファイルをソースとしたプレゼンスライドを作れる [remark.js](https://remarkjs.com/) を使っています。

仕組みとしては事前に用意された Markdown ファイルを、スライド形式の HTML に変換して表示なので、JAMStack とは相性が良く、JAMStack なスライドシステムがあったら使いたいと考えています。

Markdown からスライドを作るライブラリとしては [reveal.js](https://revealjs.com/) と [remark.js](https://remarkjs.com/) ぐらいで、どちらも事前構築の HTML には対応しておらず、現時点では現状のままか、時間を作って自前開発をしたいところです。


JAMStack なスライドシステムがあったら使いたいと考えていますが、現時点ではなさそうなので、しばらくは現状のままでしょう。

スライドシステムについては時間を作って自前開発にチャレンジしたいとも思っています。
ソースは Markdown で書きたいのがありますし、カジュアルな勉強会ではリアルタイムなフィードバック機能を動かしてみたいなど、いろいろとやってみたいことがあります。


## ラップ、タップ、アップ 🎶
最後にハッカソンで作った [ラップ、タップ、アップ 🎶](https://riotz.works/rap-tap-app/) ですが、こちらも JAMStack なアプリです。

技術としては [Nuxt.js](https://nuxtjs.org/) を使っていて SPA で静的サイトとして生成、ホスティングしています。（SSR、サーバサイドレンダリングにすると JAMStack ではなくなります）
これにより基本的な画面は "構築済みの HTML" となり、CDN に配置した際のメリットを出せます。

ラップ対戦のルーム作成はクライアントサイドのJavaScript から Web API を呼出しています。
また動画の中継と配信も JavaScript のライブラリからサービスを呼び出しています。

ハッカソンの 24時間という限られた時間の中で、フロントエンドとサーバサイドを分離し HTML をツールを使って生成する JAMStack のアーキテクチャがまさに活かせた瞬間といえるのではないでしょうか。

サーバサイドの機能が Web API 形式で提供されているため、ロジックの変更や UI の変更といったものが相互に依存せず最小限の修正で組み込みできました。


----

JAMStack は公式ウェブサイトのような完全な静的サイトのほか、ブログやアナウンスなどのニュースといった緩やかな更新が発生するサイト、そしてアプリといったさまざまなサイトが実現でき、Riotz.works の中でも多様な使い方をしています。

とくに [ラップ、タップ、アップ 🎶](https://riotz.works/rap-tap-app/) のような、サイト・ジェネレーターで静的ホスティングなサイトを作っていて、Web API を呼び出しているようなケースは意外とあったりするのではないでしょうか。

実は JAMStack なサイトを作っていたけど、JAMStack のキーワードが知られてなく、そんな名前がついていたんだというケースがいろいろありそうです。（実は [ラップ、タップ、アップ 🎶](https://riotz.works/rap-tap-app/) 作成時点では JAMStack の言葉は知らなかったです）


ただ、これだけ JAMStack な サイトと書いてきましたが 実はホスティングが GitHub Pages で、JAMStack ベスト プラクティスの CDN に 乗ってなかったりも。。

これまでの経緯から GitHub Pages を使ってて、またカスタムドメインを使っているほか、４サイト（リポジトリ）がのっかっているので一斉に引っ越しする必要があり、作業時間を作るのに苦慮しているためです。
