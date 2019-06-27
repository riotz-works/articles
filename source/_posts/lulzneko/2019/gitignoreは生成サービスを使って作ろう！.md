---
title: .gitignore は、生成サービス gitignore.io を使って作ろう！
permalink: lets-create-gitignore-using-generation-service-gitignoreio
alias: /2019/06/18/lets-create-gitignore-using-generation-service-gitignoreio/index.html
date: 2019-06-18
author: lulzneko
categories: 開発環境
tags:
- Git
- Node.js
---

Git のソースコード管理から特定のファイルやディレクトリを除外するのに .gitignore を使います。その .gitignore ファイル、手作りしていませんか？
gitignore.io のサービスを使うことでベストプラクティスを盛り込んだ .gitignore を手軽に作ることができます。素晴らしいサービスを活用して快適な .gitignore ライフを送りましょう！

![](/articles/assets/lulzneko/develop/develop.jpg)


## gitignore.io とは
[gitignore.io](https://www.gitignore.io/) は、"Create Useful .gitignore Files For Your Project" をタイトルに掲げている .gitignore 生成サービスです。

Java や Python, Node.js といった各種プログラミング言語や実行環境から、フレームワーク、開発環境、OS とさまざまな環境に応じた .gitignore を生成できます。先人たちの知恵が終結した、まさにベストプラクティスな .gitignore を簡単に手に入れることができるサービスです。

また生成元のテンプレートも [GitHub で公開](https://github.com/dvcs/gitignore) されているので、公式に追加してもらうこともできます。
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://github.com/dvcs/gitignore" data-iframely-url="//cdn.iframe.ly/vBr10LL"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>


## ブラウザを使って生成
まずは使い方を確認するためにブラウザでアクセスして .gitignore を生成してみます。

ブラウザで https://www.gitignore.io/ へアクセスします。
画面中央に入力フォームがあるので、生成したい環境を入力します。
![](/articles/assets/lulzneko/develop/gitignore/01-001.png)

インクリメンタルサーチが効いているので入力しながら確認ができます。また `JavaScript` は `Node` など、別名や集約されているケースがあるので、見つからない場合は、いくつか試してみてください。(どうしても見つからない場合や全候補が見たい場合は https://www.gitignore.io/api/list へ)

入力できると候補が確定します。欲しい組み合わせがある場合は検索ワードを追加し、完成したら [Create] ボタンをクリックします。
![](/articles/assets/lulzneko/develop/gitignore/01-002.png)

.gitignore が生成されます。
よくよく眺めてみると手入力では追加し忘れがちな `yarn-error.log` なども入っていて助かります。また逆に使っていないものも入っていますが、私は気にせずマルっと入れて使っています。
![](/articles/assets/lulzneko/develop/gitignore/01-003.png)


## コマンドラインから生成
ブラウザからコピペもいいですが、コマンドラインからサクッと作ってしまいたいところです。
先ほどのブラウザで生成した .gitignore の URL を確認すると `https://www.gitignore.io/api/node,nuxt` のようになっています。`https://www.gitignore.io/api/` に続いて検索ワードをカンマ切りで追加しています。この URL を使うことで好みの .gitignore を `curl` などのコマンドから取得できます。
```console
$ curl https://www.gitignore.io/api/node,nuxt

# Created by https://www.gitignore.io/api/node,nuxt
# Edit at https://www.gitignore.io/?templates=node,nuxt

### Node ###
# Logs
logs
*.log
npm-debug.log*

...(省略)
```

直接 `.gitignore` ファイルへ書きだしてしまえば完成です。
```console
$ curl -s https://www.gitignore.io/api/node,nuxt > .gitignore
$ ls -l .gitignore
-rw-rw-rw- 1 lulzneko lulzneko 1573  6月 18 11:14 .gitignore
```

コマンドラインからはインクリメンタルサーチの補完が効かず、また正確なキーワードが必要となります。キーワードがわからない場合は `https://www.gitignore.io/api/list` から全リストを取得して確認します。
```console
$ curl https://www.gitignore.io/api/list
1c,1c-bitrix,a-frame,actionscript,ada
adobe,advancedinstaller,agda,al,alteraquartusii
altium,android,androidstudio,angular,anjuta
ansible,apachecordova,apachehadoop,appbuilder,appceleratortitanium
... (省略)
```

コマンドラインからも使えることで、プロジェクトの初期設定時に手軽に実行して .gitignore が作れるので助かります。



----

使っている環境に合わせて .gitignore を作れるのが嬉しいですね。新しいプロジェクトを作る時にサクッと生成して、すぐ次の作業や検討に入れるのがとてもよいです。

またプロジェクト固有の除外指定は、生成後に自分で追加できるので運用もあまり変わりなく、最初にプロジェクトメンバーの知見を集めるか、先人の知恵を借りるのかの違いといえるでしょう。むしろ率先してプルリクを出し先人の知恵に参加していきたいところです。
