---
title: Shiftup! JP_Getshifter Vol3！ にて「JAMStack なサーバーレス・ウェブフロント」について発表をしました
permalink: made-presentation-about-jamstack-at-shiftup-vol3
date: 2019-04-05
author: lulzneko
categories: プレゼンテーション
tags:
- JAMStack
- Gridsome
- Nuxt.js
- Hexo
---

![](/articles/assets/lulzneko/seminar/shifter/shifter.png)

2019年4月3日に開催された「Shiftup! JP_Getshifter Vol3！はじめてのスタティックサイトジェネレーター」で『JAMStack で構築・運用するサーバーレスなウェブフロント』と題して、JAMStack の世界について発表をしました。そのサマリーです。

**シリーズの記事**
- **Shiftup! JP_Getshifter Vol3！ で JAMStack なサーバーレス・ウェブフロントについて発表をしました** (本記事)
- [Shiftup! JP_Getshifter Vol3！ 参加レポート](/articles/2019/04/03/take-seminar-on-shiftup-vol3/)
- [Shiftup! JP_Getshifter Vol3！ 振り返り、Shifter の HeadlessCMS 化に思いを馳せる](/articles/2019/04/06/think-of-shifters-headlesscms-nize-on-shiftup-vol3)


## Shiftup! JP_Getshifter Vol3！はじめてのスタティックサイトジェネレーター 概要
発表したイベント [Shiftup! JP_Getshifter Vol3！はじめてのスタティックサイトジェネレーター](https://eventregist.com/e/xiza3ieCWYFc) は、サーバーレスな WordPress ホスティング [Shifter](https://www.getshifter.io/japanese/) のユーザーコミュニティの勉強会です。

会場は決済サービスの [Stripe](https://stripe.com/ja-JP/) さん。原宿は竹下口交差点のステキなロケーション＆最高な眺めのオフィスで、お隣の東郷神社の桜がとてもキレイでした。

発表させていただくことになった経緯は、[JAWS DAYS 2019](https://jawsdays2019.jaws-ug.jp/) で『[AWS x JAMStack で構築・運用する サーバーレスな Web Front](https://jawsdays2019.jaws-ug.jp/session/1120/)』の発表をした際に、[Seiji Akatsuka(@seijiakatsuka)さん](https://twitter.com/seijiakatsuka) が聴いてくださっており、再演として本イベントに誘っていただいたのがきっかけです。

実は Shifter さんとは、この JAWS DAYS 2019 よりも前にも縁がありまして、[Serverlessconf Tokyo 2017](http://2017.serverlessconf.tokyo/) で『Java チームが選択した TypeScript による AWS Lambda 開発』で発表した際にスポンサーをされていて出展ブースでお話をさせていただいたことがありました。
WordPress をサーバーレスで動作させるというコンセプトに感嘆し、エンジニアとしてその技術に興味を持っていろいろと聞かせてもらっというのがあり、まさかの２年越しの再縁で発表の機会をいただくとは。
![](/articles/assets/lulzneko/seminar/shifter/03-11.png)

Serverlessconf Tokyo 2017 での発表『Java チームが選択した TypeScript による AWS Lambda 開発』の資料はこちらになります。
<div class="slide"><iframe src="https://riotz.works/slides/?2017-serverless-conf"></iframe></div>

今回のイベントでは JAMStack で フロント系の発表をしましたが、本業はサーバーレスなサーバーサイド エンジニア を しています。こんなことをやっているんだという感じで眺めていただけましたら幸いです。


## 発表資料
『JAMStack で構築・運用するサーバーレスなウェブフロント』の発表資料はこちらになります。
<div class="slide"><iframe src="https://riotz.works/slides/?2019-shiftup-jp_getshifter-vol3"></iframe></div>


## サマリー
発表の主旨は「スタティック・サイト・ジェネレーターの世界、JAMStack を知ろう」です。
JAWS DAYS の発表資料を流用していますが、勉強会のテーマは「はじめてのスタティックサイトジェネレーター」なので、話の内容としてはスタティック・サイト・ジェネレーターが作り出している世界である JAMStack という概念を知ってもら遺体になります。よりスタティック・サイト・ジェネレーターを活用してほしい、そして JAMStack という用語を広めてほしいという思いになります。

大きく３つのテーマでお話ししました。
1. JAMStack とは
2. AWS のサービスと JAMStack の対応
3. JAMStack の可能性

### JAMStack とは
![](/articles/assets/lulzneko/presentation/jawsdays-2019/20.png)
すべては、ほぼこのスライドに集約されます。

JAMStack の定義
- **J**avaScript of client side で、Web API の利用や画面の変更はクライアントで行う
- **A**PIs of resusable で、データの提供は再利用可能な Web API で行う
- **M**arkup of prebuilt で、Markup = HTML は事前に構築して CDN に配置する

JavaScript と API はわかりやすいですが「事前に構築した HTML」はちょっとピンと来ないかもしれません。
この事前に構築するため、本イベントのテーマであるスタティック・サイト・ジェネレーターが重要な役割を担います。

スタティック・サイト・ジェネレーターを使うことで、ウェブサイトの構成を HTML として生成できます。これの反対は、サーバー サイド レンダリング で ブラウザからのリクエストを受けてから サーバー側で HTML テンプレート を 使って 動的な値を埋め込みして HTML を 作り出す方法になります。WordPress が まさにそうです。

WordPress をはじめ、リクエストのたびにサーバーで HTML を作っているとパフォーマンスが悪いです。それをスケールさせるには大変な努力が必要です。よく「WordPress を複数台でスケールさせるには」みたいな話が出ますが、結構大変ですよね。。。

またリクエストを受けて処理する部分があるので攻撃にさらされることにもなります。WordPress でしたら WordPress 自体のセキュリティもあれば、PHP のセキュリティ、ウェブサーバーに、管理画面の保護と考えることがたくさんあります。

では逆に DB のコンテンツすべてを HTML に書きだしてしまって、CDN に配置してしまったらどうでしょうか。それが JAMStack の考えになりスタティック・サイト・ジェネレーターがやってくれることになります。
すでに HTML なので、リクエストを受けるたびに HTML を生成する必要もなく、また CDN が利用者に近いところから配信してくれるので非常にパフォーマンスが良いです。静的な HTML なので攻撃する箇所がありません。
(厳密にいうと、攻撃対象に CDN がありますが責任の範囲外なので)

つまりスライドに上がっているメリットが享受できることになります。

では、JAMStack なサイトを作るためのスタティック・サイト・ジェネレーターはどう入手するのかというと、StaticGen https://www.staticgen.com のサイトにたくさんそろっています。
オススメとしては以下でしょうか（すごく個人的な主観です）
- [GatsbyJS](https://www.gatsbyjs.org/):  JAMStack のコンテキストでも一番登場しますし、一番成熟しています
- [Hexo](https://hexo.io/):               手ごろで使いやすい静的なブログ構築ツール、JAMStack を謳ってないが JAMStack
- [Gridsome](https://gridsome.org/):      まだまだ開発中で上級者向けになりますが、開発の過程を一緒に楽しめます
- [Nuxt.js](https://nuxtjs.org/):  アプリのフレームとして使うのに向いています


### AWS のサービスと JAMStack の対応
JAMStack の定義、ベストプラクティスは AWS のサービスがカバーしてくれるので、本格的なサービスを作る時は AWS を使うとよいでしょう。本イベントはウェブサイトがターゲットとなるのでちょっとオーバースペック気味ですが、全体感をとらえるイメージで見ていただけばと。

![](/articles/assets/lulzneko/seminar/shifter/03-14.png)
自前の Web API も含めたフル構成のイメージになります。
スタティック・サイト・ジェネレーターは Nuxt.js で動的な Web API のデータを利用する形になっています。
ウェブサイトではなく動的な要素をたくさん使うサービスやアプリを作る時には Nuxt.js を使うと作りやすいです。


![](/articles/assets/lulzneko/seminar/shifter/03-15.png)
情報発信するためのウェブサイトのイメージになります。
ローカルにあるマークダウンのファイルを記事としたブログなどのサイトを作る感じなります。配置場所は AWS の CDN である CloudFront になっていますが、Netlify や GitHub Pages などを使うと手軽に作れます。

![](/articles/assets/lulzneko/seminar/shifter/03-16.png)
WordPress で記事を管理しつつ新しいウェブサイトを作ったり、既存の WordPress サイトを JAMStack としてリニューアルするイメージになります。

Shifter のミニ版を自前で作る感じになります。ただし、大事なのが WordPress をどう守るか。
このパターンだと結局 WordPress の運用は残るので、運用の負担は発生しますし攻撃を受けないようにしなければならない。仮に内部ネットワークに持って行けたとしても、内部犯もあるのでセキュリティ確保は必要です。

そうなると、Shifter を使わせてもらうのが楽で良いでしょう。フリープランもありますし。
既存で WordPress を持ってしまっていて、それでもリニューアルして静的サイト化したいというケースでは、ありです。


### JAMStack の可能性
一番向いているのは「情報発信サイト」で、もうひとつが「ウェブアプリのフレーム」です。
ウェブアプリのフレームですが、たとえば私たちが作ったラップ・バトルの『[ラップ、タップ、アップ 🎶](https://riotz.works/rap-tap-app/)』は Nuxt.js で作っており、動画を扱う部分がクライアントサイドの JavaScript であとは静的なHTMLでできています。
このようにサービスと組み合わせると多くのケースで JAMStack なサイトが対応できます。

一方で「**SEO 重視で CGM(Consumer Generated Media) や変化の激しいコンテンツ**」には **向かない** です。
JAMStack は HTML を 事前ビルドするので、変化の激しいコンテンツには処理が追い付かず向きません。
そうなるとフレームだけ JAMStack にしコンテンツは Web API 経由になりますが、SEO 関連のヘッダーは HTML である必要があります。HTML にするには JAMStack のように事前ビルドするか サーバー サイド レンダリング、サーバ側で HTML テンプレートに値を埋め込んで HTML を 生成して返す形が必要となります。


### まとめ
![](/articles/assets/lulzneko/presentation/jawsdays-2019/22.png)


## QA
### ブログのコメント機能のようなものは API 経由になりますか？
はい。クライアントサイドの JavaScript から API 経由で実現します。

※ 本記事投稿時追記
発表資料で引用した The JAM Stack でも API Economy として紹介されています。会場提供してくださった Stripe さんも入っています。
![](/articles/assets/lulzneko/seminar/shifter/03-13.png)

JAMStack はサイトをいかに安全で高速に配信するかという点にフォーカスしていますが、動的なものを持ってはいけないということではなく、静的化できるものは可能な限り事前ビルドして CDN に配置し、動的なものはクライアントサイドのJavaScript でサービスを使いましょうということになります。


### 再利用可能な API が REST になっていますが、GraphQL はどうでしょうか？
「再利用可能な API」でしたら REST に限らず GraphQL を使うこともできます。
今回の発表で「REST API」と私が話をしたのは、普段 REST を使っているから出てきたもので、再利用可能な API でしたら GraphQL や他の方法でも OK です。


## エゴサ
いろいろな記事ありがとうございます🙏　とても嬉しいです😆

[4/3 Shifterミートアップ はじめてのスタティックサイト ジェネレーター まとめ - Togetter](https://togetter.com/li/1334730)
共感いただけたり、的確なコメント、フィードバックさまざまなツイートをいただき嬉しいです！！

[gridsomeのススメとShifterの可能性について – Photosynthesic blog](https://blog.photosynthesic.jp/2019/04/gridsome%e3%81%ae%e3%82%b9%e3%82%b9%e3%83%a1%e3%81%a8shifter%e3%81%ae%e5%8f%af%e8%83%bd%e6%80%a7%e3%81%ab%e3%81%a4%e3%81%84%e3%81%a6/)
発表者の [青空俯角 / mimi(@miminari)さん](https://twitter.com/miminari) のブログです。
"先生" とつけられてしまうと気恥ずかしいです💦
記事中の「Shifter アップデートからの話」は次の記事で書きます。
まとめていただきありがとうございます！！

[JAMStackとスタティックサイト サイトジェネレーターに酔いしれた一夜 JP_Getshifterミートアップレポート | デジタルキューブ](https://www.digitalcube.jp/information/4315/)
運営の [Seiji Akatsuka(@seijiakatsuka)さん](https://twitter.com/seijiakatsuka) の記事です。
全体のまとめ、写真ありがとうございます！
そして素晴らしいイベントで発表する機会をいただき、ありがとうございます！！

<blockquote class="twitter-tweet" data-lang="ja"><p lang="ja" dir="ltr">[Serverless] AWS x JAMStack で構築・運用するサーバーレスなWeb Front のメモです <a href="https://twitter.com/hashtag/jd2019_a?src=hash&amp;ref_src=twsrc%5Etfw">#jd2019_a</a> <a href="https://twitter.com/hashtag/jawsdays?src=hash&amp;ref_src=twsrc%5Etfw">#jawsdays</a> <a href="https://t.co/WbrGcQ062l">pic.twitter.com/WbrGcQ062l</a></p>&mdash; 近藤佑子🐱技術書典6 か66 (@kondoyuko) <a href="https://twitter.com/kondoyuko/status/1099195738116870144?ref_src=twsrc%5Etfw">2019年2月23日</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>
こちらは JAWS DAYS で JAMStack について発表した際に、グラレコを描いていただいたツイートになります。本イベントではないのですが、話の内容を２ページでキレイにまとめて下さり とても分かりやすいので、この記事にも入れさせていただきました。ステキなグラレコ、ありがとうございます！

このグラレコを描いてくだった [近藤佑子🐱技術書典6 か66(@kondoyuko)さん](https://twitter.com/kondoyuko)、2019年月14日の [技術書典６](https://techbookfest.org/event/tbf06) で [これからはじめる 静的サイトホスティング](https://techbookfest.org/event/tbf06/circle/67620002)、各種クラウドサービスを活用した静的サイトホスティングの入門書（GitHub Pages、Firebase、Netlify、Amazon S3）を頒布されるそうです。いろんなホスティングのパターンがあるのでおもしろそうです。


※ できる限り探してまとめさせていただきました。もし入ってなかったり、新たに投稿いただいたなどありましたら Twitter [@lulzneko](https://twitter.com/lulzneko) へ DM やメンションいただけたら幸いです。

----

いろいろなイベントで発表させていただいてきたのですが、勉強会での発表ははじめててでドキドキでした。
しかも普段開発で使っている Serverless Framework のメンテナーにしてサーバーレスの神のひとり [horike takahiro(@horike37)さん](https://twitter.com/horike37)、発表後に知りましたが関西の AWS 他各種イベントのオーガナイザー [hiroramos☁️CPC☄️💫🌎(@hiro_baila)さん](https://twitter.com/hiro_baila)、発表者の [青空俯角 / mimi(@miminari)さん](https://twitter.com/miminari) も WordCamp Tokyo 2019 の実行委員長とすごい人たちが集っている会であり緊張ものでした💦

とはいえ発表中は [KOGA Hiromichi(@digitalcube)さん](https://twitter.com/digitalcube) の絶妙なツッコミやフォローが入るライブ感ある感じでとても楽しめました。ありがとうございます。

イベントにご参加いただいた皆さま、貴重な機会を作っていただいた運営の皆さまに感謝です！ ありがとうございす！！

----

IT カンファレンスなどの大きいイベントも楽しいですが、小規模な勉強会ではテーマを絞って話をしたり、発表者とディスカスしたりできます。この記事やツイートが勉強会への参加のきっかけとなりましたら幸いです。
