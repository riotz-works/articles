---
title: OSS-Friday 活動 - 2019年5月まとめ
permalink: summary-of-oss-friday-activities-in-2019-05
alias: /2019/06/05/summary-of-oss-friday-activities-in-2019-05/index.html
date: 2019-06-05
author: lulzneko
categories: プルリク / OSS-Friday
tags:
- Node.js
- AWS Lambda
- Hexo
---

2019年5月中頃に OSS-Friday を始めてみます宣言から最初の１ヶ月が経ちました。初月の活動についてまとめます。

![](/articles/assets/lulzneko/develop/develop.jpg)


ブログのメンタリングを受けている中で、メンターの [カック@ブロガー / k9u(@kakakakakku)さん](https://twitter.com/kakakakakku) から、使っている OSS への貢献について話がありました。使わせてもらっている以上、プロダクトやコミュニティへしっかり貢献しましょうということですね。もちろん「同意！」です。
(本件とは関係ないけど「ブログのメンタリング」って、ブログに限らず幅広く見ていただいているので実態としては何と言うとマッチするんだろ🤔)

そうした中から、どのような活動をしようかと考えていたところ、以前 IT 関連の記事で「[オープンソース フライデー | 今週の金曜日は、利用しているお気に入りのソフトウエアを数時間助けてみよう。](https://opensourcefriday.com/?locale=ja)」が取り上げられていたことを思い出し、せっかくなので金曜日を OSS について考える日にしてみることにしました。
<blockquote class="twitter-tweet" data-lang="ja"><p lang="ja" dir="ltr">ちょっとずつだけど、始めて見る<a href="https://t.co/6mPGDlJ6kq">https://t.co/6mPGDlJ6kq</a> <a href="https://t.co/BvRQ6KQwps">pic.twitter.com/BvRQ6KQwps</a></p>&mdash; lulzneko (ラルズネコ) (@lulzneko) <a href="https://twitter.com/lulzneko/status/1131196885023088640?ref_src=twsrc%5Etfw">2019年5月22日</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>


## サマリー
OSS-Friday のページで GitHub のアカウント連携すると自分のページが作られます。
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://opensourcefriday.com/users/lulzneko" data-iframely-url="//cdn.iframe.ly/OEQqnh3?iframe=card-small"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>

このページの中に GitHub での「過去３ヶ月間＆過去1,000イベント」から金曜日の活動をサマリーしたものがあります。(下図、重複を取り除いてキャプチャ)
![](/articles/assets/lulzneko/develop/pullreq/oss-friday-summary/2019-05-001.png)

自分のところの push も出てますが、メインは以下の３つです。
- gridsome/gridsome-starter-default : [Set default values in site description for more SEO friendly](https://github.com/gridsome/gridsome-starter-default/pull/10)
- hexojs/hexo-theme-landscape : [Include article title in tweets text when sharing with Twitter](https://github.com/hexojs/hexo-theme-landscape/pull/134)
- sfarthin/ga-analytics : [Add a callback function to support Node.js v8 or later](https://github.com/sfarthin/ga-analytics/pull/2)

ということで、最初の OSS-Friday 活動は３件でした！
以降、プルリクエストを出した背景など。


## gridsome/gridsome-starter-default #10
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://github.com/gridsome/gridsome-starter-default/pull/10" data-iframely-url="//cdn.iframe.ly/5Ffb20a"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>

こちらのプルリクは、2019年6月1日の「[初夏のJavaScript祭 in メンバーズキャリア](https://javascript-fes.doorkeeper.jp/events/90894)」で [Gridsome](https://gridsome.org/) のデモアプリを作る際に Lighthouse のスコアをとったところ `<meta name="description" />` がないだけでオール 100点にならなかったので出しました。
![](/articles/assets/lulzneko/develop/gridsome/01-002.png)

せっかく[SEO-friendly - [Gridsome](https://gridsome.org/)]を特徴として謳っているので 100点にしておきたいところです。これは設定ファイルにデフォルト値をセットしておくだけで 100点になります。

なにより設定にデフォルトが入っていないと、設定箇所がないように見えるので開発者が気づかない、または調べるのを後回しにして設定忘れが放置される可能性もありえます。デフォルト値は意味のない値とはいえ設定が明示的に入っていることで開発者に意識を向けさせることができるのではないでしょうか。

本質的ではないですが、気になったのでプルリクを出すことにしました。

**関連コンテンツ**
- [Gridsome で作る JAMStack な サーバーレス Web Front | Slides | Riotz.works](https://riotz.works/slides/2019-javascript-matsuri/#1)
- [初夏のJavaScript祭にて「Gridsome で作る JAMStack なサーバーレス Web Front」の発表をしました | Articles | Riotz.works](https://riotz.works/articles/2019/06/01/made-presentation-about-jamstack-with-gridsome-at-javascript-matsuri/)
- [初夏のJavaScript祭にて発表した Gridsome のサンプルアプリ実装解説 | Articles | Riotz.works](https://riotz.works/articles/2019/06/03/demo-app-implementation-commentary-presented-at-javascript-matsuri/)


## hexojs/hexo-theme-landscape #134
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://github.com/hexojs/hexo-theme-landscape/pull/134" data-iframely-url="//cdn.iframe.ly/WMR4p7l"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>

こちらのプルリクは、[Hexo](https://hexo.io/) のデフォルトテーマ [landscape](https://github.com/hexojs/hexo-theme-landscape) の Twitter 共有リンクを開いたときに URL のみだったのを、タイトルも含めるようにしたものです。
![](/articles/assets/lulzneko/serverless/hexo/04-04.png)

[カック@ブロガー / k9u(@kakakakakku)さん](https://twitter.com/kakakakakku) に指摘されるまで気づきませんでした。。。自分が使っているものをしっかり把握しておかないとですね 😓

この共有リンクはテーマのデフォルトではダイアログに入っているのですが、本ブログでは展開して記事の一部に表示する改造をしています。そのため単純な差分が取れずに、よくコードを確認する必要がありましたが勉強になりました。また最新のコードを本ブログのテーマに取り込むきっかけにもなりよかったです。(さりげなく本家からマージした LinkedIn の共有ボタンが入ってる)

テーマ周りでも Issues が上がっていたりするので、引き続き貢献ポイントを探して活動していきたいです。

**関連コンテンツ**
- [ブログで使っている Hexo の SNS 共有リンクに記事タイトルを入れる | Articles | Riotz.works](https://riotz.works/articles/2019/05/22/add-article-title-to-sns-share-link-of-hexo-used-in-blog/)
- [ブログで使っている Hexo の SNS 共有リンクのユーザビリティを高める | Articles | Riotz.works](https://riotz.works/articles/2019/04/11/improve-sns-shared-links-usability-of-hexo-used-in-blog/)


## sfarthin/ga-analytics #2
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://github.com/sfarthin/ga-analytics/pull/2" data-iframely-url="//cdn.iframe.ly/teblWd5"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>

こちらのプルリクは、本ブログの開発環境を Node.js v8 から v10 にバージョンアップしたところエラーが発生したので修正したものになります。

この [sfarthin/ga-analytics](https://github.com/sfarthin/ga-analytics) は、Google Analytics からレポートデータを取得するもので、本ブログでは Hexo で人気記事のリストを実現する [tea3/hexo-related-popular-posts](https://github.com/tea3/hexo-related-popular-posts) Plugin が使っています。エラーが発生しているので修正が必然だったものになります。


**関連コンテンツ**
- [ga-analytics に Node.js 10 以上対応のプルリクを送る | Articles | Riotz.works](https://riotz.works/articles/2019/05/24/pull-request-to-ga-analytics-about-support-nodejs10/)
- [Google Analytics にプログラムでアクセスできるようにする | Articles | Riotz.works](https://riotz.works/articles/2019/04/17/programmatically-access-google-analytics/)
- [ブログで使っている Hexo に人気の記事リストを表示したい！ | Articles | Riotz.works](https://riotz.works/articles/2019/04/19/want-to-display-list-of-popular-posts-on-hexo-used-in-blog/)

----

５月は３件のプルリクを出せました。

まだ、どれもマージされてないのが寂しいですが、最新の２件はプロジェクトテンプレートやテーマですし内容も優先度が高くないので、じっくり待ち。

３つ目の sfarthin/ga-analytics #2 は、エラー修正なのでマージされると嬉しいな。最終更新が 2014年と5年前なのがちょっと心配。。。

気づいたところを少し修正しただけですが、結構楽しいです。
今後とも OSS へ貢献する活動をしていきたいと思います。

🚲 Let's enjoy [OSS-Friday](https://opensourcefriday.com/) !!


**OSS-Friday 参考情報**
- [オープンソース フライデー | 今週の金曜日は、利用しているお気に入りのソフトウエアを数時間助けてみよう。](https://opensourcefriday.com/?locale=ja)
- [「Open Source Friday」をGitHubが提唱。金曜日は自分の好きなオープンソースに貢献しよう － Publickey](https://www.publickey1.jp/blog/17/open_soruce_friday_github.html)
- [OSS（オープンソースソフトウェア）の開発に参加したいあなたへ：新春特別企画｜gihyo.jp … 技術評論社](https://gihyo.jp/dev/column/newyear/2018/start-oss-development?page=1)
