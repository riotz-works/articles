---
title: Google Domains の メール転送 と Gmail の 送信元設定
permalink: google-domains-mail-forwarding-and-gmail-sender-settings
date: 2019-03-03
author: lulzneko
categories: 開発環境
tags:
---

![](/articles/assets/lulzneko/domain/mail/mail.jpg)

2019年2月28日 に `.dev` ドメイン の 早期アクセスプログラムが終了し、追加費用なしに取得できるようになり多くの方がドメイン取得をされたのではないでしょうか。

`.dev` ドメインに限らないのですが、オリジナルのドメインでアプリやウェブサイトを公開するほかに、メールのアドレスとしても使うことができます。

Google Domains は 管理しているドメインのメールを転送するサービスも提供しています。そして Gmail の 送信元設定と組み合わせることで、取得したオリジナルのドメイン名でメールの送受信ができるようになります。その設定方法を紹介します。


## `.dev` ドメイン
Google が 取得している gTDL(generic Top-Level Domain) で、 `example.com` のように `example.dev` のようなドメインを作ることができます。

トップレベルのドメインは、`.com`, `.net` などのような 一般用途のドメイン や、`.jp` のような国別コードのドメインが使われてきて、`.biz` や `.asia` といった gTDL, sTDL が 追加されてきました。

2012年に gTDL が 開放され様々なトップレベルのドメインが導入され、たとえば Riotz.works の `.works` なども これを機に導入されたものになります。

このとき Google は `.dev` を 取得していたのですが、長らく一般開放はされませんでした。
たまたま使われているのを見かけては欲しがったのを覚えています。
<blockquote class="twitter-tweet" data-lang="ja"><p lang="ja" dir="ltr">このサイト dev ドメイン使ってる。いいなぁ～<br>.dev って Google 管理だと思うのだけど Google Domains では買えない。どうなってるんだろ<a href="https://t.co/qFuvffbJNP">https://t.co/qFuvffbJNP</a></p>&mdash; lulzneko (ラルズネコ) (@lulzneko) <a href="https://twitter.com/lulzneko/status/1009328857789263878?ref_src=twsrc%5Etfw">2018年6月20日</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

2019年2月20日、ついに `.dev` ドメイン が 一般開放されます。
早期アクセスプログラムとして 1万1500ドル の追加から、徐々に追加費用が下がり 2月28日 に 追加費用なしとなり、日本時間 2019年2月28日 25時からドメイン取得のちょっとしたお祭り騒ぎのようになりました。これは夕方ごろの Twitter トレンド ですが入っています。（日中は東京ローカルではなかった気も）
![](/articles/assets/lulzneko/domain/mail/01.png)


