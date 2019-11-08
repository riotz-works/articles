---
title: CloudNative Days Tokyo 2019 にて「サーバレス・ネイティブ が お伝えする、フルサーバレス開発の魅力」の発表をしました
permalink: made-presentation-about-serverless-at-cloudnative-days-2019
date: 2019-07-26
author: lulzneko
categories: プレゼンテーション
tags:
---

2019年7月23日に開催された「CloudNative Days Tokyo 2019」で『サーバレス・ネイティブ が お伝えする、フルサーバレス開発の魅力』と題して、サーバーレスの魅力について発表しました。その発表サマリーです。

![](/articles/assets/lulzneko/presentation/cloudnative-days/cloudnative-days-tokyo-2019.png)


## "CloudNative Days" 概要
発表したイベント [CloudNative Days Tokyo 2019](https://cloudnativedays.jp/cndt2019/) は、元は ContainerDays というイベントで今年の４月から CloudNative Days と名称が変わりました。また今回は [OpenStack Days Tokyo 2019](https://openstackdays.com/) との共同開催です。４月の福岡をスタートに、７月の東京、11月の大阪と続きます。

会場は虎ノ門ヒルズフォーラム。はじめて行きました。地下鉄虎ノ門駅から細い歩道なので少し距離がある感じはしますが、新橋にも出られたりといい感じの場所です。


## 発表資料
『[サーバレス・ネイティブ が お伝えする、フルサーバレス開発の魅力](https://riotz.works/slides/2019-cloudnative-days/)』の発表資料はこちらになります。(下記、スライド埋め込み)
<div class="slide"><iframe src="https://riotz.works/slides/2019-cloudnative-days/"></iframe></div>


## サマリー
発表の主旨は「コンテナーもいいけど、サーバーレスもスゴクいいからね。ぜひ！」です。
大きく４つの観点からサーバーレスの魅力をお話しました。
- アイデアを即、形にできる 魅力
- アプリの開発に専念できる 魅力
- ピタゴラ装置を組み立てる 魅力
- JAMstack な サイト管理の 魅力


### アイデアを即、形にできる 魅力
モバイルアプリのハッカソンで、ラップバトルの動画中継と配信をするアプリを作ったのを例に、いかに高速でアプリが開発できるかを話しました。
![](/articles/assets/lulzneko/presentation/cloudnative-days/01-001.png)

動画の取り扱いとなると、かなり手間がかかるのですが [SkyWay - Enterprise Cloud WebRTC Platform](https://webrtc.ecl.ntt.com/) を使うことで容易に動画中継と配信を実装できました。

また対戦ルーム管理などは Lambda と DynamoDB で構築しています。Web API を簡単に構築できる基本的な構成になります。「👍」やチャットなどのリアルタイムのコミュニケーションは Fibrebase を使い、マルチクラウドの構成となっています。

クラウドがさまざまな機能を用意してくれ、また SaaS によって必要な機能を組み合わせられるので、24時間という限られた時間でも動画を扱うようなアプリを作ることができます。この高速な開発力はアイデアをすぐに形にできる、サーバーレスの魅力のひとつといえるでしょう。


### アプリの開発に専念できる 魅力
IoT 製品のバックエンドを構築した際に、実行ランタイム(= 開発言語) を切り替えるというケースがあったのを例に、その様な事態でもアプリの開発(コーディング)だけに注力できるという話をしました。
![](/articles/assets/lulzneko/presentation/cloudnative-days/01-002.png)

このプラットフォーム開発当時、Lambda の Java ランタイムは初期起動(コールドスタート)が遅いという問題がありました。それでも多くの場合、処理が遅いと感じるレベルで大問題にはならないのです。しかしながら私たちの場合はマイクロサービスとして、いくつもの Web API を相互呼出しする形になっていました。そのため初期スタートに当たる確率も高くなり、また初期スタートに当たるとリクエストがタイムアウトしてしまう問題へつながってしまいました。

いくつかあった解決策から実行ランタイムを Java から Node.js へ切り替えるという決断をします。開発言語が Java から JavaScript へ変わるのですが、TypeScript を導入することで Java チームとしても静的型付けによる親和性により切り替えに成功し、問題を解決しました。

実行ランタイムを変えるのはかなりの変更となりますが、AWS では監視やログなどが構成済みのため変更箇所はプログラムの再実装だけになります。ミドルウェアやスケールアウトさせるための仕組みなどを考える必要がないので、アプリ開発に専念できる魅力があります。

※ 補足
2019年７月現在、Lambda の起動が高速化され Java ランタイムのコールドスタートでも１秒ぐらいで起動しています。Java 実装が残っている部分で処理速度の確認があり気づきました。簡易で調べた結果なので、きちんとした検証は必要ですが、かなり改善されたのではないでしょうか。
またサービス提供者が性能を向上させてくれることで、システムのパフォーマンスが自動的によくなるメリットもあるとも言えるでしょう。


### ピタゴラ装置を組み立てる 魅力
クラウドの機能や SaaS の機能を組み合わせることを「ピタゴラ装置」に見立てて、お話しました。"ピタゴラ装置は NHK Eテレの番組『ピタゴラスイッチ』に登場するからくり装置 - [Wikipedia](https://ja.wikipedia.org/wiki/%E3%83%94%E3%82%BF%E3%82%B4%E3%83%A9%E8%A3%85%E7%BD%AE)" です。最初のきっかけ以降は運動エネルギーを引き継いで流れていくもので、いわゆるドミノの複雑版といった感じです。

サーバーレスのイベント駆動でつながる処理がまさにピタゴラ装置的で、最初のイベントを受けて以降、次々と処理が流れていきます。たとえばラップバトルのピタゴラ装置は以下です。
![](/articles/assets/lulzneko/presentation/cloudnative-days/01-003.png)

スマホアプリからルームの作成処理を API Gateway へリクエストします。API Gateway から Lambda が起動し、DynamoDB へルームのデータを登録します。
続いて通知を送るのですが、よくあるのが Lambda で「DB登録 ⇒ 通知」と処理を続けること。しかしイベント駆動で流すので、ここは DynamoDB Streams を使ってデータ登録のイベントから Lambda を起動して通知の処理をします。

このような形でクラウドの機能や SaaS を活用してアプリやシステムを組み立てていきます。これにより実装がシンプルになります。さまざまな機能を組み合わせつつ、シンプルな作りをする。これは楽しくもあり、多くのメリットを享受できる魅力とも言えます。


### JAMstack な サイト管理の 魅力
JAMstack、最近 話題になりつつあり、フロントエンドやウェブの開発などで見聞きする機会が多いキーワードです。

サーバーサイドをサーバーレスで構築していても、フロントのためにウェブサーバー(動的な処理ができるサーバー)を用意するケースが多く、どうしてもシステム全体でサーバーレスにできないケースがあり、最近は JAMstack を推進しています。

JAMstack を簡単に言ってしまうと、HTML は事前生成してリクエスト時には動的に生成しない。動的要素はブラウザ(クライアントサイド)の JavaScript で Web API 等を呼び出して作るようにするということです。
![](/articles/assets/lulzneko/presentation/cloudnative-days/01-004.png)

これにより、HTML は CDN に配置されウェブサーバーは必要としません。フロントを含めてサーバーレスにできます。サーバーレスであれば JAMstack でなくともよいのですが「フロントもサーバーレスで」というより「JAMstack で」といったほうがわかりやすいので、そのように言っています。

また JAMstack という観点からは、Serverless WordPress の [Shifter](https://www.getshifter.io/japanese/) という素晴らしいサービスがあるので紹介します。
![](/articles/assets/lulzneko/presentation/cloudnative-days/01-005.png)

利用者としては、Shifter で WordPress を起動してコンテンツを編集。コンテンツの編集が完了したらサイト生成して、コンシューマー向けの静的サイトを作り、そちらへアクセスしてもらいます。これにより WordPress 本体へアクセスされないのでセキュリティを高められますし、静的サイトなので高速です。まさに JAMstack の思想を実現したサービスといえます。

コンテンツ管理はさまざまなサービスがあり、最近では「ヘッドレス CMS」なども登場していますが、やはり WordPress の使いやすいさ、そして認知度の高さは外せません。コンテンツ管理の仕組みが必要な場合は検討したいサービスです。

8/7(水) には、Shifter と Static Site Generator を組み合わせた、WordPress の新しい活用についてお話しますので、良かったらご参加ください。
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://eventregist.com/e/0if9nERXstQg" data-iframely-url="//cdn.iframe.ly/K37jM7r"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>


### まとめ
![](/articles/assets/lulzneko/presentation/cloudnative-days/01-006.png)

サーバーレスに興味を持っていただけましたら、ぜひ 10月の ServerlessDays Tokyo へお越しください。国内最大のサーバーレスのイベントです。
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://tokyo.serverlessdays.io/" data-iframely-url="//cdn.iframe.ly/LSdMVaB?iframe=card-small"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>



----

元が ContainerDays という名前の通り、Docker や Kubernetes のセッションが多い構成の中、「サーバレス・ネイティブ」だの「フルサーバレス」というかなりイキり気味のセッションを採用いただけ、そして当日も空いているのが数えられるくらいの数席を、満席でのご参加いただきありがとうございます！

サーバーレスへの関心につながりましたら幸いです。
