---
title: 独自ドメインでメールの送受信できるように、Google Domains のメール転送と Gmail の送信元を設定する
permalink: google-domains-mail-forwarding-and-gmail-sender-settings
date: 2019-03-03
author: lulzneko
categories: 開発環境
tags:
---

![](/articles/assets/lulzneko/domain/mail/mail.jpg)

2019年2月28日に `.dev` ドメインの早期アクセスプログラムが終了し、追加費用なしで取得できるようになり多くの方がドメイン取得をされたのではないでしょうか。

`.dev` ドメインに限らないのですが、オリジナルのドメインでアプリやウェブサイトを公開するほかに、メールのアドレスとしても使うことができます。

Google Domains は管理しているドメインのメールを転送するサービスも提供しています。そして Gmail の送信元設定と組み合わせることで、取得したオリジナルのドメイン名でメールの送受信ができるようになります。その設定方法を紹介します。


## `.dev` ドメイン
Google が取得している gTDL(generic Top-Level Domain) で、 `example.com` のように `example.dev` のようなドメインを作ることができます。

トップレベルのドメインは、`.com`, `.net` などのような一般用途のドメインや、`.jp` のような国別コードのドメインが使われてきて、`.biz` や `.asia` といった gTDL, sTDL が追加されてきました。

2012年に gTDL が開放されさまざまなトップレベルのドメインが導入され、たとえば Riotz.works の `.works` なども、これを機に導入されたものです

このとき Google は `.dev` を取得していたのですが、長らく一般開放はされませんでした。
たまたま使われているのを見かけては欲しがったのを覚えています。
<blockquote class="twitter-tweet" data-lang="ja"><p lang="ja" dir="ltr">このサイト dev ドメイン使ってる。いいなぁ～<br>.dev って Google 管理だと思うのだけど Google Domains では買えない。どうなってるんだろ<a href="https://t.co/qFuvffbJNP">https://t.co/qFuvffbJNP</a></p>&mdash; lulzneko (ラルズネコ) (@lulzneko) <a href="https://twitter.com/lulzneko/status/1009328857789263878?ref_src=twsrc%5Etfw">2018年6月20日</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

2019年2月20日、ついに `.dev` ドメインが一般開放されます。
早期アクセスプログラムとして 1万1500ドルの追加から、徐々に追加費用が下がり 2月28日に追加費用なしとなり、日本時間 2019年2月28日 25時からドメイン取得のちょっとしたお祭り騒ぎのようになりました。これは夕方ごろの Twitter トレンドですが入っています。（日中は東京ローカルではなかった気も）
![](/articles/assets/lulzneko/domain/mail/01.png)


