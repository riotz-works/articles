---
title: Shiftup! JP_Getshifter Vol3！ 参加レポート
permalink: take-seminar-on-shiftup-vol3
alias: /2019/04/03/take-seminar-on-shiftup-vol3/index.html
date: 2019-04-03
author: lulzneko
categories: レポート
tags:
- JAMStack
- Gridsome
- Shifter
- WordPress
---

![](/articles/assets/lulzneko/seminar/shifter/shifter.png)


2019年4月3日に開催された「Shiftup! JP_Getshifter Vol3！はじめてのスタティックサイトジェネレーター」に参加したのでレポートです。

私も「JAMStack で構築・運用するサーバーレスなWeb Front」で発表をさせていただきましたが発表レポートの記事は別途。

当日の様子まとめ
[4/3 Shifterミートアップ はじめてのスタティックサイト ジェネレーター まとめ - Togetter](https://togetter.com/li/1334730)

**シリーズの記事**
- [Shiftup! JP_Getshifter Vol5！ にて「Shifter ＋ SSG の世界」について発表をしました](https://riotz.works/articles/lulzneko/2019/08/21/made-presentation-about-shifter-with-ssg-at-shiftup-vol5/)
- [Shiftup! JP_Getshifter Vol3！ で JAMStack な サーバーレス ウェブフロント に ついて発表をしました](https://riotz.works/articles/lulzneko/2019/04/05/made-presentation-about-jamstack-at-shiftup-vol3/)
- **Shiftup! JP_Getshifter Vol3！ 参加レポート** (本記事)
- [Shiftup! JP_Getshifter Vol3！ 振り返り、Shifter の HeadlessCMS 化 に 思いを馳せる](https://riotz.works/articles/lulzneko/2019/04/06/think-of-shifters-headlesscms-nize-on-shiftup-vol3)



## Shiftup! JP_Getshifter Vol3！はじめてのスタティックサイトジェネレーター 概要
今回参加した [Shiftup! JP_Getshifter Vol3！はじめてのスタティックサイトジェネレーター](https://eventregist.com/e/xiza3ieCWYFc) は、サーバーレスな WordPress ホスティング [Shifter](https://www.getshifter.io/japanese/) のユーザーコミュニティの勉強会です。

Shifter の紹介やユーザーさんの話ほか、クラウドやサーバーレス、スタティックサイトジェネレーターなどに携わる人の話が聞けるイベントです。

会場は決済サービスの Stripe さん。
竹下通りと明治通りがつながる竹下口にあるステキなロケーション＆最高な眺めのオフィスでした。(写真撮るのを忘れてしまった)
竹下口のバーガーキング(現ロッテリア)でバイトしてた懐かしい思いでの道を通りつつ会場入りです。

参加者が集まるまで、緩い感じ交流タイムから始まります。

主催者の [KOGA Hiromichi(@digitalcube)さん](https://twitter.com/digitalcube)、[Seiji Akatsuka(@seijiakatsuka)さん](https://twitter.com/seijiakatsuka) にご挨拶 ＆ 登壇者の [青空俯角 / mimi(@miminari)さん](https://twitter.com/miminari) さんとお話させていただきつつ、資料の事前アップが終わってなかったので〆の作業をしちゃいます。

徐々に集まってきた参加者さんと名刺を交えて、しばしご歓談。
今回は「発表者の～」と言いながら名刺を出しているので覚えていただける率は非常に高いですが、確かにこのような場面では先日受けた [心に刺さる名刺のつくり方セミナー](https://riotz.works/articles/lulzneko/2019/03/30/take-seminar-on-how-to-make-meishi-in-tokyo/) を活かした名刺づくりなり交換の仕方が大事になるなぁとさっそく実感。


## Serverless Static Site Generator「Shifter」のデモを含めた、基本的な使い方の紹介
[KOGA Hiromichi(@digitalcube)さん](https://twitter.com/digitalcube)、[Seiji Akatsuka(@seijiakatsuka)さん](https://twitter.com/seijiakatsuka) によるオープニングセッションです。

**まずは本イベント Shiftup! の紹介**
「ビール大事！たまに仕事」とのことで、さすが Happy Beer Taster を名乗っておられる [KOGA Hiromichi(@digitalcube)さん](https://twitter.com/digitalcube) さん。本イベントも会場入りしてすぐにビールが渡されるぐらいおもしろいポリシーです。
![](/articles/assets/lulzneko/seminar/shifter/03-01.jpg)

イベントの主旨
- コラボレーターとの交流
  デザイナー、エンジニア、パブリッシャー、ビジネス、マーケターと幅広く多様なユーザーおよびユーザー間の交流が目的。
- 情報共有
  利用の仕方、メリット、注意ポイント、アップデート情報などを共有する。
- ディスカッション・課題解決
  スタティックサイト？JAMStack？サーバーレス？などの旬の情報から、Shifter の改善、機能リクエストなど。Shifter はコミュニティドリブンで機能が追加されている。


**Shifter の紹介**
"静的サイトジェネレーターとサーバーレスアーキテクチャを世界でもっとも人気のあるCMSと組み合わせたWordPressホスティングソリューション"
![](/articles/assets/lulzneko/seminar/shifter/03-02.jpg)
![](/articles/assets/lulzneko/seminar/shifter/03-03.jpg)

コンセプト
"JAMStack にヒントを得て WordPress でやることを考えた" とのこと。
とうの JAMStack の人たちから、WordPress は JAMStack ではないといわれているが、それを実現してるのが Shifter。このコンセプトはおもしろいです。

なぜ、このようなサービスを提供しているのかというと "価値あることだけに集中する！" ため。
WordPress はツールであり、WordPress 自体を使いたくて使っているわけではない。WordPress を使って価値あることをやりたくて使っているとのこと。

まさにその通りですね。ブログをはじめ情報発信としてのウェブサイトなどを作ることが目的であり、そのために WordPress を使う。
WordPress で作られているサイトもたくさんあります。それをサービスとして提供してくれて安全に管理しておいてくれる。とてもありがたいことです。

ブログサイト作りたくてレンタルサーバで WordPress を構築しているケースとかを見ると、ちゃんとパッチあてしているかなとか、不安になりますが Shifter なら安全に管理してくれているのと、記事投入後は静的サイトとして配置しておいてくれるのでとても安心です。

WordPress 立てるときは、Shifter が使えるか考えてみるのも手です。[無料プラン](https://www.getshifter.io/pricing/)もありますし、コミュニティ向けのサポートも開始したとのことです。


**アップデート**
- コミュニティサポートの開始
  [Shifter Community Event Support Program | デジタルキューブ](https://www.digitalcube.jp/contact/shifter-community-support-program/)

- Shifter Webhooks
  自分のサイトで配信したいニーズにこたえるため Webhook を導入。Shifter CDN で配信するほかに、自分のサイトへ配置できる。

この Shifter Webhooks はエンタープライズ向けがターゲットとの話でした。
配置できる場所がクラウドのようなので、アクセス制限をかけるのはちょっと手間かなという印象がありましたが、機能が拡充してきたら色々使えそうです。

こちらについては懇親会の時間に色々と話をさせてもらいました。
別の記事でまとめます。

そういえばカスタムドメインは Personal プランからだけど Webhooks 使って [Netlify](https://www.netlify.com/) にデプロイしたらカスタムドメインがフリーで使えたり？🤔


**関連イベントのお知らせ**
<blockquote class="twitter-tweet" data-lang="ja"><p lang="ja" dir="ltr">コミュニティ支援イベントをご利用いただきました！<br>JP_Stripe Connect JPSC2019 <a href="https://twitter.com/hashtag/JP_Getshifter?src=hash&amp;ref_src=twsrc%5Etfw">#JP_Getshifter</a> <a href="https://t.co/D9errDSWck">pic.twitter.com/D9errDSWck</a></p>&mdash; JP_Getshifter (@JP_Getshifter) <a href="https://twitter.com/JP_Getshifter/status/1113393808438226945?ref_src=twsrc%5Etfw">2019年4月3日</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

<blockquote class="twitter-tweet" data-lang="ja"><p lang="ja" dir="ltr">コミュニティ支援プログラムをご利用いただきます！<a href="https://t.co/UWxDmCTXOT">https://t.co/UWxDmCTXOT</a><br>カツオ食べたい！<a href="https://twitter.com/hashtag/CLS%E9%AB%98%E7%9F%A5?src=hash&amp;ref_src=twsrc%5Etfw">#CLS高知</a> <a href="https://twitter.com/hashtag/JP_Getshifter?src=hash&amp;ref_src=twsrc%5Etfw">#JP_Getshifter</a> <a href="https://t.co/PeRHs8SYVQ">pic.twitter.com/PeRHs8SYVQ</a></p>&mdash; JP_Getshifter (@JP_Getshifter) <a href="https://twitter.com/JP_Getshifter/status/1113399648834560000?ref_src=twsrc%5Etfw">2019年4月3日</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

<blockquote class="twitter-tweet" data-lang="ja"><p lang="ja" dir="ltr">ついに始動🙌WEBに関係してる“すべての人”におすすめです！<a href="https://twitter.com/hashtag/JP_Getshifter?src=hash&amp;ref_src=twsrc%5Etfw">#JP_Getshifter</a> <a href="https://t.co/BvTMD8c3gP">https://t.co/BvTMD8c3gP</a></p>&mdash; hiroramos☁️CPC☄️💫🌎 (@hiro_baila) <a href="https://twitter.com/hiro_baila/status/1111199567226101760?ref_src=twsrc%5Etfw">2019年3月28日</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>



## こんなに簡単 Gridsome！
発表者: [青空俯角 / mimi(@miminari)さん](https://twitter.com/miminari)

![](/articles/assets/lulzneko/seminar/shifter/03-04.jpg)

**ドキュメントの構成からして初学者への優しさに溢れている。**
- Vue.js のような簡単さ、敷居の低さがよい
- GetStartedが１ページ、コマンドが簡単で使うことができる
- とりあえず試すができる

確かに Vue.js、VuePress、Nuxt.js、Gridsome をはじめ、各 Plugin やツールなど Vue.js 関係のドキュメントはよく整備されていて勉強しやすいです。発表を聞いて、あらためて気づきなおしました。Vue.js いいですよね。


**GraphQL の入門にぴったり。**
- ２ステップで簡単 GraphyQL を使い始めることができる
- 聞いたことはあるが、使ったことがないが、簡単に使い始めることができる

Gridsome は内部的に GraphQL を使っていて、各種ソースを GraphQL で取り出すので、クライアント作りまでいかずに手軽に試せるのと、ツールが付属しているので試行錯誤も簡単にできるのが良いです。

"聞いたことはあるが、使ったことがない" は意外とそうで、私は普段 REST で API を作っていて、次は GraphQL にしたいなぁと思いつつもなかなか着手できてないところがあります。それを Gridsome 使うことで、ちょっとした利用ができ勉強のとっかかりになるが良いです。


**５分間インストールならぬ５分間デプロイ。**
- 既存の WordPress のサイト URL があれば、Netlify にコピーサイトが５分で作れる
- サクッとデモ

![](/articles/assets/lulzneko/seminar/shifter/03-05.jpg)

Gridsome で WordPress をリニューアル。良いですよね。
実際にデモで動くのを見せてもらうと、いい感じです。今度しっかり試してみます。


**関連イベントのお知らせ**
WordCamp Tokyo 2019 11/1-2＠新宿
発表者の [青空俯角 / mimi(@miminari)さん](https://twitter.com/miminari) が実行委員長を担当

<div class="slide"><iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/YqFg5ydh_vs" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe></div>



## はじめてのスタティックサイト ジェネレーター GatsbyとSurgeでHello World! スタティックサイト ホスティングって？
発表者: [Seiji Akatsuka(@seijiakatsuka)さん](https://twitter.com/seijiakatsuka)

発表資料
<script async class="speakerdeck-embed" data-id="388840c4cff24fd28ac94efc889b282f" data-ratio="1.77777777777778" src="//speakerdeck.com/assets/embed.js"></script>

数ある静的サイトジェネレーターから Gatsby で Hello World を紹介。
[青空俯角 / mimi(@miminari)さん](https://twitter.com/miminari) セッションの「こんなに簡単 Gridsome！」の Gridsome は Gatsby にインスパイアされて Vue.js ベースで作っているもので、基本となるフレームワークが React か Vue.js かで、方向性は同じになるのですが、改め両方が詳細されるセッションのラインナップがすごくよかったです。

![](/articles/assets/lulzneko/seminar/shifter/03-06.jpg)
- 静的サイトジェネレーターの振り返り
- さまざまなサービスやツールがあり、組み合わせて使うことができる
- Shifter はすべてをカバーしてる、オールインワン

Shifter が提供するもの。
![](/articles/assets/lulzneko/seminar/shifter/03-07.jpg)


----

とても楽しい勉強会でした。

主催者の [KOGA Hiromichi(@digitalcube)さん](https://twitter.com/digitalcube) の絶妙なトーク＆ツッコミと [Seiji Akatsuka(@seijiakatsuka)さん](https://twitter.com/seijiakatsuka) の冷静な対応のペアがとてもおもしろかったです。

Shifter はとても興味を持っていたサービスで、調べようと思っていたところにちょうど勉強ようと考えていたところに、一気に学べる機会を持ててよかったです。

また Shifter のコミュニティはもとより、サーバーレスのコミュニティ、WordPress のコミュニティ、そして会場を課してくださった Stripe さんと多くのコミュニティやサービスの方々とたくさんお話する機会が得られ嬉しいです。

最近は勉強会になかなか出られてなかったのですが、しっかり時間を作って参加できるようにしてこうと強く思った勉強会でした。
ありがとうございました！
