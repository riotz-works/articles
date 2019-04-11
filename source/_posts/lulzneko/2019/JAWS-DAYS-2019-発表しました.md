---
title: JAWS DAYS 2019 で AWS x JAMStack な サーバーレス Web Front について発表をしました
permalink: made-presentation-about-jamstack-with-aws-at-jawsdays2019
date: 2019-03-01
author: lulzneko
categories: プレゼンテーション
tags:
- JAWS DAYS
- JAMStack
- Gridsome
- Nuxt.js
- Hexo
- Serverless Framework
---

![](/articles/assets/lulzneko/presentation/jawsdays-2019/01.png)

2019年2月23日 に 開催された JAWS DAYS 2019 で『AWS x JAMStack で構築・運用する サーバーレスな Web Front』と題して JAMStack と サーバレス Web Front に まつわる発表をしました。そのサマリーです。

**シリーズの記事**
- **JAWS DAYS 2019 で AWS x JAMStack な サーバーレス Web Front について発表をしました** (本記事)
- [JAWS DAYS 2019 で 頂いた QA まとめ](/articles/2019/02/24/summary-of-qa-at-jawsdays2019/)
- [発表者は、その日何をしていたのか - 発表の舞台裏 JAWS DAYS 2019 編](/articles/2019/02/23/backstage-of-presentation-at-jawsdays2019/)


