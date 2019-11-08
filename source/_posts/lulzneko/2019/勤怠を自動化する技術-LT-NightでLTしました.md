---
title: 勤怠を自動化する技術 LT Nightで、Web-NFC の PWA について LT しました
permalink: made-lt-about-Attendance-tec
date: 2019-07-20
author: lulzneko
categories: プレゼンテーション
tags:
- JAMstack
- PWA
- Nuxt.js
---

2019年7月19日に開催された「勤怠を自動化する技術 - LT Night」で LT ＆ 新作アプリのデモをしました。発表のサマリーです。

![](/articles/assets/lulzneko/develop/develop.jpg)


イベントの説明にあります通り、すべてはココから始まりました。
<blockquote class="twitter-tweet"><p lang="ja" dir="ltr">ソフトウェアエンジニアめっちゃ集めて「勤怠を自動化する技術」に絞ったLT大会やりたい</p>&mdash; ばんくし (@vaaaaanquish) <a href="https://twitter.com/vaaaaanquish/status/1138981053660483584?ref_src=twsrc%5Etfw">June 13, 2019</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

見かけた際に即 Like し、RT。開催されるだろうかと、ワクワクしてました。勤怠時間の管理については言いたいこともあれば、やってみたいこともある！そう思いながら日々過ごしてたら、ほんとに開催されました [「勤怠を自動化する技術」LT Night - connpass](https://connpass.com/event/135463/)。

即刻、参加に申し込むも絶望的な順位(皆さん、早！)。どうしても参加したかったので、空いていた LT 枠に切り替えました。もともと勤怠の時間管理のためにアプリを作るつもりでしが、LT 枠のために前倒し。かなり無茶なスケジュールでの参戦となりましたが、結果として発表できてよかったです。素晴らしい機会に感謝です。ありがとうございます！



## 発表資料
『Web-NFC の PWA で 簡単タイムレコーダー「ツカエタルヒの記録」』の発表資料はこちらになります。
<div class="slide"><iframe src="https://riotz.works/slides/2019-attendance-tech-lt"></iframe></div>


## サマリー
以下の内容で構成しました。
- 私の現在の勤怠管理
- 「ツカエタルヒの記録」紹介


### 私の現在の勤怠管理
簡単に言ってしまうと、毎日 Excel に手入力して、翌月初に上司のサインをもらうという仕組みです。ただし途中で「印刷、サイン、PDF化」が繰り返されるのでかなり大変。もちろん、人手によっているのでトラブルが多発します。
![](/articles/assets/lulzneko/presentation/attendance-lt/01-001.png)

これは、話しててヤバいなと思いつつ、参加者さんからもヤバい旨のツイートがありました💦

<blockquote class="twitter-tweet"><p lang="ja" dir="ltr">ハンコリレー案件だ！！！！ <a href="https://twitter.com/hashtag/kintai_auto?src=hash&amp;ref_src=twsrc%5Etfw">#kintai_auto</a></p>&mdash; 友尾 鏡沖 (@ymrl) <a href="https://twitter.com/ymrl/status/1152172144291303424?ref_src=twsrc%5Etfw">July 19, 2019</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

<blockquote class="twitter-tweet"><p lang="ja" dir="ltr">Excel印刷ハンコ勤怠存在するのか…すごい… <a href="https://twitter.com/hashtag/kintai_auto?src=hash&amp;ref_src=twsrc%5Etfw">#kintai_auto</a></p>&mdash; ばんくし (@vaaaaanquish) <a href="https://twitter.com/vaaaaanquish/status/1152172242307915777?ref_src=twsrc%5Etfw">July 19, 2019</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

<blockquote class="twitter-tweet"><p lang="ja" dir="ltr">古の兵器pdfメールリレーという勤怠が発生！いやぁ面白い。時代って古いんだね<a href="https://twitter.com/hashtag/kintai_auto?src=hash&amp;ref_src=twsrc%5Etfw">#kintai_auto</a></p>&mdash; KBにゃす (@kabanyasu) <a href="https://twitter.com/kabanyasu/status/1152172314349330432?ref_src=twsrc%5Etfw">July 19, 2019</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

<blockquote class="twitter-tweet"><p lang="ja" dir="ltr">見てきた中で一番シビアな勤怠してる <a href="https://twitter.com/hashtag/kintai_auto?src=hash&amp;ref_src=twsrc%5Etfw">#kintai_auto</a></p>&mdash; asatake (@asataken) <a href="https://twitter.com/asataken/status/1152172387737063424?ref_src=twsrc%5Etfw">July 19, 2019</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

<blockquote class="twitter-tweet"><p lang="ja" dir="ltr">打刻情報のExcel化、PDF化、印刷・・・システムへ手入力うげぇ・・・ <a href="https://twitter.com/hashtag/kintai_auto?src=hash&amp;ref_src=twsrc%5Etfw">#kintai_auto</a></p>&mdash; HayaUp@転職活動マン＆とある案件の下準備マン (@_HayaUp) <a href="https://twitter.com/_HayaUp/status/1152172413234233344?ref_src=twsrc%5Etfw">July 19, 2019</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

<blockquote class="twitter-tweet"><p lang="ja" dir="ltr">すごいものを見ている･･･<a href="https://twitter.com/hashtag/kintai_auto?src=hash&amp;ref_src=twsrc%5Etfw">#kintai_auto</a></p>&mdash; のりしお (@norisioo) <a href="https://twitter.com/norisioo/status/1152172502845554688?ref_src=twsrc%5Etfw">July 19, 2019</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>


### 「ツカエタルヒの記録」紹介
流石に Excel 管理の仕組み自体は変えられないので、毎日の手入力を支援するツールを自作しました。（本 LT に向けて、３日間の夜なべ仕事だったので、公開に向けてはリファクタリングしないとなので、公開は今しばらくお待ちください。）

![](/articles/assets/lulzneko/presentation/attendance-lt/01-002.png)

[Web NFC API](https://w3c.github.io/web-nfc/) を使った PWA です。PWA なので、ブラウザで動作し、またアプリとしてインストールすることもできます。

市販の NFC Tag へアプリで使うデータを書き込むことで、スマホをかざした際に出退勤の時刻を記録します。(Suica などの FeliCa 系は読み書きできません)
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://amazon.co.jp/dp/B018HX4N8G" data-iframely-url="//cdn.iframe.ly/UhVvvNZ"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>

せっかくなので「ログインボーナス」「月曜日ボーナス」機能も導入しました。今回のデモ用には [るるてあ (@k_r_r_l_l_)](https://twitter.com/k_r_r_l_l_) さんの「肯定ペンギン」を使わせていただきました。（アプリ公開に当たっては何か別のにしないとですかね。"イラストのアイコン・ヘッダー使用はご自由にどうぞ。画像の商用利用はお控えください。 - [るるてあ (@k_r_r_l_l_) / Twitter](https://twitter.com/k_r_r_l_l_)" なので大丈夫な気もしますが使わせていただくとなると確認はしないと）

Web NFC は 2019年7月現在、Android の Chrome のみ対応しており iPhone では利用できません。その場合は画面右上の [⏰] アイコンから記録できます(それ以前にアプリを使っていただくことはなさそうですが)

今後としては、まずはリファクタリングして公開を急ぎます。また現在はブラウザのローカルストレージにしかデータを保存していないので、Google スプレッドシートへ保管するようにしたいです。これがないと Excel へ転記するのが大変。また、今回のイベントの会場＆懇親会の提供をしてくださった Freee さんの [人事労務API 概要 | freee Developers Community](https://developer.freee.co.jp/docs/hr) などにも対応してみたいです。

なお「ツカエタルヒ」は [上日(ジョウジツ)とは - コトバンク](https://kotobank.jp/word/%E4%B8%8A%E6%97%A5-531802) の "日本古代の律令官人の出勤日数。〈つかえたるひ〉ともよむ。" から付けました。なんと出勤日数の管理および評価は平安時代から変わってなさそうな感じ。(しかも夜の会議と称した酒盛りとか、日勤15の夜勤５で20時間労働のブラック - [平安貴族のお仕事、実はかなりブラックな労働環境だった？ - ZAK女 - ZAKZAK](https://www.zakzak.co.jp/zakjyo/watcher/news/20170524/wat1705242010004-n1.htm) とか、1000年以上も同じことを！？ヤバい)


## 所感

### ログインボーナスについて
ログインボーナスについては、ぜひ拡張を続けて何かおもしろいことができたらと思います。

スポンサーセッションの [勤怠打刻の基礎知識](https://speakerdeck.com/ymrl/qin-dai-da-ke-falseji-chu-zhi-shi) で勉強させて頂いた通り、勤怠管理の仕組み自体を作るのは法律の知識をはじめ、さまざまな勤務形態に対応する必要があります。その部分はやはり専門の SaaS にお任せするのがよいでしょう。

一方でクライアント部分は、もっと自由な発想があってもよいと思います。むしろ、そこが足りてないから入力するのを嫌がられたりするわけです。その嫌われに対処するには、大きく２つあると思っています。

１つは完全に透過してしまい意識させないこと。これは入館ゲート(駅の改札みたいなやつ)を使ったり、GPS や WiFi 使ったりと、まさに自動化する仕組みです。もっとも、今回の会場の Freee さんみたいにオフィスに卓球台があったり、バーのある会社さんもあったりと最近は「会社にいる＝仕事している」とは限らない状況もあるので難しいところです。

もう１つは、楽しくすること。使いたくすること。そのためにログインボーナスを作ってみました。効果があるか、まだ何とも言えないですが。また画像が出るだけでは楽しくないので、もっと何か良い仕組みを考えたいと思います。ここはクライアント部分だけで作れるので、アイデアの出しどころですし、作りがいのあるところだと思います。


### ハッカソン、ワークショップについて
今回さまざまな発表を聞かせていただく中で、皆さん色々なものを作っているなぁと思いました。

せっかく今回のように色々なものを作る人が集まるのだったらハッカソンとかにしてもおもしろいだろうなぁと。

次回移行も Freee さんが開催してくださるかはわかりませんが、せっかく API を公開されているので、ワークショップにしたり、API 利用を前提としてのハッカソンなどがあると盛り上がりそうです。



----

いろいろなイベントで発表させていただいてきたのですが、LT は初めてで時間の制約を気にして緊張しました。限られた時間の中で精一杯話すのはとても楽しく、また次の機会に LT できたらと思います。

「勤怠を自動化する技術」ならぬ「勤怠を楽しくする技術」については、あと２つほどネタがあるので、ぜひ第２回、３回と会が連なり、そしてハッカソンなりと盛り上がると嬉しいです。
