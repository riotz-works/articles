---
title: 初夏のJavaScript祭にて発表した Gridsome のサンプルアプリ実装解説
permalink: demo-app-implementation-commentary-presented-at-javascript-matsuri
date: 2019-06-03
author: lulzneko
categories: 開発
tags:
- JAMStack
- Gridsome
- Nuxt.js
---

2019年6月1日の「初夏のJavaScript祭 in メンバーズキャリア」で『Gridsome で作る JAMStack なサーバーレス Web Front』の発表をした際にデモしました Gridsome のアプリについて実装を紹介します。

![](/articles/assets/lulzneko/develop/gridsome/gridsome.png)


今回の発表用に作ったのは [Gridsome](https://gridsome.org/) を使って、[Instagram](https://www.instagram.com/) から画像をとってきて並べるだけの簡単なサンプルアプリです。実装が小さいながらも GraphQL を使い、入力ソースが簡単に扱えて、それでいて画像が出るので表現がわかりやすいかと思います。具体的な実装について聞いてくださった方がいらっしゃったので実装手順を紹介します。

今回 Instagram からの入力に使っている Gridsome の Plugin [@zefman/gridsome-source-instagram](https://github.com/zefman/gridsome-source-instagram) は、"Currently only supports grabbing the latest photos from a user's public instagram profile. - [README.md](https://github.com/zefman/gridsome-source-instagram#zefmangridsome-source-instagram)" とのことで、ちょっとデータを取ってこれるだけのアプリになります。ご注意ください。

発表資料で扱ったサンプルアプリの紹介スライド
![](/articles/assets/lulzneko/presentation/javascript-matsuri/06.png)

**環境**
本記事の開発環境は以下となります。
- Windows 10 64bit + WSL Ubuntu 18.04.1 LTS
- Visual Studio Code
- Node.js 12.2.0
- Yarn 1.15.2
- Gridsome 0.6.3
- @zefman/gridsome-source-instagram 0.1.2


## Gridsome プロジェクトの初期構築
まずは `@gridsome/cli` でプロジェクトの初期構築をします。今回は [project name] を `samples-gridsome-instagram` としました。
```console
$ npx @gridsome/cli create [project name]
```

この時点でローカルサーバーを起動して、初期状態のアプリを確認できます。
下記コマンドを実行し Gridsome が起動したら、ブラウザで `http://localhost:8080/` へアクセスします。
```console
$ cd samples-gridsome-instagram
$ yarn develop
```

シンプルな初期画面が表示されます。[Home] と [About] の間で高速にページの表示切替ができます。
![](/articles/assets/lulzneko/develop/gridsome/01-001.png)

この時点で Lighthouse(Simulated throttling (faster)) は以下のスコアを出します。(キャプチャは、GitHub Pages へデプロイして計測しています)
![](/articles/assets/lulzneko/develop/gridsome/01-002.png)

SEO が 91点なのは `<meta name="description" />` タグがないからで、プロジェクト直下 `/gridsome.config.js` ファイルに `siteDescription` を追加することで 100点です。サイトのタイトルは `siteName` なので、あわせて適切な名前と説明を設定します。(以下、設定項目の全文を抜粋)
```javascript
module.exports = {
  siteName: 'Gridsome',
  siteDescription: 'My Gridsome project',
  plugins: []
}
```

デフォルト値を入れていおくプルリクを出しましたが、本質的ではないのでマージされるのは難しいかな。。。
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://github.com/gridsome/gridsome-starter-default/pull/10" data-iframely-url="//cdn.iframe.ly/5Ffb20a"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>


## Gridsome Plugin の導入とクエリー構築
続いて `@zefman/gridsome-source-instagram` を導入します。
プロジェクトのディレクトリで下記コマンドを実行します。
```console
$ yarn add -D @zefman/gridsome-source-instagram
```

`/gridsome.config.js` ファイルにプラグインの設定をします。(以下、設定項目の全文を抜粋)
- `username` は、取得したいインスタグラムのユーザー名です。(現在のところタグは対応していないのでユーザー名が必要で、今回は公式の `instagram` さんにしました)
- `typeName` は、GraphQL のクエリーで、このプラグインと設定を示すのに使う文字列です。
```javascript
module.exports = {
  siteName: 'Gridsome',
  siteDescription: 'My Gridsome project',
  plugins: [
    {
      use: '@zefman/gridsome-source-instagram',
      options: {
        username: 'instagram', // Instagram username
        typeName: 'InstagramPhoto' // The GraphQL type you want the photos to be added under. Defaults to InstagramPhoto
      }
    }
  ]
```

この状態で `yarn develop` でローカルサーバーを再起動します。
起動したら `http://localhost:8080/___explore` へアクセスします。
GraphQL の Playground が表示されるので、左側のクエリーエディターに以下を入力して画面中央の [▷ (再生)] ボタンをクリックします。
- `photos: allInstagramPhoto` は、Plugin で指定した `typeName` の文字列 `allInstagramPhoto` に `photos` の名前で使うという指定です。
- `id` と `display_url` が Instagram から取得した ID と、画像の URL です。
- `text` は、Instagram のコメント(一番最初のだけ)です。
```graphql
query {
  photos: allInstagramPhoto {
    edges {
      node {
        id
        display_url
        edge_media_to_caption {
          edges {
            node {
              text
            }
          }
        }
      }
    }
  }
}
```

右にある [DOCS] や [SCHEMA] タブを確認しながら欲しいデータが取得できるクエリーを作っていきます。(たとえば `shortcode` は `https://www.instagram.com/p/[shortcode の値]` でページを表示するための文字列が返ります。)
![](/articles/assets/lulzneko/develop/gridsome/01-003.png)


## ページの作成
`/src/pages` ディレクトリにページ用のファイルを作ります。URL に対応するアッパーキャメルケース(パスカルケース)の文字列でファイル名 + `.vue` を作ります。
今回は `InstagramPhotos.vue` としました。URL としては slug 化されて `/instagram-photos` となります([Pages - Gridsome](https://gridsome.org/docs/pages))。
ここからはローカルサーバーを立ち上げておき `http://localhost:8080/instagram-photos` へアクセスした状態で作業を進めます。ホットリロードでブラウザが自動更新するので確認が容易です。

コードは下記で、基本的に Vue.js です。`<page-query>` タグが Gridsome 特有の GraphQL のクエリーを書くための場所になります。
また画像も Gridsome 特有の `<g-image>` タグで、これにより最適化されます([Images - Gridsome](https://gridsome.org/docs/images))。
```html
<template>
  <Layout>

    <h1>Instagram Photos</h1>
    <span v-for="edge in $page.photos.edges" :key="edge.node.id">
      <a :href="'https://instagram.com/p/' + edge.node.shortcode" target="_blank" rel="noopener">
        <g-image :src="edge.node.display_url" :alt="'Instagram Photo: ' + edge.node.edge_media_to_caption.edges[0].node.text" class="photo" />
      </a>
    </span>

  </Layout>
</template>

<page-query>
query {
  photos: allInstagramPhoto {
    edges {
      node {
        id
        shortcode
        display_url
        edge_media_to_caption {
          edges {
            node {
              text
            }
          }
        }
      }
    }
  }
}
</page-query>

<style>
.photo {
  width: 144px;
  margin: 0 4px;
}
</style>
```


## レイアウトにリンクを追加
ページが作れたら、そのページへアクセスするためのリンクを作ります。(以下、テンプレート部分のみ抜粋)
[Home] と [About] のリンクがあるのは `/src/layouts/Default.vue` です。
サイト内のページ遷移は Gridsome 特有の `<g-link>` タグを使います。これによりプリフェッチが効いてページ遷移を高速化できます([Linking - Gridsome](https://gridsome.org/docs/linking))。
```html
<template>
  <div class="layout">
    <header class="header">
      <strong>
        <g-link to="/">{{ $static.metaData.siteName }}</g-link>
      </strong>
      <nav class="nav">
        <g-link class="nav__link" to="/">Home</g-link>
        <g-link class="nav__link" to="/about">About</g-link>
        <g-link class="nav__link" to="/instagram-photos">Instagram Photos</g-link>
      </nav>
    </header>
    <slot/>
  </div>
</template>
```

## 完成！
![](/articles/assets/lulzneko/develop/gridsome/01-004.png)

GitHub Pages にホスティングもしました。動作している状態を確認する場合は、こちらへアクセスしてください。
(公開サイトは１つのため記事公開に合わせて変わり、本記事の内容とは異なることがあります)
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://riotz.works/samples-gridsome-instagram/" data-iframely-url="//cdn.iframe.ly/77wK6ZK"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>

また、今回作成した部分までのソースを GitHub へアップしました。
<div style="max-width: 400px;"><div class="iframely-embed"><div class="iframely-responsive" style="padding-bottom: 50%; padding-top: 120px;"><a href="https://github.com/riotz-works/samples-gridsome-instagram/tree/0.0.3" data-iframely-url="//cdn.iframe.ly/0wHqgfv"></a></div></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>


## 発表資料
当日の発表資料と、サマリーの記事はこちらです。
もしよかったら、ご参照ください。

『[Gridsome で作る JAMStack な サーバーレス Web Front](https://riotz.works/slides/2019-javascript-matsuri)』の発表資料はこちらになります。(下記、スライド埋め込み)
<div class="slide"><iframe src="https://riotz.works/slides/2019-javascript-matsuri"></iframe></div>

また発表のサマリー記事は、こちらになります。
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://riotz.works/articles/2019/06/01/made-presentation-about-jamstack-with-gridsome-at-javascript-matsuri/index.html" data-iframely-url="//cdn.iframe.ly/5oo6K8P?iframe=card-small"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>



----

Gridsome アプリの実装例になります。

GraphQL で簡単にデータソースの内容にアクセスできるのを感じていただけたでしょうか。このように Gridsome は Source 系 Plugin を使うことでさまざまなデータソースに容易にアクセスすることができ、GraphQL として画一的に扱うことができます。いろいろなデータソースを扱っておもしろいサイトが作れそうです。
