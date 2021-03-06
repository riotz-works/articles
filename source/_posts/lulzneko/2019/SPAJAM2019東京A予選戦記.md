---
title: SPAJAM 2019 東京A予選 - ハッカソン戦記
permalink: spajam-2019-tokyo-a-pre-hackathon-chronicle
alias: /2019/05/20/spajam-2019-tokyo-a-pre-hackathon-chronicle/index.html
date: 2019-05-20
author: lulzneko
categories: ハッカソン
tags:
- SPAJAM
- Nuxt.js
- JAMstack
- AWS Lambda
---

モバイルアプリ・ハッカソンの SPAJAM 2019 東京A予選に参戦してきました。
２日間で開催され 24時間でのアプリ開発、本選をかけての熱いハッカソンの戦記です。

![](/articles/assets/lulzneko/hackathon/spajam-2019-pre/spajam.png)


いろいろなアウトプットがある中で、ハッカソンはとても好きです。
アイデア、企画設計、開発、テスト・デバッグ、プレゼンテーション、文章、チームワーク、判断に決断などと、とても多くのスキルを限られた時間の中で全力投入して形を作り上げ、それを強力なライバルたちと競い、そして打ち解けあう。

自分が発想をできないようなアイデアを見せてもらったり、新しい技術を教えてもらったり、同じ技術でも使い方使いこなしを目の当たりにしてと、多くのことを学ばせてもらえます。

また、自分自身のスキル点検というかテストを受けるよう気持ちもあります。アイデアは衰えてないか、柔軟さを失ってないか、技術力はキープできてるか、新しいものを取り込めているかなど。

そんな楽しい時間を、今回は SPAJAM という全国の猛者が競い合うハッカソンで過ごさせてもらいました。どんなイベントなのか、どんな戦いだったのかを綴ります。

とても素晴らしいイベントなので興味を持っていただければと、そしてぜひ参加して熱い戦いを楽しみましょう。


