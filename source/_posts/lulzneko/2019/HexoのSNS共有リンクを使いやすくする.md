---
title: ブログで使っている Hexo の SNS 共有リンクのユーザビリティを高める
permalink: improve-sns-shared-links-usability-of-hexo-used-in-blog
alias: /2019/04/11/improve-sns-shared-links-usability-of-hexo-used-in-blog/index.html
date: 2019-04-11
author: lulzneko
categories: フロントエンド
tags:
- JAMStack
- Hexo
---

本ブログで使っている Hexo のデフォルトのテーマ landscape。記事を SNS で共有するボタンが用意されているのですが、そのボタン群のボックスを出してから使う手順になっています。
ボタン群を直接記事の下に配置することで、手順を減らしてユーザビリティを高めます。

![](/articles/assets/lulzneko/serverless/hexo/hexo.png)


## Hexo の SNS 共有リンクの改修
前回の記事 [ブログで使っている Hexo の SNS 共有リンクに「はてなブックマーク」を追加する](https://riotz.works/articles/2019/04/10/add-hatena-bookmark-to-sns-share-link-of-hexo-used-in-blog/) では、Hexo に「はてなブックマーク(以降、はてブ)」のブックマークを作るボタンを追加しました。

そのとき気になったのが [共有] ボタンをクリックしてから、SNS 共有ボタン群のボックスを出して使う点です。今回は、この部分を改善してユーザビリティを高めます。
![](/articles/assets/lulzneko/serverless/hexo/01-05.png)


## 改修ポイントを確認
前回「はてブ」のボタンを追加したのは `script.js` の `// Share` ブロックでした。
これは `article-share-link` のクラスが割り当てられた要素、つまり記事下の [共有] をクリックすると SNS シェア用のボックスを表示するスクリプトになります。
![](/articles/assets/lulzneko/serverless/hexo/01-03.png)

今回は、このスクリプトをやめて HTML テンプレートに直接各ボタンを配置するようにします。
修正ターゲットは `article-share-link`(= [共有] がある場所)になるので、これを全文検索して探します。
※ [共有] のラベル `共有` は i18n 対応で外部化されているので検索対象に適しません

`article.ejs` の `<footer>` タグ内にありました。(キャプチャでは 36行目)
ここに `script.js` で作っているボタンを移動してきます。
![](/articles/assets/lulzneko/serverless/hexo/02-01.png)


## SNS 共有ボタンを移動
以下のコードを `<footer>` タグの下に追加し、`article-share-link` の `<a>` タグを `<span>` タグに変更します。
```html
      <a href="http://b.hatena.ne.jp/entry/<%- post.permalink %>" class="article-share-hatena" target="_blank" title="このエントリーをはてなブックマークに追加"></a>
      <a href="https://twitter.com/intent/tweet?url=<%- post.permalink %>" class="article-share-twitter" target="_blank" title="Twitter"></a>
      <a href="https://www.facebook.com/sharer.php?u=<%- post.permalink %>" class="article-share-facebook" target="_blank" title="Facebook"></a>
      <a href="http://pinterest.com/pin/create/button/?url=<%- post.permalink %>" class="article-share-pinterest" target="_blank" title="Pinterest"></a>
      <span data-url="<%- post.permalink %>" data-id="<%= post._id %>" class="article-share-link"><%= __('share') %>：</span>
```

基本的には `script.js` にあった、タグを JavaScrip の書式から HTML の書式に変更して移動してます。
`encodedUrl` は HTML 内では文字列連結ができないのと、変数が `post.permalink` に変わり、HTML テンプレートとして `<%- post.permalink %>` を使います。すぐ下の [共有] のリンクで使っているのと同じです。

できあがった HTML テンプレートで表示！
残念ながら左に寄ってしまっています。。。
![](/articles/assets/lulzneko/serverless/hexo/02-02.png)


## SNS 共有ボタンにスタイルをあてる
移動しただけでは残念ながらレイアウトが崩れてしまいました。スタイルを与えて修正します。
(この辺はサイトのデザインとご相談、１つの例として)

まずアイコン共通のスタイルを修正します。`$article-share-link` を修正しますが同じ名前で `.article-share-link` があるので注意です。`$` のほうになります。
主な修正点は以下になります。
- アイコンのボックスが大きいので `width: 20px` `height: 20px` で小さく
- 左に集まってしまっていたのを `float: right` で右に戻す
- 並びが窮屈なので `margin: 0 0 0 10px` で間隔を調整
- アイコンのフォントが [共有] に対して大きいので `font-size: 18px` で調整
```stylus
$article-share-link
  width: 20px
  height: 20px
  display: block
  float: right
  position: relative
  color: #999
  text-shadow: 0 1px #fff
  margin: 0 0 0 10px
  &:before
    font-size: 18px
    font-family: font-icon
    font-weight: normal
    absolute-center(@font-size)
    text-align: center
  &:hover
    color: #fff
```

続いて各アイコンのスタイルを修正します。
カーソルがのったときに背景色が変わるようになっていますが、新しいレイアウトだとアイコンそのものの色を変えたほうがわかりやすいでしょう。
以下 Twitter を例にしますが、すべてのアイコンの定義で同じように修正します。
- `background` の行を `color: color-twitter !important` のようにします
  - 背景色の定義を、フォントの色の定義に変更
  - `.article-footer a` の `color` 定義に負けるので、`!important` で優先
```stylus
.article-share-twitter
  @extend $article-share-link
  &:before
    content: "\f099"
  &:hover
    color: color-twitter !important
    text-shadow: 0 1px darken(color-twitter, 20%)
```

だいぶ良い感じにアイコンが並びました。
`float: right` を使ったのでアイコンが HTML の定義と逆順に並んでいます。今回はこの順番にしますが、並び順をボックスのときと同じようにするには、HTML の `<a>` タグの順番を逆順にします。
![](/articles/assets/lulzneko/serverless/hexo/02-03.png)


## [共有] のラベルからマウスカーソルスタイルとアイコンを外す
SNS 共有のボックスを開くための [共有] ラベルのスタイルを調整します。今度は `.article-share-link`、`.` 始まりの方になります。
矢印のアイコンをなくした方がすっきりしたので消しましたが、表示したい場合は `&:before` 以降を残します。
- `cursor: pointer` を削除し、マウスでクリックできる感をなくす
- `&:before` 以降を削除し、矢印のアイコンを消す
```stylus
.article-share-link
  float: right
  margin-left: 20px
```


## SNS 共有アイコンのボックスを表示するスクリプトを削除する
最後に [共有] ラベルをクリックできないようにします。`<a>` タグと `cursor: pointer` のスタイルをなくしているので、一見クリックできない要素に見えますが、クリックすると壊れたボックスが表示されます。
`script.js` の `// Share` ブロックを削除します。　(キャプチャでは 34行目～86行目、表示の都合により一部折りたたみ)
![](/articles/assets/lulzneko/serverless/hexo/02-04.png)


## 完成！
![](/articles/assets/lulzneko/serverless/hexo/02-05.png)


----

SNS 共有アイコンをボックス表示から、記事の下に展開済みで表示するようにできました。

[ブログメンティ ふりかえり １週目](https://riotz.works/articles/2019/04/09/review-of-k9us-blog-mentee-first-week/) の後に、メンター の [カック@ブロガー / k9u(@kakakakakku)さん](https://twitter.com/kakakakakku)  とお話していた時に「"はてブ"をつけるときにボタンを使う」のがありました。

私が「はてブ」を使っていなかったので、ブックマークを付ける感覚をとらえられずボタンを設置していなかったのですが、確かにサイトを開いてから URL を入力する手順はしなそうです。

Twitter も共有するときにボタンがあれば押す可能性が高まるものの、自分で URL コピーするときは何か書く時で、そうでないときは流す可能性が高まります。ただ Twitter は開いていることが多いので、コピペする率は高いかもしれません。そういったサイトの特性もあることを学びました。

せっかくボタンを設置しましたし、アカウントも開設したので、たくさんのサイトを「はてブ」していきます。

今回は CSS をいろいろと調整しての修正がありました。
キャプチャや作業紹介がしにくかったので Try & Error の紹介は省略しましたが、CSS を試行錯誤するときは Chrome DevTools で CSS を直接編集するとリアルタイムに画面が変わってわかりやすいので、まずは Chrome DevTools で試してコードに落とすと楽です。
