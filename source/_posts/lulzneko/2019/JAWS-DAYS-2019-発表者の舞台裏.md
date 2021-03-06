---
title: 発表者は、その日何をしていたのか - 発表の舞台裏 JAWS DAYS 2019 編
permalink: backstage-of-presentation-at-jawsdays2019
alias: /2019/02/23/backstage-of-presentation-at-jawsdays2019/index.html
date: 2019-02-23
author: lulzneko
categories: プレゼンテーション
tags:
- JAWS DAYS
---

![](/articles/assets/lulzneko/presentation/jawsdays-2019/01.png)

----

新しい朝が来た希望の朝だ、**発表**の朝だ！

JAWS DAYS での発表の朝を迎えて、発表者 lulzneko は絶望していた。
なんと発表資料が完成していないのです 😱

----

2019年2月23日に開催された JAWS DAYS 2019 で『AWS x JAMstack で構築・運用するサーバーレスな Web Front』と題して JAMstack にまつわる発表をしました。その発表の舞台裏ということで、発表者が当日何をしていたのかを綴ります。

**シリーズの記事**
- [JAWS DAYS 2019 で AWS x JAMstack なサーバーレス Web Front について発表をしました](https://riotz.works/articles/lulzneko/2019/03/01/made-presentation-about-JAMstack-with-aws-at-jawsdays2019/)
- [JAWS DAYS 2019 で頂いた QA まとめ](https://riotz.works/articles/lulzneko/2019/02/24/summary-of-qa-at-jawsdays2019/)
- **発表者は、その日何をしていたのか - 発表の舞台裏 JAWS DAYS 2019 編**（本記事）


## JAWS DAYS 概要
発表したイベント [JAWS DAYS 2019](https://jawsdays2019.jaws-ug.jp/) ですが、AWS のユーザーコミュニティ [JAWS-UG](https://jaws-ug.jp/) 主催、後援 アマゾン ウェブ サービス ジャパン株式会社さん で 行われる JAWS-UG 最大のイベントです。

場所は TOC五反田メッセで、さまざまなイベントや展示会などが開催される場所です。


## 会場入り
ストーリーとスライドの配置は完成していて、そこに載せる図 [AWS における JAMstack の配置](https://riotz.works/slides/2019-jaws-days#18) の章で使うものが間に合ってなかったレベルなのですが、慌てて PowerPoint を操作する朝から始まりました。
なんか、前回の [デブサミ舞台裏](https://riotz.works/articles/lulzneko/2019/02/15/backstage-of-presentation-at-devsumi-2019-winter/) でも同じことを書いた気がします。

なお、前日の 22日には発表用資料をうっかり消し飛ばす事故を起こしました。
慌てるとよくないことがあるので、急ぎつつも作業ミスをしないのが大事です。
<blockquote class="twitter-tweet" data-lang="ja"><p lang="ja" dir="ltr">何を血迷ったのか、明日の発表資料をうっかり消し飛ばす💦<br>git の reflog に しっかり残ってた、あぶねー ＆ Git 最高！</p>&mdash; lulzneko (@lulzneko) <a href="https://twitter.com/lulzneko/status/1098850301816188929?ref_src=twsrc%5Etfw">2019年2月22日</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>


急いでスライドを完成させ、所用を済ませと、あわただしく過ごして午後一の会場入りをしました。
資料の都合というより所用があって午前入りできなかったのですが、午前中のセッションも聴きたかった。

会場である TOC五反田メッセは、本館とは別の建物になります。
"TOC" だけで考えていると、うっかり本館へ行ってしまい、結構な回り道を歩くことになります。
そうです、うっかり本館へ行ってしまいました。微妙な場所からの移動だったのでタクシーさんに乗せてもらったのですが "メッセ" が伝わってなかったようで、がっつり本館へ。あまり東京の道に慣れてないとのことでしたので、結構な周り道を徒歩で堪能させていただきました。
![](/articles/assets/lulzneko/presentation/jawsdays-2019/02.png)


## 受付
無事会場入りできたので次は受付です。

イベント開催用の建物なので、入ってすぐに受付になります。
手前に参加者の受付があり、少し進んだところに発表者の受付があります。
クロークもあるので、モコモコのジャケットを預けられたのが嬉しいです。

受付で Doorkeeper のチェックインをします。QR コードを表示して読み取ってもらいます。
続いて自分の名前が書かれてるスピーカーパスをもらい、印刷したアイコンの切り紙を差し込みます。（写真では名前部分が隠れてますが、当日はアイコン画像を端において名前は見えるようにしています）
![](/articles/assets/lulzneko/presentation/jawsdays-2019/03.jpg)


受付が終わると、発表時間までフリーなのでセッションを楽しんだり、控室で作業したりできます。
![](/articles/assets/lulzneko/presentation/jawsdays-2019/04.jpg)


## 発表 10分前
前の発表が終わり、落ち着いたところで演台へ近づき発表準備に入ります。

進行の方から名前等の最終確認を受けます。

そして、電源の確認。
今回はちゃんと持っていきました！（持って帰るのを忘れました💦）

発表者サポートは、進行の方が残り時間をボードで教えてくれます。
10分、5分、3分。しっかり出してくれるので助かります。

マイクはワイヤードで、演台にセットされています。持っているほうが安定するので、外して持つようにしました。
会場は各セッションごとの個室ではなく、会場の後ろ側が空いている＆壁が天井までつながっていない仕様のため、マイクの音が小さめでレシーバーで聞くようになっています。会場の後ろのほうで聞いていると音があまりしないので、話すときにどんな感じか心配しましたが話している分には側のスピーカーから聞こえるので違和感はなかったです。
![](/articles/assets/lulzneko/presentation/jawsdays-2019/05.jpg)

プロジェクターが大きく、普段は投影画像を直接指さすタイプなのですが、さすがに届かない感じです。そろそろマイ・レーザーポインターを用意したほうが良いと思いつつ、準備完了。

発表用スライドの URL をツイート。
<blockquote class="twitter-tweet" data-cards="hidden" data-lang="ja"><p lang="ja" dir="ltr">この後の JAWS DAYS 2019　Aトラック　15:10～<br>『[Serverless 前半] AWS x JAMstack で構築・運用するサーバーレスなWeb Front』の 資料はこちらになります。<a href="https://t.co/LLflOVhGSk">https://t.co/LLflOVhGSk</a><a href="https://twitter.com/hashtag/jawsug?src=hash&amp;ref_src=twsrc%5Etfw">#jawsug</a> <a href="https://twitter.com/hashtag/jawsdays?src=hash&amp;ref_src=twsrc%5Etfw">#jawsdays</a> <a href="https://twitter.com/hashtag/jd2019_a?src=hash&amp;ref_src=twsrc%5Etfw">#jd2019_a</a></p>&mdash; lulzneko (@lulzneko) <a href="https://twitter.com/lulzneko/status/1099187884748300288?ref_src=twsrc%5Etfw">2019年2月23日</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>



## 発表
いざ、発表！
ご清聴ありがとうございました！！
<div class="slide"><iframe src="https://riotz.works/slides/2019-jaws-days"></iframe></div>

たくさんのツイートありがとうございます！！
[2019/02/23(土) JAWS DAYS 2019 <7> 15:10～ #jawsug #jawsdays - Togetter](https://togetter.com/li/1322142)

[@kondoyuko](https://twitter.com/kondoyuko) さんに、グラレコを作っていただきました。ありがとうございます！
<blockquote class="twitter-tweet" data-lang="ja"><p lang="ja" dir="ltr">[Serverless] AWS x JAMstack で構築・運用するサーバーレスなWeb Front のメモです <a href="https://twitter.com/hashtag/jd2019_a?src=hash&amp;ref_src=twsrc%5Etfw">#jd2019_a</a> <a href="https://twitter.com/hashtag/jawsdays?src=hash&amp;ref_src=twsrc%5Etfw">#jawsdays</a> <a href="https://t.co/WbrGcQ062l">pic.twitter.com/WbrGcQ062l</a></p>&mdash; 近藤佑子🐱技術書典6 か66 (@kondoyuko) <a href="https://twitter.com/kondoyuko/status/1099195738116870144?ref_src=twsrc%5Etfw">2019年2月23日</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

※ QA は後ほどまとめます。


## 懇親会まで、徘徊
Ask the Speaker はないので終了。
発表用スライドの URL を再度ツイート。

<blockquote class="twitter-tweet" data-cards="hidden" data-lang="ja"><p lang="ja" dir="ltr">JAWS DAYS 2019 Aトラック 15:10<br>『[Serverless 前半] AWS x JAMstack で構築・運用するサーバーレスなWeb Front』ご清聴ありがとうございました。<br>資料はこちらになります。質問やフィードバックありましたら気軽にお声がけやDMいただけたらと。<a href="https://t.co/LLflOVhGSk">https://t.co/LLflOVhGSk</a><a href="https://twitter.com/hashtag/jawsug?src=hash&amp;ref_src=twsrc%5Etfw">#jawsug</a> <a href="https://twitter.com/hashtag/jawsdays?src=hash&amp;ref_src=twsrc%5Etfw">#jawsdays</a> <a href="https://twitter.com/hashtag/jd2019_a?src=hash&amp;ref_src=twsrc%5Etfw">#jd2019_a</a></p>&mdash; lulzneko (@lulzneko) <a href="https://twitter.com/lulzneko/status/1099197322246770688?ref_src=twsrc%5Etfw">2019年2月23日</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>


エゴサして、Like、RT、Follow－...

回れてなかった出展ブースを巡り、スタンプラリー
すべて回ったはずなのに、スタンプが１つ足りず。。。どこが足りないのかはわからない仕様なので諦める＆配布終了に間に合わず。残念。
（次の日になったらアプリで表示できなくなったのでキャプチャできず）


## 懇親会
すごい人数でした。🍻 楽しかったです！

<blockquote class="twitter-tweet" data-lang="ja"><p lang="ja" dir="ltr">過去最大の出席者となった JAWS DAYS 2019 が終了しました！<br><br>2010年にJAWS-UGが誕生して今日で9周年、そして明日からは10周年に向かっての第一歩✨<br><br>皆さま、次は全国の勉強会でお会いしましょう！😊<a href="https://twitter.com/hashtag/jawsug?src=hash&amp;ref_src=twsrc%5Etfw">#jawsug</a> <a href="https://twitter.com/hashtag/jawsdays?src=hash&amp;ref_src=twsrc%5Etfw">#jawsdays</a> <a href="https://t.co/OydnxsiB3H">pic.twitter.com/OydnxsiB3H</a></p>&mdash; AWS / アマゾンウェブサービス (@awscloud_jp) <a href="https://twitter.com/awscloud_jp/status/1099259719447863296?ref_src=twsrc%5Etfw">2019年2月23日</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>


----

以上、発表者の一日でした。
なんとか無事発表を終えることができました。

次回は、2019年3月9日(土)『[【CTOって本当にかけるの！？】ベンチャー６社のCTOチームと競う新規サービス立ち上げハッカソン](https://cto-samurai.connpass.com/event/120473/)』でハックしに行きます。
2/25日現在、まだまだ枠がある(8/30人)ようなので、よかったら一緒にハッカソンしましょう。

また、ちょっと来て話をしてよ、といったようなことなどありましたら、Twitter DM/メンションを [@lulzneko](https://twitter.com/lulzneko) へ気軽にいただければ幸いです。
これまでの発表歴は、こちら [Slides | Riotz.works] https://riotz.works/slides になります。

----

聴きに来てくださり、ありがとうございました。
スタッフの皆さま、支援ありがとうございました。

そして、ポエム的なものをここまで読んでくださり、ありがとうございます。
