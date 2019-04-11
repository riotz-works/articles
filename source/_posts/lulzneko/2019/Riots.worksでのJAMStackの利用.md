---
title: Riots.works での JAMStack の 利用
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

最近注目を集め始めている "JAMStack"、より良いパフォーマンス、より高いセキュリティ、より安価で簡単なスケーリング、より良質な開発者エクスペリエンス を 提供するアーキテクチャ。

Riotz.works でも注目しており、実際に活用しています。今回は、この JAMStack を Riotz.works で どのように使っているかを ご紹介します。


## Riotz.works 公式ウェブサイト
公式ウェブサイト の トップページ [https://riotz.works](https://riotz.works/)、Works ページ、Engineers ページ が [Gridsome](https://gridsome.org/) で JAMStack な サイト作りをしています。

[Gridsome](https://gridsome.org/) は 2019年1月現在 バージョン 0.4.5 で 速いペースで開発が行われておりますが、必要な機能が未実装だったり、実行環境によっては様々な準備や設定が必要だったりします。
[StaticGen](https://www.staticgen.com/) では 30位あたりになります。

数あるサイト・ジェネレータから [Gridsome](https://gridsome.org/) を 選んだのは、まずは Node.js で 作られていること念頭に置きました。
これは Riotz.works では Node.js、主に TypeScript で 開発を行っているため、スキルセットや開発環境を合わせたかったというのがあります。

続いて [Vue.js](https://vuejs.org/) ベース であることを選びました。
こちらも開発が [Vue.js](https://vuejs.org/) ベースで行っているので合わせたかったというものになります。

そうすると、比較的メジャーと思われる [GatsbyJS](https://www.gatsbyjs.org/) や [Hexo](https://hexo.io/) などが落ちてしまいます。
[GatsbyJS](https://www.gatsbyjs.org/) は 非常に興味深いのですが、いつかは使ってみたいと思っていますが、今回は [Vue.js](https://vuejs.org/) ベースとしました。

選択肢としては [Nuxt.js](https://nuxtjs.org/)、[VuePress](https://vuepress.vuejs.org/)、[Gridsome](https://gridsome.org/)、VueWebsite に なります。

[Nuxt.js](https://nuxtjs.org/) は アプリ作成で使っていますが、ウェブサイトやブログ構築よりアプリ向けに感じます。
そうなると [Vue.js](https://vuejs.org/) 公式である [VuePress](https://vuepress.vuejs.org/) が 最有力になりそうです。

ところが、もうひとつの [Gridsom](https://gridsome.org/) も 調べたところ、 ["highly inspired by Gatsby.js"](https://gridsome.org/blog/2018/10/10/say-hello-to-gridsome) とあり、また [Vue.js](https://vuejs.org/) ベースであることが、まさに私たちの考えと一致するものでした。

そしてデータソースを GraphQL で 扱えるところが魅力的です。
入力ソースは基本的に Markdown を 使いたいと考えていますが、エンジニアによっては CMS を 使いたいケースもあるでしょう。このような複数のソースを GraphQL で 扱えるのが助かります。

![](/articles/assets/lulzneko/serverless/jamstack/10.png)

このような経緯から Riotz.works 公式ウェブサイト は [Gridsom](https://gridsome.org/) で 構築することにしました。

ただし、これまでの開発経緯から Articles と Slides は [Gridsom](https://gridsome.org/) とは異なる技術を使っています。
リンクを踏むとヘッダーが異なるのはこのためです。[Gridsom](https://gridsome.org/) へ 意向を考えてはいますが、なかなか着手できてない状況です。。


## Riotz.works Articles ページ
[Articles](https://riotz.works/articles/) ページ は、[Hexo](https://hexo.io/) で 作られた JAMStack な サイトです。

この Articles こそ [Gridsom](https://gridsome.org/) が 活用できる場所ではあるのですが、[Gridsom](https://gridsome.org/) が 登場する以前に [Hexo](https://hexo.io/) で 作ったものになり、いずれは [Gridsom](https://gridsome.org/) へ 移行したいと考えてはいますが、[Gridsom](https://gridsome.org/) は まだ初期開発中で Category や Tags などのページを自前で作る必要があり、作りこみに手が回ってないというのが現状です。（いずれ公式でサポートされるとは思うので待ちたいというのもあり。。）

[Hexo](https://hexo.io/) は [StaticGen](https://www.staticgen.com/) でも トップ５に入る人気のサイト・ジェネレータです。

記事は Markdown で 書けるので、エンジニア・フレンドリーといえるのではないでしょうか。
一方で表現力は落ちてしまうという点がありますが、最後は HTML を 直接書いて埋め込めるので、どうしても Markdown で 表現しきれない部分は HTML で カバーすることができます。（あまりやりたくないので奥の手という感じですが）

ブログサイトをとても手軽に作ることができ、テーマも多数用意されているので好みのデザインにすることも簡単ですし、各種 Plugin も あるので簡単に拡張することもできます。

日本語の情報も多いので、比較的簡単にやりたいことや、困りごとを解決することができます。

JAMStack な ブログ・サイトをつくり始めるには、よい入口と言えるでしょう。


## Riotz.works Slides ページ
[Slides](https://riotz.works/slides/) ページは、静的サイトではありますが完全に手組の HTML で、"構築済み の HTML" とはなっておらず、アクセスを受けてから JavaScript で HTML を 構築しているので JAMStack な サイトではありません。

こちらは、Markdown ファイル を ソースとしたプレゼンスライドを作れる [remark.js](https://remarkjs.com/) というライブラリを使っています。

仕組みとしては事前に用意された Markdown ファイルを、スライド形式の HTML に 変換して表示なので、JAMStack とは相性が良く、JAMStack な スライドシステムがあったら使いたいと考えています。

Markdown から スライドを作るライブラリとしては [reveal.js](https://revealjs.com/) と [remark.js](https://remarkjs.com/) ぐらいで、どちらも事前構築の HTML には対応しておらず、現時点では現状のままか、時間を作って自前開発をしたいところです。


JAMStack な スライドシステムがあったら使いたいと考えていますが、現時点ではなさそうなので、しばらくは現状のままでしょう。

スライドシステムについては時間を作って自前開発にチャレンジしたいとも思っています。
ソースは Markdown で 書きたいというのがありますし、カジュアルな勉強会ではリアルタイムなフィードバック機能を動かしてみたいなど、いろいろとやってみたいことがあります。


## ラップ、タップ、アップ 🎶
最後にハッカソンで作った [ラップ、タップ、アップ 🎶](https://riotz.works/rap-tap-app/) ですが、こちらも JAMStack な アプリです。

技術としては [Nuxt.js](https://nuxtjs.org/) を 使っていて SPA で 静的サイトとして生成、ホスティングしています。（SSR、サーバサイドレンダリングにすると JAMStack ではなくなります）
これにより基本的な画面は "構築済み の HTML" となり、CDN に 配置した際のメリットを出せます。

ラップ対戦のルーム作成は クライアントサイド の JavaScript から Web API を 呼出しています。
また動画の中継と配信も JavaScript の ライブラリからサービスを呼び出しています。

ハッカソン の 24時間という限られた時間の中で、フロントエンド と サーバサイド を 分離し HTML を ツールを使って生成するという JAMStack の アーキテクチャが まさに活かせた瞬間といえるのではないでしょうか。

サーバサイドの機能が Web API という形で提供されているため、ロジックの変更 や UI の 変更といったものが相互に依存せず最小限の修正で組み込みすることができました。


----

JAMStack は 公式ウェブサイトのような完全な静的サイトのほか、ブログやアナウンスなどのニュースといった緩やかな更新が発生するサイト、そしてアプリといった様々なサイトが実現でき、Riotz.works の 中でも多様な使い方をしています。

特に [ラップ、タップ、アップ 🎶](https://riotz.works/rap-tap-app/) のような、サイト・ジェネレータで静的ホスティングなサイトを作っていて、Web API を 呼び出しているようなケースは意外とあったりするのではないでしょうか。

実は JAMStack な サイトを作っていたけど、JAMStack というキーワードが知られてなく、そんな名前がついていたんだというケースがいろいろありそうです。（実は [ラップ、タップ、アップ 🎶](https://riotz.works/rap-tap-app/) 作成時点では JAMStack という言葉は知らなかったです）


ただ、これだけ JAMStack な サイトと書いてきましたが 実はホスティングが GitHub Pages で、JAMStack ベスト プラクティスの CDN に 乗ってなかったりも。。

これまでの経緯から GitHub Pages を使ってて、またカスタムドメインを使っているほか、４サイト（リポジトリ）が のっかっているので一斉に引っ越しする必要があり、作業時間を作るのに苦慮しているためです。
