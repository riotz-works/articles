---
title: OSS-Friday 活動 - 2019年6月まとめ
permalink: summary-of-oss-friday-activities-in-2019-06
date: 2019-07-06
author: lulzneko
categories: プルリク / OSS-Friday
tags:
---

普段お世話になっている OSS プロダクト、日常で OSS 活動をしていないと貢献する機会をつい逃しがち。なので毎週金曜日は少しでも OSS 活動へ意識を向ける習慣 OSS-Friday として位置付けるようにしています。2019年6月の OSS 活動についてサマリーします。

![](/articles/assets/lulzneko/develop/develop.jpg)


<blockquote class="twitter-tweet" data-lang="ja"><p lang="ja" dir="ltr">ちょっとずつだけど、始めて見る<a href="https://t.co/6mPGDlJ6kq">https://t.co/6mPGDlJ6kq</a> <a href="https://t.co/BvRQ6KQwps">pic.twitter.com/BvRQ6KQwps</a></p>&mdash; lulzneko (ラルズネコ) (@lulzneko) <a href="https://twitter.com/lulzneko/status/1131196885023088640?ref_src=twsrc%5Etfw">2019年5月22日</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

**OSS-Friday 参考情報**
- [オープンソース フライデー | 今週の金曜日は、利用しているお気に入りのソフトウエアを数時間助けてみよう。](https://opensourcefriday.com/?locale=ja)
- [「Open Source Friday」をGitHubが提唱。金曜日は自分の好きなオープンソースに貢献しよう － Publickey](https://www.publickey1.jp/blog/17/open_soruce_friday_github.html)
- [OSS（オープンソースソフトウェア）の開発に参加したいあなたへ：新春特別企画｜gihyo.jp … 技術評論社](https://gihyo.jp/dev/column/newyear/2018/start-oss-development)


## サマリー
OSS-Friday のページで GitHub のアカウント連携すると自分のページが作られます。
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://opensourcefriday.com/users/lulzneko" data-iframely-url="//cdn.iframe.ly/OEQqnh3?iframe=card-small"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>

このページの中に GitHub での「過去３ヶ月間＆過去1,000イベント」から金曜日の活動をサマリーしたものがあります。以下、そちらから抜粋。

June 07, 2019
- hexojs/hexo-theme-landscape : [Remove Google+ shared link button because the Google+ service has ended](https://github.com/hexojs/hexo-theme-landscape/pull/136)
- hexojs/hexo-theme-landscape : [Remove Google+ OGP header tags because the Google+ service has ended](https://github.com/hexojs/hexo-theme-landscape/pull/137)
- hexojs/hexo-theme-landscape : [Set the banner image path as a configuration file (resolve #106)](https://github.com/hexojs/hexo-theme-landscape/pull/138)
- zefman/gridsome-source-instagram : [Fix typo in the usage configuration example](https://github.com/zefman/gridsome-source-instagram/pull/1)

June 14, 2019
- Readify/httpstatus : [Respond object literals to JSON](https://github.com/Readify/httpstatus/pull/63)
- Readify/httpstatus : [Add 103 Early Hints [RFC8297]](https://github.com/Readify/httpstatus/pull/64)
- Readify/httpstatus : [Add 207 Multi-Status  [RFC4918]](https://github.com/Readify/httpstatus/pull/65)
- Readify/httpstatus : [Add 423 Locked [RFC4918]](https://github.com/Readify/httpstatus/pull/66)
- Readify/httpstatus : [Add 506 Variant Also Negotiates [RFC2295]](https://github.com/Readify/httpstatus/pull/67)

June 21, 2019
- hexojs/hexo-browsersync : [Introduce an option to change tag to inject Browsersync snippets](https://github.com/hexojs/hexo-browsersync/pull/34)

June 28, 2019
- hexojs/hexo-generator-tag : [Add the "order_by" option (resolve #5)](https://github.com/hexojs/hexo-generator-tag/pull/22)
- hexojs/hexo-generator-category : [Add the "order_by" option (resolve #6)](https://github.com/hexojs/hexo-generator-category/pull/23)

2019年6月の OSS-Friday 活動は12件でした！
以降、プルリクエストを出した背景など。


## hexojs/hexo-theme-landscape #136, #137, #138
最初の２つ #136, #137 は、ブログメンターの [カック@ブロガー / k9u (@kakakakakku)](https://twitter.com/kakakakakku) さんにメンタリングをしていただいている中から「Google+ は終了したのにテンプレートに残っているよね」との指摘からプルリクを出したものになります。

記事のシェアに Google+ があったほか、OGP にも Google+ がありました。Google+ という観点では１つですが、コードでは異なる範囲になるのでプルリクは２つに分けて出しました。

<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://github.com/hexojs/hexo-theme-landscape/pull/136" data-iframely-url="//cdn.iframe.ly/E5TNhfA"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>

<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://github.com/hexojs/hexo-theme-landscape/pull/137" data-iframely-url="//cdn.iframe.ly/9zso9Uv"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>

３つ目の #138 は、[Customizable Banner Image · Issue #106 · hexojs/hexo-theme-landscape](https://github.com/hexojs/hexo-theme-landscape/issues/106) を解決するためのプルリクです。デフォルトのブログトップ画像が CSS にハードコードされており、実際に自分のサイトを開くにあったり CSS を編集してパスを変えるか、画像を上書きする必要がありました。それを設定ファイルからパスを指定できるようにしました。

<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://github.com/hexojs/hexo-theme-landscape/pull/138" data-iframely-url="//cdn.iframe.ly/JVuZsG9"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>


## zefman/gridsome-source-instagram #1
こちらは、2019年6月1日の「[初夏のJavaScript祭 in メンバーズキャリア](https://javascript-fes.doorkeeper.jp/events/90894)」で [Gridsome](https://gridsome.org/) のデモアプリを作る際に、Instagram の写真を取得する Plugin [zefman/gridsome-source-instagram](https://github.com/zefman/gridsome-source-instagram) を使わせてもらいました。設定する際に typo を見つけたので修正のプルリクを出したものになります。

<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://github.com/zefman/gridsome-source-instagram/pull/1" data-iframely-url="//cdn.iframe.ly/iVDozXS"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>

**関連コンテンツ**
- [Gridsome で作る JAMStack な サーバーレス Web Front | Slides | Riotz.works](https://riotz.works/slides/2019-javascript-matsuri/#1)
- [初夏のJavaScript祭にて「Gridsome で作る JAMStack なサーバーレス Web Front」の発表をしました](https://riotz.works/articles/lulzneko/2019/06/01/made-presentation-about-jamstack-with-gridsome-at-javascript-matsuri/)
- [初夏のJavaScript祭にて発表した Gridsome のサンプルアプリ実装解説](https://riotz.works/articles/lulzneko/2019/06/03/demo-app-implementation-commentary-presented-at-javascript-matsuri/)


## Readify/httpstatus #63, #64, #65, #66, #67
[HTTP Status を返してくれるサービス httpstat.us を紹介する記事](https://riotz.works/articles/lulzneko/2019/06/12/httpstatus-makes-http-client-testing-easy/) を書いた際に、いくつかの JSON レスポンスが期待値と異なったので修正するプルリクを出しました。

C# でできているとのことで、自分のスキルセットにないので若干ひるんだところもありましたが、コードがシンプルでわかりやすく作られていたのでプルリクを出すことができました。
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://github.com/Readify/httpstatus/pull/63" data-iframely-url="//cdn.iframe.ly/exMQg07"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>

合わせて、いくつかの HTTP Status に対応するプルリクも出しました。
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://github.com/Readify/httpstatus/pull/64" data-iframely-url="//cdn.iframe.ly/VexKWW1"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>

<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://github.com/Readify/httpstatus/pull/65" data-iframely-url="//cdn.iframe.ly/HVW1bUN"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>

<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://github.com/Readify/httpstatus/pull/66" data-iframely-url="//cdn.iframe.ly/vEi38OT"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>

<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://github.com/Readify/httpstatus/pull/67" data-iframely-url="//cdn.iframe.ly/vnaCl8a"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>

**関連コンテンツ**
- [httpstat.us で、簡単 HTTP クライアントのテスト](https://riotz.works/articles/lulzneko/2019/06/12/httpstatus-makes-http-client-testing-easy/)


## hexo-browsersync #34
これは切実に困っている問題に対するワークアラウンドになります。残念ながら根本解決することはできていないのですが、このプルリクによって暫定対処はできます。暫定対処のプルリクのためかマージいただけておらず、入ってくれると助かるのだけど。。。

この Plugin の機能は、Hexo でブログ記事の下書きをする際に、ブラウザを自動リロードしてくれるものです。しかしながら、コンテンツが長い場合にリロードできないという問題があります。トップページで５記事＋サイドバーありは、ほぼエラーとなりリロードできず。単記事のページで文章が短いうちはリロードできるといったレベルです。おそらく、こちら [Problem with long pages · Issue #15 · hexojs/hexo-browsersync](https://github.com/hexojs/hexo-browsersync/issues/15) とも関連してると思われます。

問題の原因ですが、この Plugin は `</body>` タグの後にリロード用のスニペットを注入するのですが、コンテンツが長いと `</body>` へたどり着く前に切れてしまい、スニペットが注入できないことです。

解決に当たっては色々と試さないと分からないことが多いので、まずは `</body>` 以外にスニペットを注入できるようにするオプションを導入し `<body>` と、開始タグへも注入させられるようにしました。これによりコンテンツが長くて切れてしまっても注入箇所は残るので Plugin は動作できるというものです。

Plugin が使っている [Browsersync](https://www.browsersync.io/) も、タグを変更できるオプション [snippetOptions - Browsersync options](https://www.browsersync.io/docs/options#option-snippetOptions) が用意されているので、そのような形として、こちらの Plugin にもあってもいいのかなと思います。

<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://github.com/hexojs/hexo-browsersync/pull/34" data-iframely-url="//cdn.iframe.ly/j6wry2W"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>


# hexojs/hexo-generator-tag #22, hexojs/hexo-generator-category #23
それぞれ Issues へ上がっていたものに対応するプルリクです。

ブログのタグとカテゴリーの一覧表示のページで `order_by` オプションを設定したいというものです。正直あまり用途が浮かばず、対応する必要があるのかなとは思ったのですが "like hexo-generator-archive and hexo-generator-index" とコメントされており、確かにアーカイブの一覧などでは `order_by` の設定ができます。デフォルトで導入されている Plugin 間で対応の差があるのもいかがと思いプルリクを作りました。

<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://github.com/hexojs/hexo-generator-tag/pull/22" data-iframely-url="//cdn.iframe.ly/tURd7bZ"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>

<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://github.com/hexojs/hexo-generator-category/pull/23" data-iframely-url="//cdn.iframe.ly/AVbhTEw"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>



----

６月は Readify/httpstatus の新しい HTTP Status 対応があったので多くのプルリクが出せましたが、実際には１件出すのが精いっぱい、週によってはプルリクを出すことさえ難しいかなというのが正直な感想です。

それでも Issues を眺めたり、コメントを返したりと、まずは日頃お世話になっている OSS へ意識を向けることができればよいのではないでしょうか。

🚲 Let's enjoy [OSS-Friday](https://opensourcefriday.com/) !!
