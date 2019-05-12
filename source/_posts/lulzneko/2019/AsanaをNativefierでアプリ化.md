---
title: Asana を Nativefier でアプリ化する
permalink: make-asana-app-with-nativefier
date: 2019-05-03
author: lulzneko
categories: 開発環境
tags:
- Asana
---

タスク管理の Asana、スマートフォンアプリはあるのですがデスクトップアプリが提供されておらずブラウザで使います。Google Chrome でアプリウィンドウ化ができますが、常駐化をするなどの高度な使い方をしたい場合は Node.js の Nativefier を使ってアプリ化をする方法があります。Node.js の実行環境とコマンドラインでの操作が必要となりますが、手軽にアプリ化ができます。

![](/articles/assets/lulzneko/develop/asana/asana.png)


前回 [Google Chrome を使って Asana をアプリウィンドウ化](/articles/2019/05/02/make-asana-app-window-with-google-chrome/) をしましたが、ウィンドウを閉じてしまうとウェブアクセスから始まり起動に時間がかかります。常時使うようなサービスなので常駐化し、いつでも使えるようにしたいです。

公式フォーラムでもデスクトップアプリのニーズは [What's the status of a native Asana Mac app? - Product Feedback - Asana Community Forum](https://forum.asana.com/t/whats-the-status-of-a-native-asana-mac-app/1581) のように 2017年と古くから上がっているようですが進展はなさそうです。

今回は、フォーラムでも紹介されている Nativefier を使いアプリ化し常駐、必要な時にすぐに利用できるようにしたいと思います。

### 環境
本記事の開発環境は以下となります。
- Windows 10 64bit
- Node.js 12.1.0
- Nativefier 7.6.12


## Nativefier とは
[Nativefier](https://github.com/jiahaog/nativefier) は、ウェブページをデスクトップアプリ化する Node.js のツールで、ウェブサイトを Electron でラップした Windows, macOS, Linux 用のアプリを作成します。

[Electron](https://electronjs.org/) は、ウェブの技術である HTML/CSS/JavaScript を使ってクロスプラットフォームのデスクトップアプリを作るツールで Atom や Visual Studio Code などのエディター、Slack や Discord などのチャットクライアント等、さまざまなアプリで使われています。

ただし Electron アプリはメモリ消費が大きく、１アプリで数百メガ (私の Asana のケースでは 250MB) 使うのでアプリの作りすぎには注意が必要です。

Nativefier は Node.js 6 以上で動作します。インストールされていない場合は、[Node.js 公式サイト](https://nodejs.org/ja/) にしたがってインストールしてください。今回は Node.js 12.1.0 を使いました。


## Nativefier で Asana をアプリ化
以下のコマンドで Asana をアプリ化します。
```console
C:\Temp> npx nativefier -n "Asana" --internal-urls "accounts\.google\.com.*" --single-instance --tray "https://app.asana.com"
```

指定したオプションは下記となります。
- `-n` は、アプリ名(＝ディレクトリと実行ファイル名)
- `--internal-urls` は、Google 認証をウィンドウ内で行えるようするため
- `--single-instance` は、アプリの起動を１つだけにし、ウィンドウを複数開かないため
- `--tray` は、システムトレイに常駐させます

Google 認証については、GitHub に Issues が上がっています。いくつかパターンがありますが、今回は一番狭いスコープで設定する [#282 @tianhuil さん](https://github.com/jiahaog/nativefier/issues/282#issuecomment-479677143) のを使わせてもらいました。情報ありがとうございます！
- https://github.com/jiahaog/nativefier/issues/282
- https://github.com/jiahaog/nativefier/issues/164

オプション最後の `https://app.asana.com` は、アプリ化する URL で、アプリ起動時に表示したい URL たとえば Asana マイタスクなども指定できます。システムトレイに常駐させるならば起動後は最後に見た状態を覚えているのでトップページの URL がよいでしょう。

また Node.js の npm モジュールのグローバル汚染回避と、使う頻度が少く常に最新版を使いたいため `npx` で実行しました。`npx` はローカルに存在しないモジュールを実行する場合はダウンロードして実行し、実行後はダウンロードしたファイルを削除します。そのためグローバルにモジュールを追加するよりもクリーンですが、毎回モジュールをダウンロードすることになるので試行錯誤したりする場合は、グローバルに追加して実行した方が効率的です。
グローバルに追加して実行するには下記のコマンドとなります。
```console
C:\Temp> yarn add --global nativefier
C:\Temp> nativefier -n "Asana" --internal-urls "accounts\.google\.com.*" --single-instance --tray "https://app.asana.com"
```
※ npm を使う場合は `npm -g nativefier`


## Asana アプリ
Nativefier を実行すると、実行したディレクトリにアプリが作られます。
ディレクトリ名が `Asana-win32-x64` になっていますが変更したり、別の場所に移動しても大丈夫です。
![](/articles/assets/lulzneko/develop/asana/21.png)

ディレクトリ内の `Asana.exe` をダブルクリックするとアプリが起動します。
ログイン画面から始まるので、使っているアカウントでログインします。
![](/articles/assets/lulzneko/develop/asana/22.png)

システムトレイにアイコンが配置され常駐していることがわかります。ウィンドウを閉じても常駐されているので、システムトレイのアイコンから最後の状態で表示されます。
今回はシングルインスタンスにしているので `Asana.exe` を繰り返しダブルクリックしたり、ショートカットから起動させてもウィンドウは１つで、最初に起動したウィンドウが手前に表示されます。

終了するにはシステムトレイのアイコンを右クリックして [Quit] を選びます。
常駐型のアプリと同じように扱うことができます。


## Nativefier の、その他のオプション
Nativefier には、たくさんのオプションがあり柔軟に設定を行うことができます。
主なオプションを紹介します。(詳しくは `--help` を参照ください)

プラットフォームを指定してクロスビルドできます。
- `-p, --platform <value>` は、プラットフォームで 'osx', 'mas', 'linux', 'windows' から指定できます
- `-a, --arch <value>` は、CPU アーキテクチャで 'ia32', 'x64', 'armv7l' から指定できます

macOS の場合はバッジを付けることができるようです。(macOS がないので試せない ＆ Asana は何がバッジされるんだろう🤔)
- `--counter` は、ドックでカウンターのバッジを付けます
- `--bounce` は、カウンターが増えた時にバウンドします

アプリの挙動を柔軟に設定できます。
- `--tray start-in-tray` は、システムトレイ常駐で初回起動時にウィンドウを表示させません
- `--always-on-top` は、ウィンドウを常にトップに表示します



----

Nativefier を使うことで簡単にウェブサイトをアプリ化できます。Asana に限らず、よく使うサービスでデスクトップアプリがない場合に Nativefier を使うと手軽にアプリ化できるのでとても便利です。

一方であくまでもウェブサイトをラップしているだけなので、アプリらしい機能はありません。やはり公式でアプリをリリースされることが望ましです。Asana アプリ、リリースされないかな。。。