## SJAPAM 概要
[SPAJAM](https://spajam.jp/) とは "「温泉でハッカソン」を合言葉に、スキルを向上するための競技と交流の場を提供する国内最高峰のハッカソン - [What's SPAJAM](https://spajam.jp/#scene1)" です。

"国内最高峰" を謳うとおり、日本全国で予選を行い、予選を勝ち抜いたチームで本戦を行うという大規模なイベントです。2019年は全国６地域７会場で予選が行われています。

本戦入賞者には豪華な賞品が出ます！
2019年の最優秀賞は「深セン・上海スペシャルツアー」！！
副賞多数も多数で去年の実績は、こちら [結果発表 ｜ SPAJAM2018公式サイト – 温泉でハッカソン](https://spajam.jp/2018/final/result/)

ハッカソンのテーマは当日発表され、そこから24時間かけてのハッカソン勝負となります。主なスケジュールは下記。
１日目
- 開場: 開会（挨拶・趣旨説明・テーマ発表）
- 午前: アイデアソン
- 午後: ハッカソン

２日目
- 午前: ハッカソン
- 午後: プレゼン、審査＆表彰式

メンバーは１チーム５人までで、自分たちでチームを作って参戦します。
あらかじめチームを作って参加できるので安心です。

なお、本選出場とならないと「温泉」は楽しめません。
予選は協賛企業さんのオフィスを使わせていただいての開催となります。今回の東京A予選は [株式会社gumi](https://gu3.co.jp/) さん、新宿中央公園のお隣でした。


## 参戦経緯
もちろん、ハッカソンが好きで楽しみたいから！
というものありますが 2018年 SPAJAM 東京D予選で優秀賞を手にするも、本選出場ならずのリベンジ戦でもあります！！

その際の発表資料と作品は、こちら。

発表資料: [リアルタイムの競演と参加型観戦で音楽を最高に楽しむ「ラップ､タップ､アップ🎶」](https://riotz.works/slides/2018-spajam-qualification)
<div class="slide"><iframe src="https://riotz.works/slides/2018-spajam-qualification"></iframe></div>

作品: [ラップ、タップ、アップ 🎶](https://riotz.works/rap-tap-app/)
<iframe src="https://riotz.works/rap-tap-app/" width="320" height="480"></iframe>

そしてなんと、2018 東京D予選で最優秀賞を取った方が、メンバー構成は違えど今年も同じ会場という偶然。
さらに、現在私が受けているブログメンタリングの同期さん(他のハッカソンで優勝経験あり)も同じ会場！
強敵すぎるライバルたちとの、熱いハッカソンとなりました。

そして我らがチーム。
<blockquote class="twitter-tweet" data-cards="hidden" data-lang="ja"><p lang="ja" dir="ltr">SPAJAM 東京A予選に参戦！「<a href="https://t.co/qxwHfPkw32">https://t.co/qxwHfPkw32</a>、進撃 の PWA」始動！！<a href="https://twitter.com/hashtag/PWA?src=hash&amp;ref_src=twsrc%5Etfw">#PWA</a> でモバイルアプリハッカソン、今年こそ完全勝利を手に。</p>&mdash; lulzneko (ラルズネコ) (@lulzneko) <a href="https://twitter.com/lulzneko/status/1127799462053568512?ref_src=twsrc%5Etfw">2019年5月13日</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>
<blockquote class="twitter-tweet" data-lang="ja"><p lang="ja" dir="ltr">SPAJAM 参戦決った。頑張るしかない。楽しみ！！</p>&mdash; うさぎ / Riotz.works (@lopburny) <a href="https://twitter.com/lopburny/status/1127806513098121217?ref_src=twsrc%5Etfw">2019年5月13日</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

２人。。。そう、２人、通称「ねこ」と「うさぎ」で参戦です。
ふざけてたり、あまくみてるわけでなく。年明けからチーム編成とか、とくにデザイナーさんを探そうとか、たくさん考えていました。ところが「イベントでの発表の連続」「仕事のピーク」「ブログメンティー開始」とか、いろいろしてたら〆切前日💦

でも「あきらめたらそこで試合終了だよ」ですよね。たとえ２人でも本戦、全国制覇の夢はあきらめられません！参戦です！！


## テーマ
![](/articles/assets/lulzneko/hackathon/spajam-2019-pre/01.jpg)
テーマ「NEWS」。毎度ざっくりではありますが、今回は硬い。硬いなぁ。
聞いた瞬間に一瞬眩暈がしました。難しい。

なお、テーマについての説明はありません。
どうとらえ、どのような作品にするのかは参加者にゆだねられています。審査項目の１つ "テーマ性（テーマに沿って利用者と共感できる価値を提供できているか）- [予選概要 ｜ SPAJAM2019](https://spajam.jp/2019/entry/)" がありますが、説明できればオーケーです。

今回、アイドルグループも連想されますが審査基準を満たせればオーケーだと思われます。実際に2018年本戦では "モビリティというテーマから、調味料が動くに発想を転換した点 - [結果発表 ｜ SPAJAM2018](https://spajam.jp/2018/final/result/)" 他が評価され審査員特別賞を受賞されたチームがありました。

某アイドルグループの連想、やってみたかったですが詳しくないのでやめました。今考えても、いいアイデアが浮かばないので、ムリは良くないです。ハッカソン中の中間インタビューでは「某アイドルグループ」のアイデアを考えているチームもありましたが、最終発表では発表がなかったので断念してしまったのかな。見たかったので残念です。


## アイデアソン
まずは全体でアイデアソンです。みんなで、いろいろなアイデアを出して思考の枠を広げます。こちらのフレームワーク、すごくいいですよね。
![](/articles/assets/lulzneko/hackathon/spajam-2019-pre/02.jpg)

アイデアソンは各チームのテーブルで行い、時に他チームのアイデアを見て歩くようなスタイルで行いました。

最初に、この１日で心が動いたことについてのワーク。
あったことを書き、その時の感情を顔の絵文字キャラで表現し、気持ちを書く。何人か発表して共有。

私は「今朝、熊野神社でお参りをした。」「ハッカソンではやる気持ちが静まり落ち着いた。」でした。顔の絵文字キャラは、絵心がなさ過ぎてヤバいものを書いてしまいました。ヤバすぎるので貼るのはやめておきます。

続いてテーマ「ニュース」について、イメージや思いついたことなどを付箋で書き出し。その後、各チームのテーブルを回って共有。ニュース番組名や話題の人などいろいろ出ていました。

もう一度最初のワークのフォーマットで、テーマについて喜怒哀楽の表現。それをもって、４人の参加者とアイデア交換＆インタビュー。皆さん色々な発想をされていてすごかったです。「協賛の [株式会社エーアイ](https://www.ai-j.jp/) さん、提供の [高品質音声合成エンジンAITalk®](https://www.ai-j.jp/about/) ＋ Vtuber」といったアイデアなども出ていました、

そこから上記スライドのフォーマットで、より具体化。発表して共有という流れになります。

まずは「共創」の考えで、たくさんのアイデアが出ますのでアイデアが浮かばずに終了ということはないので、安心して参加できます。

去年はアイデアソンの時間はチームとばらけて作業し、できあがった資料や喜怒哀楽シートなどが張り出されていましたが、今年はチームでやり張り出しはなかったです。張り出したほうがいいのに、どうしたんだろう。聞けばよかった。(実際に去年の予選で優秀賞とったチームが、最後まで張り出しをみて悩んでギリギリでアイデアが作れたと発表してました)


## ハッカソン！
アイデアソンが終わったらハッカソン開始。ここからは「競争」です。
ちょうど、お昼なので各チーム食事へ向かいながらアイデアを検討。私たちもファミレスに入ってアイデア練り。幸いにも昼食時点で方向性も決まり、会場に戻ってすぐにコーディングへ。２人だから、どんどん進めないと間に合いません。

午後はひたすらコーディングを進めましたが、何も準備してないし、人手も足りないのでベース部分の作成で時間をとってしまいました。基本的には慣れている Nuxt.js PWA と AWS Lambda で Web API の構成です。

開場は20時まで、それ以降はメンバーの家なり宿泊施設なりで作業です。複数日での開催は宿泊をどうするのか考えておく必要があります。また SPAJAM 本戦は会場が宿泊施設で主催者提供です。(♨ 温泉いいなぁ！)

予想されていたことではありましたが、結局徹夜。ちゃんと寝ないと体にこたえるので良くないのだけど。

<blockquote class="twitter-tweet" data-lang="ja"><p lang="ja" dir="ltr">ハッカソンの徹夜開発のおともは #22 「Trello があるので眠れない」 でした 😆<a href="https://t.co/hjwarZksHw">https://t.co/hjwarZksHw</a></p>&mdash; lulzneko (ラルズネコ) (@lulzneko) <a href="https://twitter.com/lulzneko/status/1130317980523409408?ref_src=twsrc%5Etfw">2019年5月20日</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

意識高いポッドキャストで、意識(こっちの場合は睡眠不足による意識不明のほうの意識)を失わないよう高める感じで。とても面白かったです。夜間の開発とかはポッドキャスト(ラジオがやってるならラジオも)が良さそうです。

そして夜明け頃に問題発生！肝心の機能が AWS Lambda で処理しきれない😱
各種パラメーターを最大にしても時間内に処理が終わらない。急遽 DynamoDB Streams を併用する方向に転換するも、こちらも処理しきれず。

やりたかったことは形態素解析です。処理能力不足なのか、情報量が多いのか、設定が悪いのか、とにかく処理時間がかかって機能させられませんでした。タイミング的にも、人手的にも厳しい状況でした。悔しいな。(今後、ちゃんと調査して再実装します。お金の力でクラウドとかに逃げる手もありますが、それだとサービスの継続が厳しいので難しいところです。)

この時点でプレゼン資料は未着手なので、泣く泣く私はプレゼン側へ作業転換となりました。まだ他にも必要な機能が残っており「うさぎ」１人での実装では間に合わない部分が出てしまうことに。


## 発表！「📰NEWʑ Link」！！
できあがった作品は「パーソナルニュースの配信と交換によって爆速で仲良くなるアプリ「📰NEWʑ Link」」です！！
<div class="slide"><iframe src="https://riotz.works/slides/2019-spajam-qualification"></iframe></div>
https://riotz.works/slides/2019-spajam-qualification

テーマ「NEWS」に対して、私たちは「個人がニュースを発信し、嬉しい楽しいの思いを広げてつなげていきたい」というコンセプトに設定しました。「１億総メディア時代」個人がニュースを発信、その中でもパーソナルで嬉しくなるニュースを扱いたいと考えました。

スライド２ページ目ですが、ここ１週間のことなのですが、見ててほっこり、見てるこっちに嬉しくなるような TL が流れてました。世の中には楽しいパーソナルなニュースがあふれています。

そのニュースをつなげ、嬉しい楽しいの輪が広がり、そしてまた新しいニュースが生み出され発信されていく。そんな世界観を作り出すアプリです。

そしてお互いの興味が重なる部分、それが共感であり、人は同じようなことに共感している人と仲良くなりやすいというのがあります。そこに着目し、お互いのニュースを交換し共通点を見つけ出して表示することで親密度を上げらられ、仲良くなるきっかけを作り出せるのではないかと考えました。

こちらは、まだ打ち解けきれてないところで使ったり、グループに初対面の人が入った時や、会議や研修のアイスブレイクなどを想定しています。このニュースの交換は当初 [Web NFC](https://developer.mozilla.org/ja/docs/Web/API/MozNFC) を使う予定でしたが、時間切れとなってしまい QR コードとなりました。
(ちょうど別件で Web NFC が欲しくて勉強する予定でしたが、ぶつけ本番となってしまった)

アーキテクチャは以下ですが、詳細記事を書きます。
- Nuxt.js の PWA。JAMstack になります。
- サーバーサイドは AWS で、サーバーレス(API Gateway, AWS Lambda, DynamoDB)。


## 審査＆結果発表
残念ながら、受賞には至りませんでした。

「NEWS」というテーマにアイデアが追い付かなかったのと、技術の引き出し不足でした。

何とかアイデアを絞り出しアプリを作ったものの、こうして振り返ると「個人がニュースを発信したくなる」としているのに、その仕掛けが入っていないと、すぐに思いつくレベルで練り切れてないのがわかります。いろいろなテーマに対しても柔軟に対応できる発想力をもっと鍛えねば。。。

最優秀賞は、去年の同じ予選の覇者さん。もう３回連続で予選最優秀賞なのだとか。すごい！！

そしてブログメンティーの同期さんは、優秀賞。すばらしい！！さすがハッカソンでの優勝経験者。すごいです。

また、他の参加者さんもステキな作品ばかりで、アイデアも実装力も感心感嘆するばかりでした。またどこかのハッカソンでお手合わせしましょう。２日間、ありがとうございました。

[東京A予選 -結果発表 ｜ SPAJAM2019公式サイト – 温泉でハッカソン](https://spajam.jp/2019/entry/tokyo-a/)
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://spajam.jp/2019/entry/tokyo-a/" data-iframely-url="//cdn.iframe.ly/bCDpwga?iframe=card-small"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>


## 写真
![](/articles/assets/lulzneko/hackathon/spajam-2019-pre/03.jpg)
左上、新宿中央公園と三連ビル。
中央、勝利のビル！？見た時に勝利を確信したけど、みんな見てるって。
右上、会場案内。
下段、協賛のモンテールさん提供のお菓子。ごちそうさまでした！


----

最高に楽しい２日間！！。レベルの高い人たちと戦うことができ楽しかったし、賞は取れなかったけど、現在持てる力はすべて投入できました。

反省点は多々あるものの、総じて満足のいく結果でした。
また次のハッカソンで反省点を活かし、さらなる技術力をつけて来年の SPAJAM にも挑戦します！！

ぜひハッカソンの場で一緒に楽しみましょう♪

また、アプリは早めに改修を終えて公開できるようにします。
技術パートについては、ブログにします。

----

ところで、そもそも、このアプリ、ハッカソンへ参加する前に必要だったのではないだろうか 🤔
爆速で仲良くなって、SPAJAM のメンバーになってもらうために。。。 😇
