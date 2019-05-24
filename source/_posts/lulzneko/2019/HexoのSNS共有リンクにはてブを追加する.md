---
title: ブログで使っている Hexo の SNS 共有リンクに「はてなブックマーク」を追加する
permalink: add-hatena-bookmark-to-sns-share-link-of-hexo-used-in-blog
date: 2019-04-10
author: lulzneko
categories: フロントエンド
tags:
- JAMStack
- Hexo
---

本ブログは Hexo の Static Site Generator でデフォルトのテーマ landscape をカスタマイズして使っています。landscape のテーマは、記事の最後に SNS へ共有するためのリンクがあります。このリンクボタン群に「はてなブックマーク」を追加します。

[2019.4.12 追記]
本記事の方法で共有ボタンを追加した際に `api.shopstyle .com` ドメインへリダイレクトしてから「はてなブックマーク」へ戻る挙動が確認されたため、記事を取り下げておりましたが原因が確認できたので再公開します。
原因はコメント欄のサービス Disqus による挙動と思われ、本サイトの Disqus 利用を停止したところリダイレクトは確認されなくなりました。詳細については別途まとめます。
本記事の方法で共有ボタンを追加することに問題はありません。お騒がせし申し訳ありませんでした。

![](/articles/assets/lulzneko/serverless/hexo/hexo.png)


## Hexo とは
まず、本ブログで使っている [Hexo](https://hexo.io/) を紹介します。
Hexo は "A fast, simple & powerful blog framework" を謳うブログサイトを作るためのフレームワーク、というかツールです。公式サイトでは言及していませんが JAMStack なサイトを作るための Static Site Generator です。

Markdown で書いた記事をもとにウェブサイトの HTML を生成し、生成後は静的なサイトとしてデプロイできウェブサーバーだけで運用できます。簡易的なサイトでよければ [GitHub Pages](https://pages.github.com/) が使えますし、[Netlify](https://www.netlify.com/) や [AWS](https://aws.amazon.com/) の S3+CloudFront などでしっかりと運用もできます。
静的サイトなのでコメント機能は持っていませんが [Disqus](https://disqus.com/) と連携する機能が組み込み済みなので簡単に設置できます。テーマやプラグインが豊富にあり、また日本語での情報も多く使いやすいのが特徴です。

Hexo 同様のツールとしては [GatsbyJS](https://www.gatsbyjs.org/) や [VuePress](https://vuepress.vuejs.org/)、[Gridsome](https://gridsome.org/) などがあります。これらは、より高速で、ブラウザキャッシュを効果的に使う仕組みなどもあらかじめ入っているので、新しく立ち上げるサイトの場合はこれらを検討してみるのもよいでしょう。
また [StaticGen](https://www.staticgen.com/) にたくさんリストされているので、利用している技術スタックや環境などから自分に合ったものを探すのもよいです。


## 記事の共有リンクに「はてなブックマーク」を追加する
Hexo には記事の下に Twitter、Facebook、Pinterest、Google+ へ記事を共有するためのボタンが用意されています。
ここに「はてなブックマーク(以降、はてブ)」でブックマークを作るボタンを追加ます。ついでにサービス終了となった Google+ のボタンは削除します。
![](/articles/assets/lulzneko/serverless/hexo/01-01.png)

当初、Hexo の Plugin があるかと思ったのですが見当たらなかった、というか Plugin できる場所ではないようなのでテーマを直接修正することにします。

修正する場所を見つけるために Chrome DevTools であたりを付けます。
共有リンクの Google+ を右クリックして、コンテキストメニューから [検証] をクリックします。
すると Chrome DevTools が表示され、右クリックして [検証] した場所、ここでは Google+ ボタンのソースがフォーカスされます。
![](/articles/assets/lulzneko/serverless/hexo/01-02.png)

フォーカスされたソースの前後から検索に適しそうな文字列を探します。
今回はフォーカスされた部分の `class="article-share-google"` が使えそうです。できれば `id` 属性を使いたいところですが `class="article-share-google"` もソース内でかなり限定できそうですし、Twitter なども同じような命名でクラス定義されているようなので「はてブ」の CSS を作る時に使えそうです。

検索用の文字列を決めたのでソースから探します。
今回は普段から開発に使っている [Visual Studio Code](https://code.visualstudio.com/) で全文検索をかけました。


## リンクを追加する
`script.js` の中で `article-share-link` のクラスが割り当てられている要素をクリックしたときに、SNS シェア用のリンクが入ったボックスを表示する中で作られているようです。
![](/articles/assets/lulzneko/serverless/hexo/01-03.png)

`class="article-share-google"` を行ごと消して Google+ のボタンを削除します。(キャプチャでは 61行目)

「はてブ」共有用リンクを、今回は Twitter 共有リンクの前に入れます。
以下のコードを追加します。(キャプチャでは 58行目)
※ コードは公式の [はてなブックマークボタンの作成・設置について](http://b.hatena.ne.jp/guide/bbutton) を参考に作りました。 `title` 属性はサイトに合わせて変更してください。
```html
            '<a href="http://b.hatena.ne.jp/entry/' + encodedUrl + '" class="article-share-hatena" target="_blank" title="このエントリーをはてなブックマークに追加"></a>',
```


## スタイルを追加する
`article.styl` に `.article-share-google` が定義されています。
ここに「はてブ」共有リンク用のスタイルを追加します。(キャプチャでは 323行目)
![](/articles/assets/lulzneko/serverless/hexo/01-04.png)

この `.article-share-google` の下に追加します。(`.article-share-google` のブロックは削除して大丈夫です)
```stylus
.article-share-hatena
  @extend $article-share-link
  &:before
    content: "B!"
    font-family: Verdana;
    font-weight: bold
  &:hover
    background: #00a4de
    text-shadow: 0 1px darken(#00a4de, 20%)
```

Hexo 内のアイコンは [Font Awesome 4](https://fontawesome.com/v4.7.0/) が使われています。しかしながら、このフォントセットには「はてブ」アイコンは入っていません。かわりに公式アイコンを使いたいところですが、スタイルにズレが生じてしまうのでフォントアイコンで乗り切りたいところです。

こちらのサイトさんで良い方法を紹介してくださっていました。素晴らしい情報をありがとうございます！
[Font Awesome などアイコンフォントにないはてなブックマークを自力で追加する簡単な方法](https://hayashikejinan.com/webwork/css/913/)

色は公式の [素材集 - 株式会社はてな](https://hatenacorp.jp/press/resource) から、アイコンをカラーピックしました。


## できあがり
![](/articles/assets/lulzneko/serverless/hexo/01-05.png)



----

「はてブ」でブックマークを作るボタンを導入することができました！

導入の背景としては、[ブログメンティ ふりかえり １週目](https://riotz.works/articles/2019/04/09/review-of-k9us-blog-mentee-first-week/) で各 KPI を取得した際に「はてブ」が増えていなかったためです。

なぜ増えていないのか(おそらく以前から)を考えたところ「はてブ」を付けていただけるような記事を作ることはもとより、**そもそもボタンを設置していない**のが大きかったのではないかと反省し導入しました。

また改修していて気づいたのですが [共有] ボタンを押してから、使いたい SNS のボタンを選ぶ手順も良くないなと。[共有] の右に直接アイコンを並べるようにしますが、そのお話は次回。

ウェブサイトのちょっとしたところを修正したい場合に、Chrome DevTools [検証] からの、全文検索は結構使えます。スタイルを手直ししたり、配置を入れ替えたりといったときにおススメです。
