---
title: Outlook で、自分のアドレスを常に Cc に入れる
permalink: always-put-my-address-in-cc-in-outlook
date: 2019-08-23
author: lulzneko
categories: 開発環境
tags:
---

マイクロソフトのメール＆スケジュール管理ソフトの Outlook。この Outlook、メールを送る際に自分を Cc なり Bcc に設定しておきたいのですが、設定方法が見つかりません。そう、設定ではなくルール、仕分けルールとして定義する必要があるのです。設定方法について紹介します。

![](/articles/assets/lulzneko/domain/mail/mail.jpg)


Outlook、主に仕事で使っているます。自分が送ったメールは [送信済みアイテム] にあるので基本的には問題ないのですが、自分で仕分けしていたり、レスがなかったりすると送ったメールがどこ行ったか分からなくなるので、Cc か Bcc で自分宛にも送っておきたいところです。

基本的には一度定義すると Exchange Server に保存されているので、改めて定義する必要がないのですが何かの拍子に消えていたり、設定方法を聞かれたりするので備忘録。

**環境**
本記事の開発環境は以下となります。
- Windows 10 64bit
- Office 365


## 仕分けルールの作成
Outlook を起動し、メール機能(受信トレイ)を表示します。
リボンの [移動グループ] から [ルール] - [仕分けルールと通知の管理] をクリックします。
※ [仕分けルールの作成] からは定義できません
![](/articles/assets/lulzneko/client/outlook/01-001.png)

仕分けルールと通知の管理ダイアログから、[新しい仕分けルール] をクリックします。
![](/articles/assets/lulzneko/client/outlook/01-002.png)

ウィザードが開始されるので [送信メッセージにルールを適用する] を選択し [次へ] をクリックします。
([仕分けルールと通知の管理] から来ないと、このメニューがないのでハマった。。。)
![](/articles/assets/lulzneko/client/outlook/01-003.png)

続いて条件指定の定義選択が表示されます。
**何もチェックをつけず** に [次へ] をクリックします。
![](/articles/assets/lulzneko/client/outlook/01-004.png)

条件なし、すなわち、すべての送信メッセージ。[はい] をクリックします。(わかりにくい。。)
![](/articles/assets/lulzneko/client/outlook/01-005.png)

処理の定義選択が表示されます。
[名前/パブリックグループをメッセージの [CC] に追加する]へチェックをします。
ダイアログ下段の [ステップ 2] に表示されている [名前/パブリックグループ] をクリックし、アドレス帳から自分のメールアドレスを選択します。
![](/articles/assets/lulzneko/client/outlook/01-006.png)

例外条件の定義選択が表示されます。
自分宛のメールの場合は追加する必要がないので、[[宛先] または [CC] が名前/パブリックグループの場合を除く] にチェックします。
ダイアログ下段の [ステップ 2] に表示されている [名前/パブリックグループ] をクリックし、アドレス帳から自分のメールアドレスを選択します。
![](/articles/assets/lulzneko/client/outlook/01-007.png)

最後に [仕分けルールの名前] を入力し [完了] をクリックします。(ここでは Cc: Self としましたが、良いネーミングを考えたい)
※ 下図は [名前/パブリックグループ] になってますが、実際には自分の名前が表示されている状態になります
![](/articles/assets/lulzneko/client/outlook/01-008.png)

クライアントサイドのルールであること警告されます。Exchange Server 内で処理されず Outlook のソフトウェアだけで適用されます。とくに問題ありません。
(ウェブアクセス版では、このルールが適用されないのでウェブアクセス版を使う場合は注意が必要です)
![](/articles/assets/lulzneko/client/outlook/01-009.png)

無事、追加されました。
![](/articles/assets/lulzneko/client/outlook/01-010.png)

メール作成の際には、Cc は空ですが送信時には追加されています。
![](/articles/assets/lulzneko/client/outlook/01-011.png)




----

設定できているもののメール作成画面で表示されないので、ちょっと安心できない ＆ 設定が消えていることに気づきにくい。。。

できれば Bcc がよいのですが、良い設定方法が見つからず、今回の設定を使っています。
(長年慣れ親しんだ [Becky! Internet Mail](http://www.rimarts.co.jp/becky-j.htm) が使いたいんだけどなぁ)
