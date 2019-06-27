---
title: ブログで使っている Hexo に人気の記事リストを表示したい！
permalink: want-to-display-list-of-popular-posts-on-hexo-used-in-blog
alias: /2019/04/19/want-to-display-list-of-popular-posts-on-hexo-used-in-blog/index.html
date: 2019-04-19
author: lulzneko
categories: フロントエンド
tags:
- JAMStack
- Hexo
---

ブログによくある「人気の記事」のリストが欲しい。しかしながら本ブログは JAMStack で動的なことができません。ところが、スゴイ Plugin があって静的なサイトなのに「人気の記事」ができてしますのです！！

![](/articles/assets/lulzneko/serverless/hexo/hexo.png)


**環境**
本記事の開発環境は以下となります。
- Windows 10 64bit + WSL Ubuntu 18.04.1 LTS
- OpenSSL 1.1.0g (WSL)
- Visual Studio Code
- Node.js 8.10.0
- npm 5.6.0
- sfarthin/ga-analytics 0.0.7
- hexo-related-popular-posts 3.0.4


## JAMStack なサイトの強み/弱み
スゴイ Plugin の紹介の前に、少し JAMStack について整理します。

本ブログは Static Site Generator の Hexo を使って静的なサイト、つまり JAMStack として作っています。静的なサイトなのでプログラムを動かせるようなサーバーは持っていません。

