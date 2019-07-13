---
title: #Go_SaaS 三種の神器オンボーディングセミナー #1 〜東京〜 参加レポート
permalink: take-seminar-on-go-saas1-in-tokyo
date: 2019-07-09
author: lulzneko
categories: レポート
tags:
- Auth0
- CircleCI
- Stripe
- AWS
---

2019年7月8日に開催された「#Go_SaaS 三種の神器 オンボーディングセミナー#1(東京)」に参加したのでレポートします。

![](/articles/assets/lulzneko/seminar/study.png)


今回参加したのは、日本における SaaS ビジネス、サブスクリプションビジネスを推進し ISV や Startup の支援を目的とした「[Go_SaaS 三種の神器](http://go-saas.jp/)」というセミナーです。

このセミナーは ID 管理サービスの Auth0、決済サービスの Stripe、CI/CD サービスの CircleCI を、SaaS/サブスクリプション型のビジネス開発における三種の神器として、サービスの概要紹介とユーザー事例を紹介する半日のセミナーです。また SaaS 型サービス構築の考え方についてから始まるので、まさにサービス開発、新ビジネス開発に向けてパッケージングされたセミナーといえます。

**主な内容**
- AWS ジャパンから見た SaaS 化における課題、及び、今後の展望 (岡崎 貴紀氏 - AWSジャパン　ISV/SaaS パートナー部 部長)
- 「Go_SaaS 三種の神器」が目指す新たな日本のSaaS促進支援の形 (小島 英揮氏 - Still Day One 合同会社 代表社員)

- SaaS 三種の神器 - ID管理・CI/CD・決済
  - 藤田 純氏 - Auth0 株式会社 カントリーマネージャー
  - 森本 健介氏 - CircleCI カントリーマネージャー
  - ダニエル ヘフェルナン氏 - Stripe Japan 代表取締役

- ユーザー事例紹介 - 三種の神器を活用したストレスフリーSaaS運用環境
  - Shifter 開発の舞台裏 (小賀 浩通氏 - Digital Cube 代表取締役社長)
  - SaaS化にあたり直面した課題 (Jeremy Hall, PhD氏 - AppSocially株式会社 VP of Engineering)


## AWS ジャパンから見た Saa S化における課題、及び、今後の展望
まずは AWS の ISV/SaaS パートナー部長の岡崎さんから、ソフトウェア開発および SaaS の現状について。

国内の SaaS 市場の成長率は 15%、それに対してソフトウェア市場は 4.6%。また SaaS の利益率は 76% と、市場の拡大および利益への貢献度が高まっているとのこと。つまり SaaS 開発は収益性を向上させることができる。とはいえ最小限に稼働するレベルの SaaS を作るにしても３ヶ月～２年の開発期間が必要となる。よって既存の SaaS と組み合わせて構築していくことが必要となってくる。
![](/articles/assets/lulzneko/seminar/go-saas/01-001.jpg)

AWS では、その SaaS 開発を支援するプログラムも用意しているので活用してほしいとのこと。
<blockquote class="twitter-tweet" data-lang="en"><p lang="ja" dir="ltr">AWSのパートナー支援制度 <a href="https://twitter.com/hashtag/Go_SaaS?src=hash&amp;ref_src=twsrc%5Etfw">#Go_SaaS</a> <a href="https://t.co/Ytf6bFXgx0">pic.twitter.com/Ytf6bFXgx0</a></p>&mdash; Hideki Ojima @ パラレルマーケター (@hide69oz) <a href="https://twitter.com/hide69oz/status/1148099103424802816?ref_src=twsrc%5Etfw">July 8, 2019</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

SaaS化が求められる市場の潮流と本取り組みへの期待
<iframe src="//www.slideshare.net/slideshow/embed_code/key/xaTC6CAuIVWfSx" width="595" height="485" frameborder="0" marginwidth="0" marginheight="0" scrolling="no" style="border:1px solid #CCC; border-width:1px; margin-bottom:5px; max-width: 100%;" allowfullscreen> </iframe> <div style="margin-bottom:5px"> <strong> <a href="//www.slideshare.net/takanoriokazaki/20190708-go-saasseminaraws" title="20190708 go saas_seminar_aws" target="_blank">20190708 go saas_seminar_aws</a> </strong> from <strong><a href="https://www.slideshare.net/takanoriokazaki" target="_blank">Takanori Okazaki</a></strong> </div>

ソフトウェアを提供するには SaaS にするのが良いよというお話でした。現在はそういった流れなので新規で作るなら当然 SaaS 型になっていくだろうなというのは感じますし、AWS でそのための支援プログラムがあるというのがよいです。


## 「Go_SaaS 三種の神器」が目指す新たな日本の SaaS 促進支援の形
続いて Still Day One の小島さんより。小島さんは富士通から Adobe、AWS を経て現在は６社のサービスでアドバイザーをされているとのこと。まさに SaaS ビジネスのプロで、今回 SaaS をよく知っているほか、AWS と三種の神器(Auth0, CircleCI, Stripe) をつなぐことができる人物ということで本プログラムの進行を担当されているとのこと。
![](/articles/assets/lulzneko/seminar/go-saas/01-002.jpg)

改めて SaaS/サブスクリプションビジネスの凄さについて説明。2019年時点で SaaS/サブスクリプションビジネスは３兆円規模で、年率40%以上で成長している。非常に魅力的なビジネスであるが、既存のパッケージ販売から転換するには大きな壁がある。たとえばファイナンスの壁では Adobe が SaaS 転換した際に、売り上げが落ちることを周到に説明する必要があったことなどがわかりやすい。実際に SaaS 転換後、大きく売り上げが落ち込んだ。現在は素晴らしい業績になっている。
組織文化としても大きく変わる。カスタマーサポートについては極端な例になるが売り切り製品の場合は小さくしたい、やめてしまいたい。販売後はコストになってしまうから。一方で SaaS は使い続けてもらう、解約されないようにするためフィードバックループを回す。顧客のビジネスにメリットをもたらす必要があるのでカスタマーサクセスが重要になってくる。営業よりもカスタマーサクセスの方が売り上げを叩きだすケース多い。
開発も大きく変わってくる。フィードバックループを回すために DevOps の体制が必要になってくるし、クラウドの活用が重要になってくる。SaaS の上に SaaS を作るようなイメージ、既存 SaaS と連携して開発する。
![](/articles/assets/lulzneko/seminar/go-saas/01-003.jpg)

そうした壁もあるが、ビジネスとして美味しく魅力的である。サブスクリプションは売り切りでない。10件売って、次に10件売れば、20件の利益。売り切りは、10件は10件。
では、どのように転換していくかというと、まずは BYOL、ライセンス型のソフトウェアを EC2(AMI) に組み込んでライセンスだけをサブスクリプションとして売る。このサブスクリプションも月額固定でよいし、請求書ベースでも構わない。次にシングルテナントの SaaS にする。徐々に DevOps に向けて体制を変えデプロイを自動化できるようにしていく。ライセンスもライセンスコードの発行から利用ユーザーの ID ベースに移していく。そうなると課金モデルも変わってくる。月の途中で利用者数が変わってくる。
これらを新規で作ると大変であり、また問題を発生させかねない。ムリに作ろうとするとセキュリティ・インシデントとなる。ここは既存のサービスを使っていく。
![](/articles/assets/lulzneko/seminar/go-saas/01-004.jpg)

構築したサービス利用の顧客決済についてはクレジットカードの導入は必須。B2B では請求書でないとダメや「できない」といわれることもあるが現実はできる。そうでなければ AWS がこれだけ使われるわけがない。
クレジットカード決済にすることでキャッシュフローが改善される。SaaS の利用料を月末で〆るとして、翌週には入金される。一方で AWS の利用料も月末で〆られるが引き落としは翌月。入金されてから出金の形なる。これが請求書だと逆になる。AWS の利用料が引き落とされ、それから請求書の支払いが１ヶ月、２ヶ月遅れで入金されてくる。売り上げが上がっているようで、キャッシュフローは赤字になっている。
![](/articles/assets/lulzneko/seminar/go-saas/01-005.jpg)

「Go_SaaS 三種の神器」が目指す新たな日本の SaaS 促進支援の形
<iframe src="//www.slideshare.net/slideshow/embed_code/key/wZtaajo9b4lh3T" width="595" height="485" frameborder="0" marginwidth="0" marginheight="0" scrolling="no" style="border:1px solid #CCC; border-width:1px; margin-bottom:5px; max-width: 100%;" allowfullscreen> </iframe> <div style="margin-bottom:5px"> <strong> <a href="//www.slideshare.net/hide69oz/20190708gosaasoverview" title="20190708_go_saas_overview" target="_blank">20190708_go_saas_overview</a> </strong> from <strong><a href="https://www.slideshare.net/hide69oz" target="_blank">Hideki Ojima</a></strong> </div>

自社製品を SaaS 化するにあたってという観点から、とても勉強になりました。とくに売り上げが積み重ねとなるという考え方は、言われてみればその通りですが見落としてました。もちろん解約というリスクがありますが、前月の売り上げが次月にスライドして、次月の売り上げが重なるのは素晴らしいビジネスです。またキャッシュフローの改善は盲点でした。


## ユーザー事例紹介: Shifter 開発の舞台裏
以前、[JAMStack について発表](https://riotz.works/articles/lulzneko/2019/04/05/made-presentation-about-jamstack-at-shiftup-vol3/)させていただいた Shifter さん、偶然にも SaaS のユーザー事例として小賀さんが Shifter の開発についてお話されてました。

小島さんの既存パッケージソフトウェアを SaaS 化する話が、まさに Shifter がたどってきた道でした。
Shifter の元になる AMIMOTO という WordPress のパッケージがあり、当初は EC2(AMI) として提供していた。それをシングルテナントの SaaS として影響を開始。最終的にマルチテナントの SaaS、Shifter へと。商品から体験へ、ユーザーの価値観が変わってきている。
![](/articles/assets/lulzneko/seminar/go-saas/01-006.jpg)

アーキテクチャはサーバーレスで作られていて、CircleCI と Stripe を使っている。ID 管理は Cognito を使っているが、ここは Auth0 に切り替えるとよいのかもしれない。検討していく。

CI/CD について、Jenkins が良いのではという話はよく聞く。カスタマイズなどができてよいのかもしれないが、いわゆる「Jenkins おじさん問題」がある。Jenkins を保守運用して管理する人が必要となってしまう。CircleCI を使うことで本業に集中できる。

また Stripe については、決済の仕組みを自前で開発したら恐ろしいことになるだろう。PCI DSS などの認証をとるのはとても大変。多通貨、多国籍、柔軟な定期決済と対応するものも多く、不正使用、不正請求などへの対処も必要となってくる。ここはサービスを利用することが重要。また小島さんが話していた通り、キャッシュフローの改善も重要。

その他、モニタリング、UI開発、サポートデスク、CRM とさまざまな SaaS を使っている。

Shifter 開発の舞台裏 三種の神器編
<script async class="speakerdeck-embed" data-id="22eacdafc9c2496fa092f285f458ec84" data-ratio="1.77777777777778" src="//speakerdeck.com/assets/embed.js"></script>

SaaS 開発の歴史をお話してくださり、開発のリアルをすごく感じました。「SaaS 提供者こそ SaaS を使い倒そう！」というメッセージがカッコよかったです！


## AppSocially スタートアップの武器となるSaaS
発表が英語だったためレポートを書けず。。。すみません。
<script async class="speakerdeck-embed" data-id="eea612101f924fe98ab605981a061edf" data-ratio="1.77777777777778" src="//speakerdeck.com/assets/embed.js"></script>


## SaaS 三種の神器 - ID管理・CI/CD・決済
三種の神器の各サービス紹介の資料です。

### Auth0がB2B SaaS企業に必要な理由
<iframe src="//www.slideshare.net/slideshow/embed_code/key/ApVKtJs4PpyGbp" width="595" height="485" frameborder="0" marginwidth="0" marginheight="0" scrolling="no" style="border:1px solid #CCC; border-width:1px; margin-bottom:5px; max-width: 100%;" allowfullscreen> </iframe> <div style="margin-bottom:5px"> <strong> <a href="//www.slideshare.net/junfuji/auth0b2b-saas" title="Auth0がB2B SaaS企業に必要な理由" target="_blank">Auth0がB2B SaaS企業に必要な理由</a> </strong> from <strong><a href="https://www.slideshare.net/junfuji" target="_blank">Jun Fujita</a></strong> </div>

### SaaSビジネスに必須のCI/CD by CircleCI
<iframe src="//www.slideshare.net/slideshow/embed_code/key/zQ1zZ5k9icv2OS" width="595" height="485" frameborder="0" marginwidth="0" marginheight="0" scrolling="no" style="border:1px solid #CCC; border-width:1px; margin-bottom:5px; max-width: 100%;" allowfullscreen> </iframe> <div style="margin-bottom:5px"> <strong> <a href="//www.slideshare.net/KensukeMorimoto1/gosaas1seminarsaascicd20190708" title="Go_SaaS_#1_seminar_SaaSビジネスに必須のCI/CD_20190708" target="_blank">Go_SaaS_#1_seminar_SaaSビジネスに必須のCI/CD_20190708</a> </strong> from <strong><a href="https://www.slideshare.net/KensukeMorimoto1" target="_blank">Kensuke Morimoto</a></strong> </div>

### Stripe SaaSを支える決済機能のコツ
<script async class="speakerdeck-embed" data-id="88e8a5d4069943d5b45ef5409aea7a0e" data-ratio="1.77777777777778" src="//speakerdeck.com/assets/embed.js"></script>



----

SaaS/サブスクリプション型のビジネスを始めるにあたって、最初に必要な知識や考え方をいっぺんに勉強できる素晴らしいセミナーでした。とくにユーザー事例として実際に SaaS を構築されている方々のお話もあり、そして SaaS を構築する上でも SaaS を組み合わせていくとよいというのが響きました。

スタートダッシュや知識のベースアップとして、こちらのセミナーを受け、そのうえで深堀したいサービスのセミナーやミートアップなどのイベントにつなげていくとよいのではないでしょうか。
