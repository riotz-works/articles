---
title: Nuxt.js で PWA(Progressive Web Apps) のベースアプリを作る
permalink: develop-base-app-for-pwa-with-nuxtjs
date: 2019-05-09
author: lulzneko
categories: フロントエンド
tags:
- TypeScript
- Nuxt.js
- PWA
- JAMStack
---

ウェブの技術を使い、またウェブサイトとしてホスティングしながらも、モバイルやデスクトップのアプリとして振る舞うことができる PWA。最近では Google Play Store でも配信できるようになったり、iOS での対応強化など話題に事欠きません。
そんな PWA を簡単に高速に開発することができる Nuxt.js を紹介するとともに、今後さまざまな機能を試すためのベースアプリを作ります。

![](/articles/assets/lulzneko/develop/nuxtjs/nuxtjs.png)

**シリーズの記事**
- **Nuxt.js で PWA(Progressive Web Apps) のベースアプリを作る** (本記事)
- [Nuxt.js PWA(Progressive Web Apps) のベースアプリをTypeScript対応する](https://riotz.works/articles/2019/05/10/typescripting-base-app-of-nuxtjs-pwa/)

**環境**
開発者の環境は以下となります。
- Windows 10 64bit + WSL Ubuntu 18.04.1 LTS
- Visual Studio Code
- Node.js 12.2.0
- Yarn 1.15.2
- Nuxt.js 2.6.3


## PWA とは
[PWA](https://developer.mozilla.org/ja/docs/Web/Progressive_web_apps) は Progressive Web Apps の略称で、ウェブサイトをモバイルアプリとして使えるようにする仕組みです。さらに Google Chrome 67 からデスクトップアプリとしても動作させることができるようになりました。

アプリを PWA とすることで、以下のような機能が提供できます。
- 端末にインストールしホームやデスクトップにアイコンを追加できる
- ブラウザとは異なるネイティブアプリのような操作感
- オフラインでも使えるように作ることができる
- プッシュ通知を受け取ることができる
- リンクやサイトへの埋め込みができる
- 自身の運営サイト、および Google Play Store から配信できる

一方でデメリットもあります。
- OS(ブラウザ) により使える機能のサポートレベルが異なる
- ネイティブ実装に対してブラウザでサポートしている機能が足りない (e.g. NFC, 2019年5月現在)

主な PWA (アプリがない状態でブラウザを使いアクセスすると [ホームに追加] が表示される)
- Twitter - https://mobile.twitter.com/
- 日本経済新聞 - https://r.nikkei.com/
- こえのブログ - https://voice.ameba.jp/

また私たち Riotz.works は、モバイルアプリのハッカソン [SPAJAM](https://spajam.jp/) 2018(東京D予選) にて Nuxt.js
 で PWA を作り優秀賞を獲得しました。「モバイルアプリ」の大会なので当然ですがネイティブアプリ開発のチームに対して、「唯一の PWA」として健闘し受賞に至りました。PWA スゴイ！

発表資料: [リアルタイムの競演と参加型観戦で音楽を最高に楽しむ「ラップ､タップ､アップ🎶」](https://riotz.works/slides/?2018-spajam-qualification)
<div class="slide"><iframe src="https://riotz.works/slides/?2018-spajam-qualification"></iframe></div>

作品: [ラップ、タップ、アップ 🎶](https://riotz.works/rap-tap-app/)
[バトルを募集する] ボタンから [対戦者] の [QRコード] または [URL] をスマホに渡しエントリー ＆ カメラ付き PC は [対戦者] ラベル右の [入場] アイコンからエントリーで、ラップバトルの中継が行えます。PWA のため `<iframe>` で埋め込みもでき実際に動作させられます。スマホアプリなのにこういったことができるのも PWA のメリットになります。
<iframe src="https://riotz.works/rap-tap-app/" width="320" height="480"></iframe>


## Nuxt.js とは
[Nuxt.js](https://ja.nuxtjs.org/) は、Vue.js を使ったアプリの開発を支援するフレームワークです。"Vue.js アプリケーションの開発を楽しくするために必要なすべての設定が揃っています - [Nuxt.js](https://ja.nuxtjs.org/)" と謳う通り Vue.js で開発する際に必要となる、さまざまなライブラリや設定があらかじめ構築済みで、簡単にアプリ開発が始められるフレームワークです。

開発できるアプリは３パターンあります。
- サーバーレンダリング - Universal SSR(Server Side Rendering)
- シングルページアプリ - SPA(Single Page Applications)
- 静的サイト(プレレンダリング)

SSR と JAMStack の両方を１つのソースから生成できるため、アプリ特性に合わせてモードを切り替えられます。

[Vue.js](https://jp.vuejs.org/) は、ウェブフロントを開発するためのフレームワークです。"The Progressive JavaScript Framework" と題しており、ウェブ UI に対して少しずつ適用でき、コンポーネントへの分離と再利用がしやすくなっています。

Vue.js は強力なフレームワークですが、あらゆるウェブサイトに対して柔軟に利用できるようコア機能に絞っています。そのため開発を支援するための周辺ライブラリが多数あります。
たとえば、ページの[ルーティング(URL のマッピング)](https://jp.vuejs.org/v2/guide/routing.html) はコア機能には含まれていません。独自に実装するか、公式ライブラリの [Vue Router](https://router.vuejs.org/) を別途導入します。

より踏み込んで高度なことの実装に耐えうる Vue.js ですが、アプリを高速に作り上げるには、ある程度のレールを敷いておいてほしい(たとえば Vue Router は設定済みにしてほしい)、そんなときに Nuxt.js を使うと手軽に開発できます。


## Nuxt.js で、アプリ開発
さっそく Nuxt.js で PWA のベースアプリを作っていきます。
今回のアプリはベースなので以下の仕様としました。今後、このアプリをもとにさまざまな機能の検証などをしたいと思います。
- SPA の JAMStack として作成
- TypeScript 化

今回は PWA アプリで、SPA の JAMStack で作成します。SSR(Universal) にするとサーバーサイドの処理を同時に実装できるので便利ですが、SEO 重視で CGM(Consumer Generated Media) や変化の激しいコンテンツでもない限り JAMStack にして、フロントとサーバーサイドは分離し Web API 経由でデータアクセスするほうが良いでしょう。
詳しくは [JAWS DAYS 2019 で AWS x JAMStack な、サーバーレス Web Front について発表をしました](/articles/2019/03/01/made-presentation-about-jamstack-with-aws-at-jawsdays2019/) をご参照ください。

[TypeScript](https://www.typescriptlang.org/) は JavaScript に型定義とクラスを導入する AltJS のプログラミング言語(正確にはスーパーセット)です。型定義を導入することで開発効率や不具合回避ができるため注目されており、最近で導入されるケースが多いようです。今回はゼロから作るので導入しておきます。
※ Nuxt.js は、バージョン 2.6 からオフィシャルに TypeScript 対応したのですが、2019年5月現在 CLI から簡単に導入できないため詳細は次回の記事で書きます。

2019年5月10日追記
書きました！
⇒ [Nuxt.js PWA(Progressive Web Apps) のベースアプリをTypeScript対応する](https://riotz.works/articles/2019/05/10/typescripting-base-app-of-nuxtjs-pwa/)


### プロジェクトの作成
公式のウィザード CLI (scaffolding tool) があるので、そちらを使います。
今回は最後の引数 `<my-project>` を `pwa-base-app` としました。
```console
$ npx create-nuxt-app <project-name>
```
※ yarn の場合 `yarn create nuxt-app <my-project>` がありますがグローバル汚染回避のために `npx` を使いました

プロジェクト名と説明を聞かれます。必要に応じて入力します。
```console
? Project name (pwa-base-app)
? Project description (My majestic Nuxt.js project)
```

サーバーのフレームワークを聞かれますが JAMStack でいくので `none` を選択します。
```console
? Use a custom server framework (Use arrow keys)
❯ none
  express
  koa
  adonis
  hapi
  feathers
  micro
(Move up and down to reveal more choices)
```

インストールする機能を聞かれます。
- `PWA Support` は、今回の目的なのでチェックします。
- `Axios` は、HTTP Client で Web API などにアクセスするのに使います。使うケースが多いので入れておきます。
- `Linter / Formatter` と `Prettier` は TypeScript との兼ね合いがあり CLI からは入れません。
```console
? Choose features to install (Press <space> to select, <a> to toggle all, <i> to invert selection)
 ◉ Progressive Web App (PWA) Support
 ◯ Linter / Formatter
 ◯ Prettier
❯◉ Axios
```

UI のフレームワークを選択します。ここはお好みで。
今回は `none` を選択し、本アプリを開発する際に別途導入します。(普段 [Vuetify](https://vuetifyjs.com/ja/) を使ってますがウィザードで入れると生成されるプロジェクトのサンプルコードが大きくなりすぎるので自分で入れてるというのもあります)
```console
? Use a custom UI framework (Use arrow keys)
❯ none
  bootstrap
  vuetify
  bulma
  tailwind
  element-ui
  buefy
```

テスティングのフレームワークを選択しまが、TypeScript にするので後から追加します。(このタイミングで追加すると `babel-jest` になるため)
```console
? Use a custom test framework (Use arrow keys)
❯ none
  jest
  ava
```

Nuxt.js のモードを選択します。JAMStack にするので `Single Page App` を選択します。
```console
? Choose rendering mode (Use arrow keys)
  Universal
❯ Single Page App
```

Author を入力し、パッケージマネージャーを選択します。
```console
? Author name ()
? Choose a package manager (Use arrow keys)
  npm
❯ yarn
```

以上でウィザードが終了し、プロジェクトの生成とパッケージのインストールが行われます。
ウィザード終了時の案内に従いプロジェクトのディレクトリへ移動し、開発サーバーを起動します。
※ Yarn を選んだ場合に `yarn run dev` を案内されますが `run` は省略可能です
```console
$ cd pwa-base-app
$ yarn dev
```

ウィザード終了時の案内に出ていた通りブラウザで `http://localhost:3000/` へアクセスすると、PWA な Nuxt.js アプリが表示されます。
![](/articles/assets/lulzneko/develop/nuxtjs/01.png)


**Note**
2019年5月現在、開発サーバーを起動すると `ERROR  (node:515) DeprecationWarning: Tapable.plugin is deprecated. Use new API on .hooks instead` の警告が表示されますが `@nuxtjs/pwa` のバージョンが古いためです。`ERROR` と出ていますが問題なく動作します。気になる場合は下記コマンドでモジュールをアップデートします。
```console
$ yarn upgrade --latest
```
※ npm の場合は、[tjunnone/npm-check-updates](https://github.com/tjunnone/npm-check-updates) を使うとよいでしょう


### デプロイ用のビルド
デプロイ用のビルドを実行すると `dist` ディレクトリが作られます。
この `dist` ディレクトリの内容物をウェブサーバーに配置するだけでデプロイ完了となります。
```console
$ yarn build
```
※ 同じようなコマンドで `generate` がありますが、こちらは完全に静的化され HTTP Request が使えなくなるので `build` を使います。各種サンプルなどで `generate` が出てくる場合は `build` に読み替えてください。たとえば下記デプロイの FAQ は `generate` で書かれていますが `build` を使います。

デプロイについては環境によって設定などが異なってくるので別途書きたいと思います。
試してみたい方は、[公式 FAQ](https://ja.nuxtjs.org/faq/) をご参照ください。
- [AWS S3 と CloudFront によるデプロイ](https://ja.nuxtjs.org/faq/deployment-aws-s3-cloudfront/)
- [Dokku へデプロイ](https://ja.nuxtjs.org/faq/dokku-deployment/)
- [GitHub Pages へデプロイ](https://ja.nuxtjs.org/faq/github-pages/)
- [Google App Engine へのデプロイ](https://ja.nuxtjs.org/faq/appengine-deployment/)
- [Heroku へデプロイ](https://ja.nuxtjs.org/faq/heroku-deployment/)
- [Netlify を使ったデプロイ](https://ja.nuxtjs.org/faq/netlify-deployment/)
- [Now を使ったデプロイ](https://ja.nuxtjs.org/faq/now-deployment/)
- [Surge へデプロイ](https://ja.nuxtjs.org/faq/surge-deployment/)
- [nginx proxy](https://ja.nuxtjs.org/faq/nginx-proxy/)


また、現時点でデプロイしても PWA として機能します。
![](/articles/assets/lulzneko/develop/nuxtjs/02.png)

キャプチャ左側がスマートフォンでブラウザ表示時 [ホームに追加] が案内されます。中央がホームに追加後アプリとして起動した状態です。まだ何もないアプリですがブラウザのアドレスバーが消えてアプリっぽくなっています。アプリ内のボタンを押すとブラウザに戻るのは、アプリのドメイン外へ出たためです。

キャプチャ右側は Windows PC に、デスクトップ PWA としてインストールして起動したものになります。こちらもアドレスバーがなくアプリのような感じになります。


## ソースコード
今回作成した部分までのソースを GitHub へアップしました。
https://github.com/riotz-works/samples-pwa-base-app/tree/0.0.1

GitHub Pages にホスティングもしました。
(公開サイトは１つのため記事公開に合わせて変わり、本記事の内容とは異なります)
https://riotz.works/samples-pwa-base-app/



----

Nuxt.js で PWA のベースアプリができました！

後は `pages`  ディレクトリに表示するページの vue ファイルを追加していくことでアプリが作れますが、その前に TypeScript は入れておいたほうが良く次回の記事で、その辺のところを書きます。
TypeScript 対応しても、ページ単位で TypeScript/JavaScript を切り替えられますし、TypeScript の中に JavaScript が書けるので、TypeScript をやったことがなくても導入してしまい少しずつ使うということができます。

2019年5月10日追記
書きました！
⇒ [Nuxt.js PWA(Progressive Web Apps) のベースアプリをTypeScript対応する](https://riotz.works/articles/2019/05/10/typescripting-base-app-of-nuxtjs-pwa/)

本文中で紹介しました SPAJAM は「2019 予選」が **募集中** です。本記事で PWA のアプリはできたも同然、ぜひハッカソンで試してみましょう！
Riotz.works は「東京A予選」でエントリー中です。チーム名「Riotz.works、進撃のPWA」と、今回こそ PWA で最優秀賞を目指します！！(エントリー結果が出てないので参戦できるかは決まってません)
[予選一覧 ｜ SPAJAM2019公式サイト – 温泉でハッカソン](https://spajam.jp/2019/entry/list/)
