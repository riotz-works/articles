---
title: Shiftup! JP_Getshifter Vol3！ 振り返り、Shifter の ヘッドレス CMS 化 に 思いを馳せる
permalink: think-of-shifters-headlesscms-nize-on-shiftup-vol3
date: 2019-04-06
author: lulzneko
categories: レポート
tags:
- JAMStack
- Gridsome
- Nuxt.js
- Hexo
---

![](/articles/assets/lulzneko/seminar/shifter/shifter.png)


2019年4月3日 に 開催された「Shiftup! JP_Getshifter Vol3！はじめてのスタティックサイトジェネレーター」で ディスカスした「Shifter の ヘッドレス CMS 化」についてアイデアになります。
WordPress コンテンツ作成管理 ＋ スタティック サイト ジェネレータ ウェブ生成 ＋ Shifter 運用管理 な 構成っていかがでしょう？

**シリーズの記事**
- [Shiftup! JP_Getshifter Vol3！ で JAMStack な サーバーレス ウェブフロント に ついて発表をしました](/articles/2019/04/05/made-presentation-about-jamstack-at-shiftup-vol3/)
- [Shiftup! JP_Getshifter Vol3！ 参加レポート](/articles/2019/04/03/take-seminar-on-shiftup-vol3/)
- **Shiftup! JP_Getshifter Vol3！ 振り返り、Shifter の ヘッドレス CMS 化 に 思いを馳せる** (本記事)


## イントロダクション
Shiftup! JP_Getshifter Vol3！ にて Shifter Webhooks の 発表がありました。
このは Shifter が サイト生成した際に、Shifter CDN 以外のホスティング環境へデプロイできるようにする機能です。

この機能についてピザ休憩で話をしている際に、Shifter の 生成済みウェブサイトを流すのではなく「管理しているコンテンツを任意の CI へ 流すことができたら凄いのではないか」という話で盛り上がりました。