## Google Domains と メール転送
`.dev` ドメイン は [Google Domains](https://domains.google/) から購入することができます。また他にも取り扱っているレジストラはあります。
[Google Domains](https://domains.google/) の よいところは、すでにアカウントを持っていること(が多いか)と、メール転送が使える点になります。あとは好みはありますが UI/UX が よいのではないでしょうか。
`.dev` ほか、多数のドメインを取り扱っているので、比較的好みのドメインがとりやすいです。

ここからは。`.dev` に 限らず、[Google Domains](https://domains.google/) で 購入したドメイン全般の話になります。
[Google Domains](https://domains.google/) で 購入したドメインは、１ドメインにつき 100個までメール転送の設定ができます。(2019年3月現在)

[Google Domains](https://domains.google/) の マイドメイン 管理 [https://domains.google.com/m/registrar/](https://domains.google.com/m/registrar/) へ アクセスし、メール転送を設定したいドメインを選択します。
左のメニューから [メールアドレス] を 選択します。
画面中央のメール転送の設定に、自分のドメインで使いたいアカウント名と、転送先のメールアドレスを入力します。
ここでは アカウント名 `contact` (@100%.dev) を `100% @gmail.com` へ 転送する例とします。
(※ 実際には `%` は 使えず、`% @` の スペースは自動リンク防止のためにスペースを入れている、架空のアドレスになります)
![](/articles/assets/lulzneko/domain/mail/02.png)

これで、`contact@100%.dev` へ 来たメールは `100% @gmail.com` へ転送されます。
新しく取れたドメインで 100個までは転送設定できるので、ある程度の規模までは耐えられるのではないでしょうか。

現代のコミュニケーションにおいてメールはメジャーではなくなりつつありますが、`contact@example.com` のような対外コンタクト用や `info@example.com` のような情報発信など完全に廃止しきれない部分はあるかと思います。


## Gmail と 送信元設定
[Google Domains](https://domains.google/) によってメール転送ができるようになりました。
これで取得したドメインでメールを受け取ることができるのですが、困るのが返信。
このままでは転送砂礫のアドレスでメールを書くことになり「※ `contact@example.com` は 発信用のため～」みたいなことを書くことになります。（かきました😢）

そこで [Gmail](https://mail.google.com/) に 送信元の設定をして、転送設定した際に作ったアカウントのメールから送信できるようにします。

**[重要]** この設定は ２段階認証 を 迂回する設定のため場合によっては危険です。実際に「セキュリティ診断 - サードパーティによるアクセス」の警告が出ます。必要の有無を確認の上、自己責任で設定してください。
詳しくは [アプリ パスワードでログイン - Google アカウント ヘルプ](https://support.google.com/accounts/answer/185833?p=app_passwords_sa&hl=ja&visit_id=636872203569898801-709091582&rd=1) を ご確認ください。

転送先の [Gmail](https://mail.google.com/) アカウント で Google アカウント の セキュリティ設定 [https://myaccount.google.com/security](https://myaccount.google.com/security) へ アクセスし、[アプリ パスワード] を 選択します。
![](/articles/assets/lulzneko/domain/mail/03.png)

[アプリを選択] から [メール] を 設定します。
[デバイスを選択] から [その他（名前を入力）] を 設定します。
![](/articles/assets/lulzneko/domain/mail/04.png)

アプリ パスワード に 任意の名前を付け、[生成] を クリックします。
![](/articles/assets/lulzneko/domain/mail/05.png)

アプリ パスワード が 生成されるのでひかえておきます。
![](/articles/assets/lulzneko/domain/mail/06.png)

続いて転送先の [Gmail](https://mail.google.com/) の アカウント と インポート 設定 [https://mail.google.com/mail/u/0/#settings/accounts](https://mail.google.com/mail/u/0/#settings/accounts) へ アクセスします。
[名前: (Gmail を使用して他のメール アドレスからメールを送信します)] の [他のメール アドレスを追加] を クリックします。
![](/articles/assets/lulzneko/domain/mail/07.png)

設定ダイアログが開きます。
[名前] に メール送信者の名前を入力します。
[メールアドレス] に [Google Domains](https://domains.google/) で 設定した、新しいドメインのメールアドレスを入力します。
[エイリアスとして扱います。] の チェックを外します。
![](/articles/assets/lulzneko/domain/mail/08.png)

SMTPサーバーの設定を入力します。
[SMTP サーバー] に [smtp.gmail.com] を入力します。
[ユーザー名] に [Gmail の アドレス] を 入力します。
[パスワード] に 先ほど生成した アプリ パスワード を 入力します。
![](/articles/assets/lulzneko/domain/mail/09.png)

Goodle Domains で 設定したアカウントのアドレスへメールが送信されます。
メール本文のリンクをクリックするか、確認コードを入力します。
![](/articles/assets/lulzneko/domain/mail/10.png)

送信元のアドレスが追加されます。
常に新しいドメインのメールでよい場合は、そのメールアドレスの横の [デフォルト] を クリックします。
また、下の [デフォルトの返信モード] も 必要に応じて設定します。
![](/articles/assets/lulzneko/domain/mail/11.png)

デフォルトに設定しておくと、新しいメールを書く際にも、選択したメールアドレスが設定されて表示されます。
変更する場合は、[From] に 設定されているメールアドレスの右の [▼] を クリック思案す。
![](/articles/assets/lulzneko/domain/mail/12.png)


----

[Google Domains](https://domains.google/) による メール転送 と [Gmail](https://mail.google.com/) の 送信元設定になります。
最近はメールを使うことも少なくなってきましたが、対外的なコンタクトなどではまだまだ使いますし、[Gmail](https://mail.google.com/) の アドレスよりも、ちゃんと取得したドメインでメール送信できるといいですよね。

メールサーバーを立てるとなると大変なことですし、送受信セットで手軽に使えるクラウドのサービスもまだないように感じます。[Google Domains](https://domains.google/)、アカウント設定、[Gmail](https://mail.google.com/) と 行ったり来たりになりますが、この組み合わせだと比較的容易に設定できるのではないでしょうか。

なによりも、[Gmail](https://mail.google.com/) が そのままつけるのがありがたいです。