## Google Domains とメール転送
`.dev` ドメインは [Google Domains](https://domains.google/) から購入できます。また他にも取り扱っているレジストラーはあります。
[Google Domains](https://domains.google/) のよいところは、すでにアカウントを持っていること(が多いか)と、メール転送が使える点になります。あと好みはありますが UI/UX がよいのではないでしょうか。
`.dev` ほか、多数のドメインを取り扱っているので、比較的好みのドメインがとりやすいです。

ここからは。`.dev` に限らず、[Google Domains](https://domains.google/) で購入したドメイン全般の話になります。
[Google Domains](https://domains.google/) で購入したドメインは、１ドメインにつき 100個までメール転送の設定ができます。(2019年3月現在)

[Google Domains](https://domains.google/) のマイドメイン管理 [https://domains.google.com/m/registrar/](https://domains.google.com/m/registrar/) へアクセスし、メール転送を設定したいドメインを選択します。
左のメニューから [メールアドレス] を選択します。
画面中央のメール転送の設定に、自分のドメインで使いたいアカウント名と、転送先のメールアドレスを入力します。
ここではアカウント名 `contact` (@100%.dev) を `100% @gmail.com` へ転送する例とします。
(※ 実際には `%` は使えず、`% @` のスペースは自動リンク防止のためにスペースを入れている、架空のアドレスになります)
![](/articles/assets/lulzneko/domain/mail/02.png)

これで、`contact@100%.dev` へ来たメールは `100% @gmail.com` へ転送されます。
新しく取れたドメインで 100個までは転送設定できるので、ある程度の規模までは耐えられるのではないでしょうか。

現代のコミュニケーションにおいてメールはメジャーではなくなりつつありますが、`contact@example.com` のような対外コンタクト用や `info@example.com` のような情報発信など完全に廃止しきれない部分はあるかと思います。


## Gmail と送信元設定
[Google Domains](https://domains.google/) によってメール転送ができるようになりました。
これで取得したドメインのメールを受け取りできるのですが困るのは返信。
このままでは転送砂礫のアドレスでメールを書くことになり「※ `contact@example.com` は発信用のため～」みたいなことを書くことになります。（かきました😢）

そこで [Gmail](https://mail.google.com/) に送信元の設定をして、転送設定した際に作ったアカウントのメールから送信できるようにします。

**[重要]** この設定は ２段階認証を迂回する設定のため場合によっては危険です。実際に「セキュリティ診断 - サードパーティによるアクセス」の警告が出ます。必要の有無を確認の上、自己責任で設定してください。
詳しくは [アプリ パスワードでログイン - Google アカウント ヘルプ](https://support.google.com/accounts/answer/185833?p=app_passwords_sa&hl=ja&visit_id=636872203569898801-709091582&rd=1) をご確認ください。

転送先の [Gmail](https://mail.google.com/) アカウントで Google アカウントのセキュリティ設定 [https://myaccount.google.com/security](https://myaccount.google.com/security) へアクセスし、[アプリ パスワード] を選択します。
![](/articles/assets/lulzneko/domain/mail/03.png)

[アプリを選択] から [メール] を設定します。
[デバイスを選択] から [その他（名前を入力）] を設定します。
![](/articles/assets/lulzneko/domain/mail/04.png)

アプリ パスワード に 任意の名前を付け、[生成] をクリックします。
![](/articles/assets/lulzneko/domain/mail/05.png)

アプリ パスワード が 生成されるのでひかえておきます。
![](/articles/assets/lulzneko/domain/mail/06.png)

続いて転送先の [Gmail](https://mail.google.com/) のアカウントとインポート設定 [https://mail.google.com/mail/u/0/#settings/accounts](https://mail.google.com/mail/u/0/#settings/accounts) へアクセスします。
[名前: (Gmail を使用して他のメール アドレスからメールを送信します)] の [他のメール アドレスを追加] をクリックします。
![](/articles/assets/lulzneko/domain/mail/07.png)

設定ダイアログが開きます。
[名前] にメール送信者の名前を入力します。
[メールアドレス] に [Google Domains](https://domains.google/) で設定した、新しいドメインのメールアドレスを入力します。
[エイリアスとして扱います。] のチェックを外します。
![](/articles/assets/lulzneko/domain/mail/08.png)

SMTPサーバーの設定を入力します。
[SMTP サーバー] に [smtp.gmail.com] を入力します。
[ユーザー名] に [Gmail のアドレス] を入力します。
[パスワード] に 先ほど生成した アプリ パスワード を 入力します。
![](/articles/assets/lulzneko/domain/mail/09.png)

Goodle Domains で設定したアカウントのアドレスへメールが送信されます。
メール本文のリンクをクリックするか、確認コードを入力します。
![](/articles/assets/lulzneko/domain/mail/10.png)

送信元のアドレスが追加されます。
常に新しいドメインのメールでよい場合は、そのメールアドレスの横の [デフォルト] をクリックします。
また、下の [デフォルトの返信モード] も必要に応じて設定します。
![](/articles/assets/lulzneko/domain/mail/11.png)

デフォルトに設定しておくと、新しいメールを書く際にも、選択したメールアドレスが設定されて表示されます。
変更する場合は、[From] に設定されているメールアドレスの右の [▼] をクリック思案す。
![](/articles/assets/lulzneko/domain/mail/12.png)


----

[Google Domains](https://domains.google/) によるメール転送と [Gmail](https://mail.google.com/) の送信元設定になります。
最近はメールを使うことも少なくなってきましたが、対外的なコンタクトなどではまだまだ使いますし、[Gmail](https://mail.google.com/) のアドレスよりも、ちゃんと取得したドメインでメール送信できるといいですよね。

メールサーバーを立てるのは大変なことですし、送受信セットで手軽に使えるクラウドのサービスもまだないように感じます。[Google Domains](https://domains.google/)、アカウント設定、[Gmail](https://mail.google.com/) と行ったり来たりになりますが、この組み合わせだと比較的容易に設定できるのではないでしょうか。

なによりも、[Gmail](https://mail.google.com/) がそのまま使えるのがありがたいです。