発表者 [青空俯角 / mimi(@miminari)さん](https://twitter.com/miminari) の ブログ [gridsomeのススメとShifterの可能性について – Photosynthesic blog](https://blog.photosynthesic.jp/2019/04/gridsome%e3%81%ae%e3%82%b9%e3%82%b9%e3%83%a1%e3%81%a8shifter%e3%81%ae%e5%8f%af%e8%83%bd%e6%80%a7%e3%81%ab%e3%81%a4%e3%81%84%e3%81%a6/) で 言及されている、こちらになります。
![](/articles/assets/lulzneko/seminar/shifter/03-21.png)


ざっくり言うと Shifter の WordPress エディター と コンテンツ管理 と 運用 を 利用ターゲットとし、WordPress で 作成したコンテンツのソースを任意の スタティック サイト ジェネレータ に かけて JAMStack な サイト生成するということができるのではないかという話です。

スタティック サイト ジェネレータ を 使う場合、コンテンツ管理を ヘッドレス CMS というエディターとコンテンツ管理のためだけのサービスを使い、そこからコンテンツを取得して スタティック サイト ジェネレータ で サイト生成するという手法があります。
この手法を Shifter で実現すると、とても良いことが起こるのではないかという考えになります。

以下は私の発表で使ったものですが、WordPress の 部分を Shifter にして動作させることができたら嬉しいというものになります。(REST で 取得するかどうかは別として)
![](/articles/assets/lulzneko/seminar/shifter/03-16.png)


## ヘッドレス CMS とは
ここで "ヘッドレス CMS" という概念が急に登場したので、整理しておきます。

JAMStack な ブログや記事を配信するサイトを作る際に、その記事のソースをどのように管理するのかという話があります。スタティック サイト ジェネレータ 自体は記事を管理する機能はなく、特定のソースから JAMStack な サイトを作ることに特化しています。そのため記事のソースを管理する方法が必要となってくるからです。

１つは、ローカルにある Markdown などのファイルをソースとし使う方法になります。
サイトの構成と一緒に記事も管理する方法になります。たとえば、この記事自体は Markdown ファイルとして Git で 管理されていて、同じリポジトリ内にサイト生成のテンプレート類も入っています。これを Hexo というツールで JAMStack な サイトとして生成し、GitHub Pages でホスティングしています。

もう１つは、CMS で 管理されているソースをもとに JAMStack な サイトを生成する方法です。
この方法で使う CMS は エディター と コンテンツ管理が主体になります。コンテンツの作成と管理に特化していて、ウェブサイトは別で持ってくださいという形です。

この エディター と コンテンツ管理 に 特化した CMS を ヘッドレス CMS と 呼びます。
CMS は 通常 エディター と コンテンツ管理 から、ウェブサイトの生成とホスティングまでのすべてを担っています。このウェブサイト生成とホスティングがないものを Headless とし、Headless な CMS、ヘッドレス CMS と 呼ばれています。

通常 GUI がないものが Headless と 呼ばれますが、ヘッドレス CMS は コマンドラインベースというわけではなく Web UI がちゃんとあります。この場合「ウェブサイト側」が ないことを指して Headless と 呼ばれるようです。

主な ヘッドレス CMS は [headlessCMS | Top Content Management Systems for JAMstack sites](https://headlesscms.org/) で 見つけることができます。
![](/articles/assets/lulzneko/serverless/jamstack/04.png)

最近は [Contentful](https://www.contentful.com/) が 話題になっているようです。(私は Markdown ＋ Git 派なので、ヘッドレス CMS を 使ってないです。すみません。。。)


## Shifter が ヘッドレス CMS 化 した場合の世界観
では、Shifter が ヘッドレス CMS になると何がうれしいのかということですが、Shifter = WordPress だからです。

WordPress は 世界で一番使われている CMS と いわれることがあります。最近では note などの新しいサービスが色々登場し、老舗のはてな や 技術系のQiita といった人気のサービスもあるとはいえ、なんだかんだで WordPress を 使ったり立ち上げしたことがある人が多いのではないでしょうか。

その「使っている人が多い」「使ったことがある人も多い」「初心者にも使いやすい」という特性を Shifter は 活かすことができます。

そして WordPress の コンテンツ作成力(= エディターと管理) を そのままに、スタティック サイト ジェネレータ の ウェブサイトの表現力を組み合わせた場合に、凄い相乗効果が得られると考えています。

WordPress の テーマはたくさんあり優れていますが、オリジナルを作ろうとすると途端にハードルが上がります。また、ブログではなくウェブサイトを作ろうとすると、これもまた大変です。この部分をフロント系の得意な技術、つまり任意の スタティック サイト ジェネレータ へ転換することができたら、素晴らしいマリアージュが生まれるのではないでしょうか。

では WordPress を 自前で立てて、REST API 経由で スタティック サイト ジェネレータ へ 流したらよいのではないか、まさに発表のときに使った図の構成になります。
![](/articles/assets/lulzneko/seminar/shifter/03-16.png)

しかしながら、この図には欠点があります。**WordPressの管理運用**をどうするのか。

WordPress を 立てて REST 経由でコンテンツを取得するとなると、WordPress へ アクセスできることになり、WordPress を 守る必要が出てきます。またバージョンアップなどの管理も必要となります。これは WordPress だけではなく、PHP に ウェブサーバー に DB、OS と たくさんの管理対象が発生します。

これを Shifter に お願いすることができたら？
完全に管理しておいてくれるので安心であり、管理作業から解放されることになります。

できれば スタティック サイト ジェネレータ で 作ったサイトを Shifter CDN へ 返せるとさらによいのですが、そこは難しいようでしたら Netlify や S3 など、CI からデプロイできます。

以上が Shifter が ヘッドレス CMS と なり、スタティック サイト ジェネレータ と 組み合わせることの世界観とメリットになります。


## まとめ
Shifter が ヘッドレス CMS として動作してくれることで以下のようなメリットが生まれます。
- WordPress による、コンテンツ作成管理力
- スタティック サイト ジェネレータ による、ウェブサイト表現力
- Shifter の WordPress マネージドによる、WordPress 管理からの解放

Shifter さん、ぜひ検討してみていただけませんか？
ヘッドレス CMS という表現を使っていますが、生成済みサイトを流すのではなく、単に管理しているコンテンツのソースを流すような仕組みがあればということで、スタティック サイト ジェネレータ と組み合わせてステキなことができそうです。

ウェブサイト構築はデザイナーさんやフロントエンドエンジニアさんがやり、記事などのコンテンツを継続的に作っていくのは実務部門やるといったことがあります。そういった時に表現力のあるウェブサイトを作りながらも、WordPress によるコンテンツ管理の運営を発揮できるのではないでしょうか。


----

Shiftup! JP_Getshifter Vol3！ 参加記三部作、最後の記事になります。

ちょうど 発表者 [青空俯角 / mimi(@miminari)さん](https://twitter.com/miminari) と 運営の [KOGA Hiromichi(@digitalcube)さん](https://twitter.com/digitalcube)、[Seiji Akatsuka(@seijiakatsuka)さん](https://twitter.com/seijiakatsuka) と Shifter アップデートについての話をして、Gridsome との組み合わせに話が及んで浮かんだアイデアになります。(イントロダクションで[青空俯角 / mimi(@miminari)さん](https://twitter.com/miminari)記事引用の話)

本記事の主題としては「Shifter さん、ぜひ作ってください」にはなりますが、WordPress の 運用部分は発生するものの Gridsome などと WordPress を 組み合わせて同様のこともできますし、実際にやったことがある案件で Nuxt.js から WordPress REST API を 呼び出して、モバイル対応してない既存 WordPress サイトのモバイル対応するというのもやったことがあります。

WordPress と スタティック サイト ジェネレータ の 組み合わせは、応用力が高いと考えています。
そのためアーキテクチャや考え方などについて発信したく、また ヘッドレス CMS ＋ スタティック サイト ジェネレータ という JAMStack の アーキテクチャの１つを広めたく書いたものになります。

そして勉強会に参加すると、こういった使い方や新しいアーキテクチャについての議論ができたりします。今回はかなりカジュアルでお話しやすいイベントだったというのもありますが、実際にサービスを展開されている方や発表者さんとディスカスできるのは、とても素晴らしく、よいですね。

どこかでお会いしましたら、いろいろなアイデアについてディスカスしましょう！