JAMStack にすることでパフォーマンスが良く、安全性を高めたサイトを作ることができます。詳しくは本ブログの記事 [JAMStack、それは ハイパフォーマンスなウェブフロントを実現するアーキテクチャ](https://riotz.works/articles/lulzneko/2019/01/23/jamstack-an-architecture-to-realize-fine-web-front/) をご参照ください。

メリットがあれば、デメリットもあります。静的なサイトになるので動的なこと、たとえばコメント欄などは実現できません。今回のテーマである「人気の記事」も同様で、実現するには各記事へのアクセスを集計しアクセスが多いものを人気として動的にリストを作る必要があります。つまり JAMStack ができない、動的な機能が必要となります。(なお JAMStack で動的な要素が必要な場合は SaaS などのサービスと連携して実現することが多いです)


## hexo-related-popular-posts plugin！
この動的なことができない JAMStack ですが、[tea3/hexo-related-popular-posts](https://github.com/tea3/hexo-related-popular-posts) plugin が「人気の記事」を実現してくれます！

作者 [ᴛ ᴇ ᴀ 🍵(@tea0828)さん](https://twitter.com/tea0828) のサイト [Hexoブログで関連記事や人気記事を生成するプラグインを作った(node.js製hexo)|おちゃカメラ。](https://photo-tea.com/p/hexo-related-popular-posts/) によると、Google Analitycs のページビューを取得して、人気の記事を作るとのことです。確かに Google Analitycs は設置しているケースは多いですし、本ブログでも設定しています。また各ページごとのアクセス数もしっかり取ってくれています。そこに注目して Plugin として実現してしまうのがスゴイ！

そして形態素解析も備えていて、記事本文を分析して「関連する記事」のリストも作ってくれます。また「人気度」「関連度」を組み合わせることもできミックスしてどちらにウェイトを置いてリストを作るかの制御も可能です。

ただし注意点が１つあります。Static Site Generator の Plugin なので「サイト生成時に動作」します。つまり**リアルタイムに人気の記事は変化しません。あくまでも「サイト生成時の人気の記事」**であることに留意が必要です。とはいえ、きちんと投稿したりサイトメンテをしていれば更新されていくので安心ですし、夜間ビルドを走らせて毎日ビルドしてしまう手もあります。

最高にスゴイ Plugin です。[ᴛ ᴇ ᴀ 🍵(@tea0828)さん](https://twitter.com/tea0828) ありがとうございます！！


## インストール
Hexo のプロジェクトディレクトリへ移動して、以下のコマンドを実行します。
```console
$ yarn add hexo-related-popular-posts
```
※ npm を使っている場合は `npm install -S hexo-related-popular-posts`


## 人気の記事リストを表示
さっそく Google Analytics からアクセスデータを取得して人気の記事リストの機能を使っていきます。この機能を使うために Google Analytics へ Web API 経由でアクセスできるようにします。
設定方法の詳細については、こちらの記事 [Google Analytics に プログラムでアクセスできるようにする](https://riotz.works/articles/lulzneko/2019/04/17/programmatically-access-google-analytics/) をご参照ください。

本記事では以下が用意できているものとして進めます。
- [Google Developer Console](https://console.developers.google.com/) で Analytics API を使える認証情報が作られている
- [Google Analytics](https://analytics.google.com/analytics/) で上記アカウントにアクセス権が設定されている
- [sfarthin/ga-analytics](https://github.com/sfarthin/ga-analytics) でトータルのセッション数が取得できている


### Hexo と hexo-related-popular-posts の動作確認
sfarthin/ga-analytics で動作確認した `google-services.pem` を Hexo プロジェクトのルートディレクトリに配置します。

プロジェクトディレクトリ直下の `_config.yml` に以下を追記します。
- `rankingSheet` は人気の記事に使うランキングデータを保存するファイル名
- `pvMeasurementsStartDate` は累計アクセス数の計測を開始する日付
- `cache: path:` は解析結果のキャッシュファイル
```yaml
popularPosts:
  googleAnalyticsAPI:
    rankingSheet: rankingSheet.txt
    pvMeasurementsStartDate: 2005-11-14
  cache:
    path: hexo-popular-related-posts-cached.json
```

以下のコマンドを実行し Hexo ローカルサーバーを起動します。起動時に Google Analytics へアクセスしランキングシートが生成されます。
```console
$ export GOOGLEAPI_CLIENTID="[サービスアカウントの名前].apps.googleusercontent.com"
$ export GOOGLEAPI_EMAIL="[サービスアカウントのメール]"
$ export GOOGLEAPI_KEY="google-services.pem"
$ export GOOGLEAPI_ANALYTICS_TABLE="ga:[Google Analytics の ビュー ID]"

$ yarn hexo start
```

`_config.yml` の `rankingSheet` で設定したファイルに各記事の PV が出力されていることを確認します。(`pvMeasurementsStartDate` を設定してない場合 `TOTALPV` は 0)


### 人気の記事のウィジェットを作成
Plugin の動作が確認できたので、ブログに人気の記事を配置します。
今回はウェブサイトの右側にあるウィジェットを新たに追加します。

「最近の投稿」が似ているイメージなので、こちらをベースに作ります。
Google Chrome でサイトを表示し「最近の投稿」を右クリックして [検証] を選択、Chrome DevTools でソースを確認します。
![](/articles/assets/lulzneko/serverless/hexo/03-01.png)

`<h3 class="widget-title recent">最近の投稿</h3>` の `widget-title` で全文検索します。(※ `recent` は本ブログのカスタマイズにて追加したもので、素の Hexo landscape テーマには `widget-title` しかありません)
たくさん見つかりますが各ウィジェットと、そのスタイルです。「最近の投稿」の本体は `recent_posts.ejs` です。![](/articles/assets/lulzneko/serverless/hexo/03-02.png)

`recent_posts.ejs` を参考に `/themes/landscape/layout/_widget/popular_posts.ejs` を作ります。
![](/articles/assets/lulzneko/serverless/hexo/03-03.png)
```html
<% if (site.posts.length){ %>
  <div class="widget-wrap">
    <h3 class="widget-title popular">よく読まれている投稿</h3>
    <div class="widget">
      <%- popular_posts({ PPMixingRate: 1.0 }) %>
    </div>
  </div>
<% } %>
```

`<%- popular_posts({ PPMixingRate: 1.0 }) %>` が hexo-related-popular-posts で人気の記事を表示するコードになります。
`PPMixingRate` の `1.0` は「人気の記事」、`0.0` は「関連する記事」になります。

設定の詳細は公式の [ヘルパータグの表示オプション - Hexoブログで関連記事や人気記事を生成するプラグインを作った(node.js製hexo)|おちゃカメラ。](https://photo-tea.com/p/hexo-related-popular-posts/#%E3%83%98%E3%83%AB%E3%83%91%E3%83%BC%E3%82%BF%E3%82%B0%E3%81%AE%E8%A1%A8%E7%A4%BA%E3%82%AA%E3%83%97%E3%82%B7%E3%83%A7%E3%83%B3) を、ご参照ください。


### 作成した人気の記事のウィジェットを有効化
ウィジェットを追加するにはテーマの設定ファイル `_config.yml` (`/themes/landscape/_config.yml`) を更新します。
※ こればかりは Chrome DevTools や全文検索では見つけにくくドキュメント参照 [Configuration - hexojs/hexo-theme-landscape: A brand new default theme for Hexo.](https://github.com/hexojs/hexo-theme-landscape#configuration)

`widgets:` に表示するウィジェットのファイル名を列挙します。
今回は最近の投稿の上に表示したいので `- tagcloud` と `- recent_posts` の間に `- popular_posts`(ファイル名 `popular_posts.ejs` の拡張子 `.ejs` を除いた名前) を追加します。
![](/articles/assets/lulzneko/serverless/hexo/03-04.png)


### 作成した人気の記事のウィジェットのスタイル適用
ローカルサーバーを使い動作確認を行うとウィジェットが表示されますが、記事のタイトルが大きいように感じます。これは hexo-related-popular-posts が生成するリストに `<h3>` タグが入っており若干大きく表示されているのがあります。スタイルを適用してそろえるようにします。
(`<h3>` タグを取り除くようにカスタマイズもできます - [リストのHTMLをカスタマイズ](https://photo-tea.com/p/hexo-related-popular-posts/#%E3%83%AA%E3%82%B9%E3%83%88%E3%81%AEhtml%E3%82%92%E3%82%AB%E3%82%B9%E3%82%BF%E3%83%9E%E3%82%A4%E3%82%BA))
![](/articles/assets/lulzneko/serverless/hexo/03-05.png)

先ほどの全文検索で見つかった `sidebar-aside.styl`(`/themes/landscape/source/css/_partial/sidebar-aside.styl`) に以下を追加します。
ウィジェットを作った際に `<h3 class="widget-title popular">` と `widget-title` に `popular` を追加して作りました。これにより人気記事のウィジェットにクラスを当てられるようにしています。あとは `<h3>` タグのフォントサイズを親タグから継承させて完成です。(Riotz.works のサイトではアイコンを入れたりマージンを広げたりしてますが、この辺はお好みで)
```stylus
.widget-title.popular + div li
  h3
    font-size: inherit
```


### 注意
環境変数に設定した GOOGLEAPI_CLIENTID などの各 ID と `google-services.pem` をセットで GitHub の Public Repository などへアップしないようにします。Google Analytics のデータにアクセスされてしまうほか、権限設定に誤りがあると不正操作される可能性があります。


## 関連する記事リストを表示
せっかくなので記事の下に関連する記事のリストも表示します。

デフォルトでタグから関連記事を抽出してくれます。また形態素解析にも対応しており `morphologicalAnalysis` オプションを追加することで利用可能です。形態素解析を利用する場合はプロジェクトディレクトリ直下の `_config.yml` に以下を追記します。
※ 除外キーワードなど設定が柔軟に行えます - [記事本文と関連する記事](https://photo-tea.com/p/hexo-related-popular-posts/#%E8%A8%98%E4%BA%8B%E6%9C%AC%E6%96%87%E3%81%A8%E9%96%A2%E9%80%A3%E3%81%99%E3%82%8B%E8%A8%98%E4%BA%8B)
```yaml
popularPosts:
  morphologicalAnalysis:
  googleAnalyticsAPI:
    rankingSheet: rankingSheet.txt
    pvMeasurementsStartDate: 2005-11-14
  cache:
    path: hexo-popular-related-posts-cached.json
```

表示する場所は SNS 共有リンクの下にします。こちらは [前回の記事](https://riotz.works/articles/lulzneko/2019/04/11/improve-sns-shared-links-usability-of-hexo-used-in-blog/) で改修した場所 `/themes/landscape/layout/_partial/article.ejs` の `<footer>` タグ内になります。
![](/articles/assets/lulzneko/serverless/hexo/03-06.png)

以下のコードを `<footer>` タグ内に追記します。(ここでは、キャプチャの46行目)
```html
      <div class="related-posts">
        <h2>関連記事</h2>
        <%- popular_posts({ PPMixingRate: 0.0 }, post) %>
      </div>
```

`/themes/landscape/source/css/_partial/article.styl` を編集して、スタイルを適用します。
```stylus
.related-posts
  clear: both
  margin-top: 32px
  li
    margin-bottom: 4px
  a
    color: color-link
    text-decoration: none
    &:before
      margin: 0 4px
      content: "－"
    &:hover
      color: color-link
      text-decoration: underline
```

配置やスタイルは全体のデザインや好みがありますので、一例として。
フォントカラーの `color: color-link` は `/themes/landscape/source/css/_variables.styl` に定義されている値を使っています。

また `.article-footer` の CSS が効いているので必要に応じて上書きする必要があります。たとえば `<a>` タグの `color` は記事のタグで使われているため本文と同じ色に設定されています。そのためリンクっぽくないので上書きしています。

ということで、関連する記事のリストが表示できました！
![](/articles/assets/lulzneko/serverless/hexo/03-07.png)



----

JAMStack なのに、アクセス解析が必要な「人気の記事」を作ることができました！
[hexo-related-popular-posts](https://github.com/tea3/hexo-related-popular-posts)、Google Analytics を使う着眼点と、それを実現する実装力、素晴らしいです！！

累計アクセスも取得できているので拡張することで、いわゆる「殿堂入り」リストも作れそうです。
また、この Google Analytics と連携して人気の記事を作る手法は他の Static Site Generator にも応用できるので、最近使い始めている Gridsome 版へのポーティングにもチャレンジしてみたいです。
