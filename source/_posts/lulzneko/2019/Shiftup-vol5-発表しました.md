---
title: Shiftup! JP_Getshifter Vol5！ にて「Shifter ＋ SSG の世界」について発表をしました
permalink: made-presentation-about-shifter-with-ssg-at-shiftup-vol5
date: 2019-08-21
author: lulzneko
categories: プレゼンテーション
tags:
- JAMStack
- Gridsome
- Nuxt.js
- Shifter
- WordPress
---

2019年8月7日に開催された「Shiftup! JP_Getshifter Vol5 暑気払い！WordPressとJAMstackの夕べ」で『Shifter ＋ SSG(Static Site Generator) が生み出す、新しい WordPress の世界』と題して、WordPress の新しい世界について発表をしました。そのサマリーです。

![](/articles/assets/lulzneko/seminar/shifter/shifter.png)


第３回のイベント [Shiftup! JP_Getshifter Vol3！ で JAMStack な サーバーレス ウェブフロント に ついて発表](https://riotz.works/articles/lulzneko/2019/04/05/made-presentation-about-jamstack-at-shiftup-vol3/) をした際に、[Shifter](https://www.getshifter.io/japanese/) から SSG(Static Site Generator) でサイト生成できたらよいという話で盛り上がり、[Shiftup! JP_Getshifter Vol3！ 振り返り、Shifter の HeadlessCMS 化に思いを馳せる](https://riotz.works/articles/lulzneko/2019/04/06/think-of-shifters-headlesscms-nize-on-shiftup-vol3) という記事を書きました。

そしたら Shifter さんの神対応があり「[Shifter Webhooksで Netlify上のGatsbyサイトにWordPressコンテンツをインポート可能になりました - 株式会社デジタルキューブ](https://www.digitalcube.jp/shifter/4434/)」というアップデートが即だされました。Shifter スゴイ！

その流れから、Shifter で SSG が使えると何が嬉しいのか、どんな世界が描けるのか、当初考えていたことを整理し発表しました。

**シリーズの記事**
- **Shiftup! JP_Getshifter Vol5！ にて「Shifter ＋ SSG の世界」について発表をしました** (本記事)
- [Shiftup! JP_Getshifter Vol3！ で JAMStack な サーバーレス ウェブフロント に ついて発表をしました](https://riotz.works/articles/lulzneko/2019/04/05/made-presentation-about-jamstack-at-shiftup-vol3/)
- [Shiftup! JP_Getshifter Vol3！ 参加レポート](https://riotz.works/articles/lulzneko/2019/04/03/take-seminar-on-shiftup-vol3/)
- [Shiftup! JP_Getshifter Vol3！ 振り返り、Shifter の HeadlessCMS 化に思いを馳せる](https://riotz.works/articles/lulzneko/2019/04/06/think-of-shifters-headlesscms-nize-on-shiftup-vol3)


## "Shiftup! JP_Getshifter Vol5 暑気払い！WordPressとJAMstackの夕べ" 概要
発表したイベント [Shiftup! JP_Getshifter Vol5 暑気払い！WordPressとJAMstackの夕べ](https://eventregist.com/e/0if9nERXstQg) は、サーバーレスな WordPress ホスティング [Shifter](https://www.getshifter.io/japanese/) のユーザーコミュニティの勉強会です。

会場は決済サービスの [Stripe](https://stripe.com/ja-JP/) さん。原宿は竹下口交差点のステキなロケーション＆最高な眺めのオフィスです。ちょうど Stripe さんのセッション中に「神宮花火ナイター」の花火が上がり、絶好の眺めとなりました。花火が見えるオフィス、いいなぁ。


## 発表資料
『[Shifter ＋ SSG(Static Site Generator) が生み出す、新しい WordPress の世界](https://riotz.works/slides/2019-shiftup-jp_getshifter-vol5)』の発表資料はこちらになります。(下記、スライド埋め込み)
<div class="slide"><iframe src="https://riotz.works/slides/2019-shiftup-jp_getshifter-vol5"></iframe></div>



## サマリー
発表の主旨は「Shifter と SSG を使って、WordPress のウェブフロントの新しい考え方を作ろう」です。

おおきく３つのテーマで発表しました。
1. Shifter が もたらした、新世界
2. Shifter ＋ SSG が 生み出す、さらなる新世界
3. Shifter + SSG の 可能性

前半は Shifter の機能やメリットを振り返る内容で、後半から SSG と組み合わせた場合の世界、メリットの話になります。


### Shifter が もたらした、新世界
まずは、WordPress と Shifter の違いについてです。WordPress は言わずと知れた世界でもっとも使われている CMS。ウェブサイトの 30% は WordPress と言われているぐらいだそうです。

そんな、すごい WordPress の世界ですが少し困り事も。WordPress サイトの立ち上げまでは、ある程度情報があるし楽しめるところもあります。しかしながら、セキュリティ対策は難しい面もありますし、なにより運用段階での各種バージョンアップ対応が大変。ウェブサイトなので、どうしても不特定多数からアクセスされるのでセキュリテ対策と継続的なバージョンアップは必須です。

そうした中、Shifter は "Serverless WordPress"、"JAMStack WordPress" という形で困り事に対処してくれます。

ひとつは、**Serverless WordPress** (または **Managed WordPress** や **WordPress SaaS**)。
Shifter へサインアップしてサイトを作るだけで WordPress の環境が構築される、そして構築された環境のバージョンアップなどのメンテは Shifter がやってくれる。これにより、どれだけの困り事がなくなるか。
![](/articles/assets/lulzneko/seminar/shifter/05-01.png)

もう１つは、**JAMStack WordPress**。
これも Shifter のスゴイところ。「[JAMstack 公式](https://jamstack.org/)」では、WordPress は JAMSstack ではない "A site built with a server-side CMS like WordPress, Drupal, Joomla, or Squarespace. - [JAMStack](https://jamstack.org/#what)" と言っています。しかしながら Shifter は JAMSstack として実現しています。
これによりパフォーマンスとスケーリングを実現し、WordPress として本体をさわる人を限定することでセキュリテ問題を局所化＆**Serverless WordPress**でセキュリティ対策をしています。
![](/articles/assets/lulzneko/seminar/shifter/05-02.png)


### Shifter ＋ SSG が 生み出す、さらなる新世界
ここまで「"至れり尽くせり" な WordPress = Shifter」ですが、あと１つ。ウェブフロントの開発を WordPress Theme だけでなく、何とかしたい。JAMSstack の技術にしたいというがあります。

それが Shiftup! JP_Getshifter Vol3！ で、参加者の皆さま、Shifter の中の人と、お話しさせていただいた「[Shifter の WordPress HeadlessCMS 化に思いを馳せる](https://riotz.works/articles/lulzneko/2019/04/06/think-of-shifters-headlesscms-nize-on-shiftup-vol3)」であり、神対応していただいた [Shifter Webhooks + SSG](https://www.digitalcube.jp/shifter/443) です！
![](/articles/assets/lulzneko/seminar/shifter/05-03.png)

こちらの機能が欲しかったのは、PHP の中に HTML/CSS/JavaScript を書くのが大変、つまり JAMSstack(の "Better Developer Experience") でない最後の困り事を解決したかった。もし JAMSstack 系の技術だけで WordPress のウェブフロントを作れたらすごいのではないか？そして、その先に？ということです。


### Shifter + SSG の 可能性
ウェブフロントの世界では JAMSstack という考えが広まりつつあります。詳しくは [JAMStack、それはハイパフォーマンスなウェブフロントを実現するアーキテクチャ](https://riotz.works/articles/lulzneko/2019/01/23/jamstack-an-architecture-to-realize-fine-web-front/) を、ご参照ください。

一方で JAMSstack なウェブサイトとはいえデータソースは必要です。JAMSstack が、一番得意なのは情報発信サイト。つまり WordPress のような CMS に投稿された情報発信するようなサイト。

CMS といえば？＝ WordPress、これまでの話の通り WordPress を使うなら Shifter のメリットを生かすとよいでしょう。そして、ウェブフロントを [Gridsome](https://gridsome.org/) や [GatsbyJS](https://www.gatsbyjs.org/) といった SSG を使って作る。これにより、ウェブ・フロントエンドの技術者が WordPress のサイトでありながら、ウェブ・フロントエンドの技術だけウェブサイトを実現できるようになります。新しい世界が作られ、新たな市場が生まれるといっても過言ではないでしょう。
![](/articles/assets/lulzneko/seminar/shifter/05-04.png)

これまでにも WordPress の Theme を SSG 系の技術(の要素技術といったほうが正しいかな)を使って作る方法はありましたが、SSG だけで作るケースは少なかったと思います。それは WordPress の REST API を使う必要があるからです。そうなると結局 WordPress を運用するので、あえて SSG を使うよりも WordPress の技術スタックで作るほうがよいです。

Shifter により WordPress の構築・運用から解放されることで WordPress ＋ SSG が急浮上します。むしろ通常のウェブフロントの開発と同じになります。純粋にウェブフロントだけを管理するようなモデルが生まれるのではないでしょうか。



----

Shifter + SSG、新しいウェブサイト開発の潮流になるのではないかと思っています。

SSG を使った JAMSstack サイトの開発としては、データの投入として Headless CMS を使うケースがあります。最近は Headless CMS である [Contentful](https://www.contentful.com/) などの名前を聞くケースが増えてきました。

新しいサービスはもちろんウェルカムなのですが、１つだけ困ることがあります。記事を書く人が「そのサービスを使い倒すことができるか？」です。

個人やエンジニアブログなどでしたらよいのですが、技術系ではない人が使うには難しかったり、情報が少なくて調べられない、知ってる人や使ってる人が少ないなどの問題が出てきます。その点で WordPress は No.1 CMS であり多くの情報や利用者がいます。このアドバンテージを生かしつつ、WordPress の構築・運用からは手離れ、ウェブフロントをモダンな技術で手早く作る。

まだ Shifter Webhooks ＋ SSG も出たばかりですし、これからだと思いますが新しい流れができたら、おもしろいですね。
