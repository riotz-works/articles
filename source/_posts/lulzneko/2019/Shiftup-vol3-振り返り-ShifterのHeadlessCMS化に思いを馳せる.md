---
title: Shiftup! JP_Getshifter Vol3！ 振り返り、Shifterのヘッドレス CMS 化に思いを馳せる
permalink: think-of-shifters-headlesscms-nize-on-shiftup-vol3
alias: /2019/04/06/think-of-shifters-headlesscms-nize-on-shiftup-vol3/index.html
date: 2019-04-06
author: lulzneko
categories: レポート
tags:
- JAMstack
- Gridsome
- Nuxt.js
- Hexo
- Shifter
- WordPress
---

![](/articles/assets/lulzneko/seminar/shifter/shifter.png)


2019年4月3日に開催された「Shiftup! JP_Getshifter Vol3！はじめてのスタティックサイトジェネレーター」でディスカスした「Shifter のヘッドレス CMS 化」についてアイデアになります。
WordPress コンテンツ作成管理 ＋ スタティック・サイト・ジェネレーター ウェブ生成 ＋ Shifter 運用管理な構成っていかがでしょう？

**シリーズの記事**
- [Shiftup! JP_Getshifter Vol5！ にて「Shifter ＋ SSG の世界」について発表をしました](https://riotz.works/articles/lulzneko/2019/08/21/made-presentation-about-shifter-with-ssg-at-shiftup-vol5/)
- [Shiftup! JP_Getshifter Vol3！ で JAMstack な サーバーレス ウェブフロント に ついて発表をしました](https://riotz.works/articles/lulzneko/2019/04/05/made-presentation-about-jamstack-at-shiftup-vol3/)
- [Shiftup! JP_Getshifter Vol3！ 参加レポート](https://riotz.works/articles/lulzneko/2019/04/03/take-seminar-on-shiftup-vol3/)
- **Shiftup! JP_Getshifter Vol3！ 振り返り、Shifter のヘッドレス CMS 化に思いを馳せる** (本記事)


## イントロダクション
Shiftup! JP_Getshifter Vol3！ にて Shifter Webhooks の発表がありました。
このは Shifter がサイト生成した際に、Shifter CDN 以外のホスティング環境へデプロイできるようにする機能です。

この機能についてピザ休憩で話をしている際に、Shifter の生成済みウェブサイトを流すのではなく「管理しているコンテンツを任意の CI へ流すことができたらすごいのではないか」という話で盛り上がりました。

発表者 [青空俯角 / mimi(@miminari)さん](https://twitter.com/miminari) のブログ [gridsomeのススメとShifterの可能性について – Photosynthesic blog](https://blog.photosynthesic.jp/2019/04/gridsome%e3%81%ae%e3%82%b9%e3%82%b9%e3%83%a1%e3%81%a8shifter%e3%81%ae%e5%8f%af%e8%83%bd%e6%80%a7%e3%81%ab%e3%81%a4%e3%81%84%e3%81%a6/) で言及されている、こちらになります。
![](/articles/assets/lulzneko/seminar/shifter/03-21.png)


ざっくり言うと Shifter の WordPress エディターとコンテンツ管理・運用を利用ターゲットとし、WordPress で作成したコンテンツのソースを任意のスタティック・サイト・ジェネレーターにかけて JAMstack なサイト生成するということができるのではないかという話です。

スタティック・サイト・ジェネレーターを使う場合、コンテンツ管理をヘッドレス CMS というエディターとコンテンツ管理のためだけのサービスを使い、そこからコンテンツを取得してスタティック・サイト・ジェネレーターでサイト生成するという手法があります。
この手法を Shifter で実現すると、とても良いことが起こるのではないかという考えになります。

以下は私の発表で使ったものですが、WordPress の部分を Shifter にして動作させることができたら嬉しいというものになります。(REST で取得するかどうかは別として)
![](/articles/assets/lulzneko/seminar/shifter/03-16.png)


## ヘッドレス CMS とは
ここで "ヘッドレス CMS" という概念が急に登場したので、整理しておきます。

JAMstack なブログや記事を配信するサイトを作る際に、その記事のソースをどのように管理するのかという話があります。スタティック・サイト・ジェネレーター自体は記事を管理する機能はなく、特定のソースから JAMstack なサイトを作ることに特化しています。そのため記事のソースを管理する方法が必要となってくるからです。

１つは、ローカルにある Markdown などのファイルをソースとし使う方法になります。
サイトの構成と一緒に記事も管理する方法になります。たとえば、この記事自体は Markdown ファイルとして Git で管理されていて、同じリポジトリ内にサイト生成のテンプレート類も入っています。これを Hexo というツールで JAMstack なサイトとして生成し、GitHub Pages でホスティングしています。

もう１つは、CMS で管理されているソースをもとに JAMstack なサイトを生成する方法です。
この方法で使う CMS はエディター とコンテンツ管理が主体になります。コンテンツの作成と管理に特化していて、ウェブサイトは別で持ってくださいという形です。

このエディターとコンテンツ管理に特化した CMS をヘッドレス CMS と呼びます。
CMS は通常エディターとコンテンツ管理から、ウェブサイトの生成とホスティングまでのすべてを担っています。このウェブサイト生成とホスティングがないものを Headless とし、Headless な CMS、ヘッドレス CMS と呼ばれています。

通常 GUI がないものを Headless と呼ばれますが、ヘッドレス CMS はコマンドラインベースというわけではなく Web UI がちゃんとあります。この場合「ウェブサイト側」がないことを指して Headless と呼ばれるようです。

主なヘッドレス CMS は [headlessCMS | Top Content Management Systems for JAMstack sites](https://headlesscms.org/) で見つけることができます。
![](/articles/assets/lulzneko/serverless/jamstack/04.png)

最近は [Contentful](https://www.contentful.com/) が話題になっているようです。(私は Markdown ＋ Git 派なので、ヘッドレス CMS を使ってないです。すみません。。。)


## Shifter がヘッドレス CMS 化した場合の世界観
では、Shifter がヘッドレス CMS になると何がうれしいのかということですが、Shifter = WordPress だからです。

WordPress は世界で一番使われている CMS といわれることがあります。最近では note などの新しいサービスが色々登場し、老舗のはてなや技術系のQiita といった人気のサービスもあるとはいえ、なんだかんだで WordPress を使ったり、立ち上げたことがあるのではないか。

その「使っている人が多い」「使ったことがある人も多い」「初心者にも使いやすい」という特性を Shifter は活かすことができます。

そして WordPress のコンテンツ作成力(= エディターと管理) をそのままに、スタティック・サイト・ジェネレーターのウェブサイトの表現力を組み合わせた場合に、すごい相乗効果が得られると考えています。

WordPress のテーマはたくさんあり優れていますが、オリジナルを作ろうとすると途端にハードルが上がります。また、ブログではなくウェブサイトを作ろうとすると、これもまた大変です。この部分をフロント系の得意な技術、つまり任意のスタティック・サイト・ジェネレーターへ転換することができたら、素晴らしいマリアージュが生まれるのではないでしょうか。

では WordPress を自前で立てて、REST API 経由でスタティック・サイト・ジェネレーターへ流したらよいのではないか、まさに発表のときに使った図の構成になります。
![](/articles/assets/lulzneko/seminar/shifter/03-16.png)

しかしながら、この図には欠点があります。**WordPressの管理運用**をどうするのか。

WordPress を立てて REST 経由でコンテンツを取得すると、WordPress へアクセスできることになり、WordPress を守る必要が出てきます。またバージョンアップなどの管理も必要となります。これは WordPress だけではなく、PHP に、ウェブサーバー、DB、OS とたくさんの管理対象が発生します。

これを Shifter にお願いすることができたら？
完全に管理しておいてくれるので安心であり、管理作業から解放されることになります。

できればスタティック・サイト・ジェネレーターで作ったサイトを Shifter CDN へ返せるとさらによいのですが、そこは難しいようでしたら Netlify や S3 など、CI からデプロイできます。

以上が Shifter がヘッドレス CMS となり、スタティック・サイト・ジェネレーターと組み合わせることの世界観とメリットになります。


## まとめ
Shifter がヘッドレス CMS として動作してくれることで以下のようなメリットが生まれます。
- WordPress による、コンテンツ作成管理力
- スタティック・サイト・ジェネレーターによる、ウェブサイト表現力
- Shifter の WordPress マネージドによる、WordPress 管理からの解放

Shifter さん、ぜひ検討してみていただけませんか？
ヘッドレス CMS という表現を使っていますが、生成済みサイトを流すのではなく、単に管理しているコンテンツのソースを流すような仕組みがあればということで、スタティック・サイト・ジェネレーターと組み合わせてステキなことができそうです。

ウェブサイト構築はデザイナーさんやフロントエンドエンジニアさんがやり、記事などのコンテンツを継続的に作っていくのは実務部門やるといったことがあります。そういった時に表現力のあるウェブサイトを作りながらも、WordPress によるコンテンツ管理の運営を発揮できるのではないでしょうか。


----

Shiftup! JP_Getshifter Vol3！ 参加記三部作、最後の記事になります。

ちょうど発表者 [青空俯角 / mimi(@miminari)さん](https://twitter.com/miminari) と運営の [KOGA Hiromichi(@digitalcube)さん](https://twitter.com/digitalcube)、[Seiji Akatsuka(@seijiakatsuka)さん](https://twitter.com/seijiakatsuka) と Shifter アップデートについての話をして、Gridsome との組み合わせに話が及んで浮かんだアイデアになります。(イントロダクションで[青空俯角 / mimi(@miminari)さん](https://twitter.com/miminari)記事引用の話)

本記事の主題としては「Shifter さん、ぜひ作ってください」にはなりますが、WordPress の運用部分は発生するものの Gridsome などと WordPress を組み合わせて同様のこともできますし、実際にやったことがある案件で Nuxt.js から WordPress REST API を呼び出してモバイル対応してない既存 WordPress サイトのモバイル対応するというのもやったことがあります。

WordPress とスタティック・サイト・ジェネレーターの組み合わせは、応用力が高いと考えています。
そのためアーキテクチャや考え方などについて発信したく、またヘッドレス CMS ＋ スタティック・サイト・ジェネレーターという JAMstack のアーキテクチャの１つを広めたく書いたものになります。

そして勉強会に参加すると、こういった使い方や新しいアーキテクチャについての議論ができたりします。今回はかなりカジュアルでお話しやすいイベントだったというのもありますが、実際にサービスを展開されている方や発表者さんとディスカスできるのは、とても素晴らしく、よいですね。

どこかでお会いしましたら、いろいろなアイデアについてディスカスしましょう！
