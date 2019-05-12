---
title: hexo-related-popular-posts にサブパス対応のプルリクを送る
permalink: pull-request-to-hexo-related-popular-posts-about-subpath-enablement
date: 2019-04-20
author: lulzneko
categories: プルリクエスト
tags:
- JAMStack
- Hexo
---

Static Site Generator の Hexo で「人気の記事」を実現する Plugin、hexo-related-popular-posts。ビルド時に Google Analytics からアクセス解析のデータを取得してランキングを作成するという素晴らしいアイデアで不可能を可能にしてくれます。
今回こちらの Plugin を使わせていただいたところ、サブパスの URL に対応する機能がなかったのでエンハンスのプルリクエストをだして拡張してもらいました。

![](/articles/assets/lulzneko/develop/develop.jpg)


## hexo-related-popular-posts
[tea3/hexo-related-popular-posts](https://github.com/tea3/hexo-related-popular-posts) は [Hexo](https://hexo.io/) の Plugin で、Google Analytics からアクセス解析のデータを取得して各記事のランキングを作成してくれます。
![](/articles/assets/lulzneko/develop/pullreq/hexo-related-popular-posts/01.png)

Hexo は Static Site Generator で JAMStack サイトを作ります。JAMStack は静的なサイトで、アクセス解析のようなことはできず、通常は「人気の記事」のような動的な要素が必要なことはできません。

ところが tea3/hexo-related-popular-posts は冒頭に書きました通り Google Analytics と連携することで実現してくれる、最高にスゴイ Plugin です。

詳しくは「[ブログで使っている Hexo に人気の記事リストを表示したい！](https://riotz.works/articles/2019/04/19/want-to-display-list-of-popular-posts-on-hexo-used-in-blog/)」を、ご参照ください。


## プルリクを出すことになった背景
本ブログに人気の記事リストを導入した際に、順不同でアクセス解析が反映さていない現象に遭遇しました。

正しくないランキングに明らかにアクセスがないであろう「Hello World !!」が入っており不思議だったのと、起動しなおすとランキングが入れ替わる挙動をしました。
![](/articles/assets/lulzneko/develop/pullreq/hexo-related-popular-posts/02.png)

※「[Hello World !!](https://riotz.works/articles/2017/12/01/Hello-World-!!/)」の記事は実際にアクセス数 0。さりげなくリンクを貼ってみます。


## インストール・エラーとなる原因
まず hexo-related-popular-posts での動作確認です。アクセス解析したデータが出力されるファイル `rankingSheet.txt` を確認したところ、PV が 0 でした。
![](/articles/assets/lulzneko/develop/pullreq/hexo-related-popular-posts/03.png)

Google Analytics からデータが取れていないことも考えられるので、hexo-related-popular-posts が使っているライブラリ [sfarthin/ga-analytics](https://github.com/sfarthin/ga-analytics) の動作確認したところ、コンソールに正しく表示できました。環境設定に問題はなさそうです。
※ ga-analytics の動作確認については、こちら「[Google Analytics に プログラムでアクセスできるようにする](http://localhost:4000/articles/2019/04/17/programmatically-access-google-analytics/)」をご参照ください

そうなるとデータを取得した後に何かがあるのかなということで、hexo-related-popular-posts のソース確認に入ります。

Visual Studio Code で Hexo プロジェクト配下の `node_modules/hexo-related-popular-posts` を開きます。
```console
$ cd node_modules/hexo-related-popular-posts
$ code .
```

ソースコード 13ファイルとはいえ、何かあたりが欲しいところ。まずは `rankingSheet.txt` を正しく表示できるところを目標にランキングデータファイル名の設定 `rankingSheet` を全文検索します。そうすると `cache.js` がかかりました。
![](/articles/assets/lulzneko/develop/pullreq/hexo-related-popular-posts/04.png)

ざっと眺めてみると 33行目に `// PV update` と、それっぽいコメントがあり、36行目でパスの比較をしてデータモデルを作っているようです。
ここにデバッグ用のコンソール出力を挟んで動作確認をしたところ `articles/` 有無でミスマッチしていることがわかりました。
```console
2018/12/29/what-we-did-in-2018/ == articles/2018/12/29/what-we-did-in-2018/
2018/12/31/review-of-2018/ == articles/2018/12/31/review-of-2018/
2019/04/03/take-seminar-on-shiftup-vol3/ == articles/2019/04/03/take-seminar-on-shiftup-vol3/
2019/04/01/k9us-blog-mentoring-to-lulzneko/ == articles/2019/04/01/k9us-blog-mentoring-to-lulzneko/
2019/02/24/summary-of-qa-at-jawsdays2019/ == articles/2019/02/24/summary-of-qa-at-jawsdays2019/
...
```

この `articles/` は、本ブログ URL のトップで `riotz.works` ドメインのサブパスに配置されているパス名になります。そのサブパスが Google Analytics のデータには含まれていて、Hexo のデータには入っていなかったことからランキングデータが正しく作られなかったようです。

原因が判明したので、サブパスを使っている場合に対応する機能拡張をプルリクします。


## フォークしてプルリク作成の環境用意
プルリクを出すために、まずは元リポジトリをフォークしてプルリク作成の環境を作ります。

[tea3/hexo-related-popular-posts](https://github.com/tea3/hexo-related-popular-posts) のリポジトリへ行き、画面右上の [Fork] をクリックします。
![](/articles/assets/lulzneko/develop/pullreq/hexo-related-popular-posts/05.png)

いい感じにリポジトリがフォークされます。(この画面、スキ)
![](/articles/assets/lulzneko/develop/pullreq/hexo-related-popular-posts/06.png)

自分のアカウントにフォークしたリポジトリが作られるので `git clone` します。
(画面左上のリポジトリのアカウントが自分になっていて、フォーク元が表示されている)
![](/articles/assets/lulzneko/develop/pullreq/hexo-related-popular-posts/07.png)


## フォークした自分のリポジトリに変更を加える
プルリクしたい変更を自分のリポジトリに行います。
しっかりやるには作業用ブランチを用意しますが、今回は単発の作業で開発用ブランチもなさそうなので `master` ブランチに直接変更を加えてしまいます。

**変更の方針**
原因究明時にほぼ判明しているので難しくないですが、以下となります。
- `cache.js` にサブパスの文字列を持ってくる (今回は `articles/` になる文字列)
- Google Analytics とパスマッチする部分で、上記サブパスを利用する
![](/articles/assets/lulzneko/develop/pullreq/hexo-related-popular-posts/04.png)

サブパスの文字列は Hexo の設定、プロジェクト直下　`_config.yml` ファイルの `root: /articles/` になります。Hexo 公式ドキュメントでは "root directory" と書かれている、こちら [URL - Configuration | Hexo](https://hexo.io/docs/configuration#URL) の設定になります。

これを `cache.js` で使うには `hexo.config` の `root` プロパティ、すなわち `hexo.config.root` を使います。ただし、このままだと `/articles/` のように先頭の `/` でまたミスマッチするので `hexo.config.root.slice(1)` にして先頭を取り除きます。

ここで取得したサブパスの文字列を判定の `if` 文で使います。(上記キャプチャの 36行目)
```javascript
if (hexo.config.root.slice(1) + hexo.locals.cache.posts.data[v].popularPost_tmp_gaData.path == tmp_gaData[w].path) {
  // ...
}
```

このままだと処理がわかりにくいのでリファクタリング。
とくに `slice(1)` を毎回処理するのはムダなのでファイル上部の変数宣言部に移動します。`root` という変数名は微妙ですが設定の項目名をそのまま使っているようなので合わせます。
```javascript
const root = hexo.config.root.slice(1)

if (root + hexo.locals.cache.posts.data[v].popularPost_tmp_gaData.path == tmp_gaData[w].path) {
  // ...
}
```

テストケースが用意されているのでテストが失敗しないか確認しておきます。
修正が完了したら自分のリポジトリへプッシュします。


## プルリクの作成
フォークした自分のリポジトリへ行き、変更がプッシュされていることを確認します。
つづいて画面中ほどの左に [New pull request] のボタンがあるのでクリックします。
![](/articles/assets/lulzneko/develop/pullreq/hexo-related-popular-posts/08.png)

プルリク作成画面が表示されるので、以下を確認し [Create pull request] をクリックします。
- [file changed] から差分の最終確認
- マージ先の確認(今回は `tea3/hexo-related-popular-posts` の `master`)
![](/articles/assets/lulzneko/develop/pullreq/hexo-related-popular-posts/09.png)

プルリク作成画面プルリクの目的と機能についての説明コメントを書き [Create pull request] をクリックします。
今回、作者さんのブログが日本語なので日本語でリクエストを出しても大丈夫だと思いましたが GitHub は英語で書かれているのとクローズされているプルリクも英語でやり取りしていたので英語にしました。(私の英文がヤバげなのは英語がまったくできないので頑張ってくググった成れの果てです💦)
![](/articles/assets/lulzneko/develop/pullreq/hexo-related-popular-posts/10.png)

無事プルリクが送られたので、マージされるのを期待して待ちましょう。
![](/articles/assets/lulzneko/develop/pullreq/hexo-related-popular-posts/11.png)

[Support sub-path URL configuration with popular posts feature by lulzneko · Pull Request #19 · tea3/hexo-related-popular-posts](https://github.com/tea3/hexo-related-popular-posts/pull/19)


## 一晩でマージされた！
プルリクを出して一晩というか、深夜に出して夕方にはマージしリリースしてくださりました。はやっ！[ᴛ ᴇ ᴀ 🍵(@tea0828)さん](https://twitter.com/tea0828) ありがとうございます！！



----

プルリクもマージされ人気記事のリストが表示されるようになりました。

プルリクを送る、となるとハードルが高いように感じるかもしれませんが作業そのものとしては難しくはありません。この記事がプルリク作成のきっかけになれば幸いです。
