---
title: Asana を Google Chrome でアプリウィンドウ化する
permalink: make-asana-app-window-with-google-chrome
alias: /2019/05/02/make-asana-app-window-with-google-chrome/index.html
date: 2019-05-02
author: lulzneko
categories: 開発環境
tags:
- Asana
---

タスク管理の Asana、スマートフォンアプリはあるのですがデスクトップアプリが提供されておらずブラウザで使います。ブラウザで使っているとタブの中に埋もれてしまい欲しいときにすぐ使えなかったり、ブラウザごとまとめて閉じられてしまうことなどがあります。もっと快適に利用できるよう Google Chrome でアプリのように扱う方法を紹介します。

![](/articles/assets/lulzneko/develop/asana/asana.png)


今回は Google Chrome の機能を使ってアプリウィンドウ化します。この機能について一時期使えなくなっていたこともあり、環境によってはうまく動作しないかもしれません。今回の動作確認環境は下記となります。(Google Chrome にメニューがなかったりなど今回の方法でできなかったり、Google Chrome を使っていない場合は、次回投稿予定のアプリ化の方法をご検討ください)

**環境**
本記事の開発環境は以下となります。
- Windows 10 64bit
- Google Chrome 74.0.3729.108


## Google Chrome でショートカットの作成
まずは Google Chrome で Asana https://app.asana.com/ へログインします。
現在ブラウザで表示しているページが、アプリのトップになるので [マイタスク] やプロジェクトなど、最初に出てほしいページを表示しておきます。今回は [マイタスク] としました。
![](/articles/assets/lulzneko/develop/asana/11.png)

右上の [メニューアイコン] - [その他のツール] - [ショートカットを作成] を選びます。
![](/articles/assets/lulzneko/develop/asana/12.png)

ショートカットの作成ダイアログが表示されます。
[ショートカットの名前] を入力し、[ウィンドウとして開く] にチェックを付け [作成] をクリックします。
![](/articles/assets/lulzneko/develop/asana/13.png)

ブラウザが終了し、アプリウィンドウとして Asana が表示されます。
あとはアプリとして独立したウィンドウのため、ブラウザを閉じても巻き込まれることはありません。
![](/articles/assets/lulzneko/develop/asana/14.png)


## スタートメニューとタスクバーへの登録
Google Chrome のアプリとして登録されているので、起動は `chrome://apps/`  またはブックマークバーの [アプリ] からとなります。
※ ブックマークバーは [Ctrl + Shift + B] で表示できます
![](/articles/assets/lulzneko/develop/asana/15.png)

起動をもう少し簡単にするため、スタートメニューへ登録します。
Google Chrome の [アプリ] にあるアイコンを右クリックし [ショートカットを作成] を選びます。
![](/articles/assets/lulzneko/develop/asana/16.png)

ショートカット作成のダイアログが表示されるので、必要なショートカットを選び [作成] をクリックします。
今回は両方作成しました。
![](/articles/assets/lulzneko/develop/asana/17.png)

デスクトップまたはスタートメニューのアイコンの右クリックからタスクバーにも追加できます。
これによりタスクバーからもいつでも起動することができるようになります。
![](/articles/assets/lulzneko/develop/asana/18.png)



----

タスク管理の Asana をデスクトップアプリ風にしました。これにより簡単に起動できますし、ブラウザのウィンドウやタブに紛れて探す必要もなくなります。なによりブラウザの閉じるでまとめて消えてしまわないのが助かります。

この方法は Asana だけではなく、ほとんどのウェブサイトで同様のことができます。よく使うサイトをアプリウィンドウ化することで便利に使うことができるようになります。

次回は Google Chrome の機能ではなく、アプリ化する方法を紹介します。