## JAWS DAYS 概要
発表したイベント [JAWS DAYS 2019](https://jawsdays2019.jaws-ug.jp/) ですが、AWS の ユーザーコミュニティ [JAWS-UG](https://jaws-ug.jp/) 主催、後援 アマゾン ウェブ サービス ジャパン株式会社さん で 行われる JAWS-UG 最大のイベントです。

場所は TOC五反田メッセ で、様々なイベントや展示会などが開催される場所です。

そして各開催にはテーマが設定されます。今回は「満漢全席」です。

![](/articles/assets/lulzneko/presentation/jawsdays-2019/02.png)

AWS には たくさんの機能があります。そしてフロントエンドにもたくさんの技術があります。

それらの中から最近注目を集めている JAMStack を 中心に、**選りすぐりの技術を集めた** 素晴らしいアーキテクチャをご紹介したい。

満漢全席なイベントに、１つのコース料理としての満漢全席の皿（アーキテクチャ）を詰め込みお伝えしたい、そんな思いから『AWS x JAMStack で構築・運用する サーバーレスな Web Front』の 発表となりました。

タイトルこそ満漢全席は含まれていませんが、アーキテクチャの表現として盛り込みました。

"お残しはあきまへんぇ〜"


## 発表資料 と Togetter
<iframe src="https://riotz.works/slides/?2019-jaws-days" width="608" height="342"></iframe>

[2019/02/23(土) JAWS DAYS 2019 <7> 15:10～ #jawsug #jawsdays - Togetter](https://togetter.com/li/1322142)
沢山のツイートありがとうございます！！

[JAWS DAYS 2019 で 頂いた QA まとめ](/articles/2019/02/24/summary-of-qa-at-jawsdays2019/)
QA・ディスカス ありがとうございます！！


## サマリー
発表の主旨は「JAMStack、サーバレス Web Front を 広めよう」です。

主にサーバサイドを担当することが多く、サーバーレスなサーバサイド開発にこだわって行く中で、フロントサイドが (広義の) SSR したいという相談があり、そのたびに「インスタンスは持ちたくないです」「ホントにサーバ側で HTML 作る必要がありますか？」と 議論することがあり、結局 SSR 必要なかった ということが多々ありました。（もちろん発表の通り SEO x CGM とか SSR 必要な場合は SSR します）

サーバーレスを専門でやっているので「フロントもサーバーレスで」というキーワードを使うこともあるのですが、今一つ伝わりにくかった。どうしたら伝わるのか、何か共通のキーワードはないのか、そう思っているさなかに JAMStack が 浮上してきました。

JAMStack は HTML を 事前ビルドし CDN へ 配置するので、まさにサーバーレスになります！
これが共通の言葉として認知され、定着されれば フロントサイド、サーバサイド ともに サーバレスで作れます。
これはサーバーレス推進派としては広めねばとのことをお話しするに至った次第です。
「JAMStack、サーバレス Web Front」として、サーバレス界隈、フロントエンド界隈 の 盛り上がりに貢献できたら嬉しいです。

大きく３つのテーマでお話ししました。
1. JAMStack とは
2. AWS に おける JAMStack の 配置
3. JAMStack の 可能性

### JAMStack とは
まずは、公式サイト [https://jamstack.org](https://jamstack.org/) および 提唱者 [@biilmann](https://twitter.com/biilmann) さん の [The JAM Stack](https://speakerdeck.com/biilmann/the-jam-stack) を 中心に JAMStack そのものについて説明をしました。ご存知の方も少なからずいらっしゃるかと思ったのですが、JAMStack の 概念、メリット を しっかりお伝えした上で活用をお話ししたかったものになります。

![](/articles/assets/lulzneko/presentation/jawsdays-2019/20.png)
ほぼ全ては このスライドに集約されます。

JAMStack の 定義
- **J**avaScript of client side、Web API 呼出しはクライアントで行う
- **A**PIs of resusable、再利用可能な Web API で データを提供する
- **M**arkup of prebuilt、HTML は 事前構築済みで CDN に 配置する

また JAMStack な サイトを構築するには SSG(Static Site Generator) を 使います。
その SSG が リストされているのが StaticGen [https://www.staticgen.com](https://www.staticgen.com/) です。
こちらから好みの技術や使い勝手などを試して使うとよいでしょう。

おすすめとしては以下でしょうか（すごく個人的な主観です）
- [GatsbyJS](https://www.gatsbyjs.org/):  JAMStack の コンテキストでも一番登場しますし、一番成熟していると思います
- [Hexo](https://hexo.io/):               手ごろで使いやすい静的なブログ構築ツール、JAMStack を 謳ってないが JAMStack
- [Gridsome](https://gridsome.org/):      まだまだ開発中で上級者向けになりますが、開発の過程を一緒に楽しめます
- [Nuxt.js](https://nuxtjs.org/):  アプリのフレームとして使うのに向いています

※ 終盤でお話ししましたが、Riotz.works では 2019年2月現在、[ウェブサイト](https://riotz.works/) が [Gridsome](https://gridsome.org/)、[ラップアプリ](https://riotz.works/rap-tap-app/) が [Nuxt.js](https://nuxtjs.org/)、[本投稿](https://riotz.works/articles/) が [Hexo](https://hexo.io/) です。システム構成を見直しているので変わるかもしれません。


### AWS に おける JAMStack の 配置
JAMStack の 定義 や ベストプラクティス は AWS の サービスで賄うことができます。
ホントに "AWS と JAMStack は 相性が良い！" と いえるでしょう。

そして JAWS DAYS 2019 テーマ「満漢全席」に なぞらえて、選りすぐりの技術で作られたアーキテクチャ例です。

![](/articles/assets/lulzneko/presentation/jawsdays-2019/21.png)
すべて AWS で 固めたイメージです。（[Serverless Framework](https://serverless.com/) が 入ってますがツール部分なのでってことで）
この構成でよいのですが、右下の開発者周りはもう少し落とし込んで。

![](/articles/assets/lulzneko/presentation/jawsdays-2019/12.png)
"俺の" 構成。料理だけにレストランを展開されているネーミングをモチーフにしました。（書きながら本当にあってもよさそう）
やはり [GitHub](https://github.com/), [CircleCI](https://circleci.com/), [Slack](https://slack.com/) を 使うケースが多いのではないでしょうか。
アプリは [Nuxt.js](https://nuxtjs.org/) で SPA の Generate を 使うと作りやすいです。[Nuxt.js](https://nuxtjs.org/) は SSR もできますが JAMStack なので Generate になります。

[Dependabot](https://dependabot.com/) は リポジトリの Node.js の `package.json` や Java/Maven `pom.xml` を 解析し、依存している外部ライブラリのバージョンアップをチェックし、バージョンアップしてたらプルリクエストを作ってくれます。

その他ブログなどの情報発信用 の 満漢ミニ席 や WordPress リニューアル 席 と ネーミングが苦しいアーキテクチャを紹介しました。
どれも選りすぐりの技術を活用した一皿となっています。

満漢ミニ席は [Riotz.works の ウェブサイト](https://riotz.works/) で 使っているパターンです。
これまでの開発経緯から GitHub Pages に ホスティングしている部分が一部異なります。（始まりは手書きHTMLだったので）

WordPress リニューアル 席 は [Nuxt.js](https://nuxtjs.org/) で 作ったパターンがあります。
[GatsbyJS](https://www.gatsbyjs.org/) や [Gridsome](https://gridsome.org/) は WordPress に 対応しているので、単純リニューアルでしたら そちらを使ったほうが良いでしょう。

これらのように、様々なパターンで JAMStack と AWS は 活用できます。


### JAMStack の 可能性
一番向いているのは「情報発信サイト」です。
公式サイト、ブログ、ドキュメント など、特定管理下で情報を追加していくようなユースケースはとても相性が良いです。
[GatsbyJS](https://www.gatsbyjs.org/)、[Hexo](https://hexo.io/)、VuePress、[Gridsome](https://gridsome.org/) といったツールが使われます。

次に「ウェブアプリ の フレーム」です。
こちらも最近話題の PWA(Progressive Web Apps) のようなアプリ や ウェブアプリ の フレームとして使たりします。
動的なデータは Web API 経由で取得しますが、可能な限り構築済みの HTML としてフレーム化します。
例えば ラップ・バトル の 『[ラップ、タップ、アップ 🎶](https://riotz.works/rap-tap-app/)』は [Nuxt.js](https://nuxtjs.org/) で SSG した PWA です。
Next.js、[Nuxt.js](https://nuxtjs.org/) が 使われます。

一方で「**SEO 重視 で CGM(Consumer Generated Media) や 変化の激しいコンテンツ**」には **向かない** です。
JAMStack は HTML を 事前ビルドするので、変化の激しいコンテンツには処理が追い付かず向きません。
そうなるとフレームだけ JAMStack にし コンテンツ は Web API 経由にすることになりますが、SEO 関連のヘッダーは HTML の 必要があります。HTML にするには JAMStack のように事前ビルドするか SSR(Server Side Rendering) に する必要があります。


### まとめ
![](/articles/assets/lulzneko/presentation/jawsdays-2019/22.png)


## エゴサ
まとめ、参加録、フィードバック、いろいろな記事 ありがとうございます🙏　とても嬉しいです😆

[2019/02/23(土) JAWS DAYS 2019 <7> 15:10～ #jawsug #jawsdays - Togetter](https://togetter.com/li/1322142)
共感いただけたり、的確なコメント、フィードバック 様々なツイートをいただき嬉しいです！！

[JAWS DAYS 2019に行ってきた - せてぃーずノート](http://setys.hatenablog.com/entry/2019/02/25/185640)
Dependabot と Java についても触れてくださっています。ありがとうございます！

[JAWS DAYS 2019参加レポ | NologoyAnce.net](https://nologyance.net/2019/02/24/post-657/)
参加されたセッションをシンプルにまとめていただきました。ありがとうございます！

[JAWSUG2019に初参加してきました - プログレッシブ・プロレタリアート](http://farewell-work.hatenablog.com/entry/2019/02/24/140853)
JAMStack が 流行るのか、確かに気になります。頑張って流行らせたいです。ありがとうございます！

[JAWS DAYS 2019手がきメモまとめ #jawsdays #jawsug｜kondoyuko｜note](https://note.mu/kondoyuko/n/n15fa080d94de)
ステキなグラレコでまとめてくださいました。ありがとうございます！
<blockquote class="twitter-tweet" data-lang="ja"><p lang="ja" dir="ltr">[Serverless] AWS x JAMStack で構築・運用するサーバーレスなWeb Front のメモです <a href="https://twitter.com/hashtag/jd2019_a?src=hash&amp;ref_src=twsrc%5Etfw">#jd2019_a</a> <a href="https://twitter.com/hashtag/jawsdays?src=hash&amp;ref_src=twsrc%5Etfw">#jawsdays</a> <a href="https://t.co/WbrGcQ062l">pic.twitter.com/WbrGcQ062l</a></p>&mdash; 近藤佑子🐱技術書典6 か66 (@kondoyuko) <a href="https://twitter.com/kondoyuko/status/1099195738116870144?ref_src=twsrc%5Etfw">2019年2月23日</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

できる限り探してまとめさせていただきました。
もし入ってなかったり、新たに投稿いただいたなどありましたら Twitter [@lulzneko](https://twitter.com/lulzneko) へ DM や メンションいただけたら幸いです。

----

JAWS DAYS で 発表させていただくのは初めてで、とても良い経験をさせていただきました。
オープンな会場と小さめのマイクの音に、いつもの雰囲気が違うので上手く話ができるのか不安と緊張が入り混じった感じで発表に入りました。出だしで噛んでしまったところから吹っ切れた感もでて、勘が戻ってきて話ができたと思います。

最後になりますが、セッションにご参加いただいた皆さま、万事支援してくださったスタッフの皆さま、そして貴重な機会を作っていただいた運営の皆さま に 感謝です！ ありがとうございす！！
