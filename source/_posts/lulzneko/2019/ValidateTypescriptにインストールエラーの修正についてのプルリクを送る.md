---
title: Validate TypeScript にインストールエラーの修正についてのプルリクを送る
permalink: pull-request-to-validate-typescript-about-installation-errors
date: 2019-03-13
author: lulzneko
categories: プルリクエスト
tags:
- TypeScript
---

Web API の リクエスト パラメーター を 入力チェックするのによさそうな Validate TypeScript というライブラリを見つけ使おうとしたところ、インストールエラーが発生してしまいました。
こちらをプルリクエストで修正してもらいましたので、プルリクエストの手順と合わせて整理します。

![](/articles/assets/lulzneko/develop/develop.jpg)


## Validate TypeScript
[Validate TypeScript](https://github.com/Grant-Zietsman/validate-typescript) - https://github.com/Grant-Zietsman/validate-typescript はスキーマベースのバリデーターで、JSON などのオブジェクトの値や型をチェックできます。
![](/articles/assets/lulzneko/develop/validate-typescript/01.png)


## プルリクを出すことになった背景
ドキュメントにしたがって `npm install` するとエラーとなります。また `npm install -S` とセーブオプションを付けてもエラーなので `package.json` に反映されませんし `node_modules` ディレクトリにも保存されません。
```console
lulzneko@PC:/tmp/project$ npm install validate-typescript

> validate-typescript@4.0.0 install /tmp/project/node_modules/validate-typescript
> cd test && npm install

sh: 1: cd: can't cd to test
npm ERR! code ELIFECYCLE
npm ERR! errno 2
npm ERR! validate-typescript@4.0.0 install: `cd test && npm install`
npm ERR! Exit status 2
npm ERR!
npm ERR! Failed at the validate-typescript@4.0.0 install script.
npm ERR! This is probably not a problem with npm. There is likely additional logging output above.

npm ERR! A complete log of this run can be found in:
npm ERR!     /home/lulzneko/.npm/_logs/2019-03-13T25_67_89_000Z-debug.log
```

普段は [Yarn](https://yarnpkg.com/) を使っているのですが、こちらも同様にエラーとなり `package.json` に反映されません。しかしながらダウンロードはできているので `node_modules` に保存されます。CI とかで環境が変わると、パッケージ名を明示してインストールはしないので、そちらではエラーとなるので注意が必要です。
```console
lulzneko@PC:/tmp/project$ yarn add validate-typescript
yarn add v1.13.0
[1/4] Resolving packages...
[2/4] Fetching packages...
[3/4] Linking dependencies...
[4/4] Building fresh packages...
error /tmp/project/node_modules/validate-typescript: Command failed.
Exit code: 2
Command: cd test && npm install
Arguments:
Directory: /tmp/project/node_modules/validate-typescript
Output:
/bin/sh: 1: cd: can't cd to test
info Visit https://yarnpkg.com/en/docs/cli/add for documentation about this command.
```


## インストール・エラーとなる原因
どうも `cd test && npm install` というコマンドを実行しようとして `test` ディレクトリが見つからないためエラーとなっているようです。
普段 [Yarn](https://yarnpkg.com/) を使っているので `npm install` は出てこないでしょうし `cd test` することもないです。

自分のプロジェクト内で問題となってるわけではなないようなので [Validate Typescript](https://github.com/Grant-Zietsman/validate-typescript) に何かの処理があるのではないと探ります。

利用しようとしていた v4.0.0  cf81b3b のファイルを見ると `scripts` に `"install": "cd test && npm install",` があります。
これが実行されてエラーとなっているようです。
https://github.com/Grant-Zietsman/validate-typescript/blob/cf81b3b17a2b37f2fb7d72006eff9bc28519220c/package.json#L12
![](/articles/assets/lulzneko/develop/validate-typescript/02.png)

Yarn だとダウンロードできているので、ディレクトリを確認したところ `test` ディレクトリは含まれていませんでした。
またインストールに成功する Version 3.0.0 にも `test` は含まれていません。
パッケージングの忘れか、意図しない実行のいずれかなのでしょうか 🤔


## 変更履歴の確認
ひとつ前の Version 3.0.0 はインストールに成功するので、Version 4.0.0 リリースに至るまでに変化があったといえます。

`3.0.0 3e3c296` は `"setup": "cd test && npm run setup",` 、`install` でなく `setup` という名前で作られていたようです。
これならインストール時ではなく、明示的に呼び出した時に実行されるので問題ないです。
https://github.com/Grant-Zietsman/validate-typescript/blob/3e3c2967150ae88c828b1479576ba922227c2eb7/package.json#L12

次の `Fixed outstanding issues, except feature request. 5dce751` でエラーとなっている `install` に変わっています。
変更が多数あるのですがコミットが分かれておらず、またコメントからもちょっと推測しにくいところです。
https://github.com/Grant-Zietsman/validate-typescript/commit/5dce751b6cea0870be3287835befa437ed37c8cf#diff-b9cfc7f2cdf78a7f4b91a753d10865a2

残念ながら変更意図の理解につながりそうなコメントなどはなさそうでした。
とりあえず、元に戻すプルリクを出して相談してみることにします。

-----

勝手な推測ですが、[Validate Typescript](https://github.com/Grant-Zietsman/validate-typescript) 自体の開発を行う際に外部依存ライブラリ(現時点だと `"chalk": "^2.3.1"`) を `npm install` します。その時に合わせて、テストで依存している外部ライブラリのインストールも一緒に行いたかったのではないでしょうか。

Version 3.0.0 だと、以下の手順が必要です。
```console
$ npm install
$ npm run setup
cd test && npm run setup
```

これが Version 4.0.0 だと、１回。
```console
$ npm install
cd test && npm run setup
```

開発環境を便利にした反面、これが利用者側のインストールするときにも同じことが起こるとは想定外だったのかなと想像します。


## フォークしてプルリク作成の環境用意
プルリクを出すために、まずは元リポジトリをフォークして、プルリク作成の環境を作ります。

[Validate Typescript](https://github.com/Grant-Zietsman/validate-typescript) のリポジトリへ行き、画面右上の [fork] ボタンをクリックします。
![](/articles/assets/lulzneko/develop/validate-typescript/03.png)

リポジトリをスキャンしてる画像が表示されます。いい感じ。
![](/articles/assets/lulzneko/develop/validate-typescript/04.png)

無事フォークが完了し自分のリポジトリが作られます。
フォークしたことが [forked from Grant-Zietsman/validate-typescript] の表示で分かります。
![](/articles/assets/lulzneko/develop/validate-typescript/05.png)

後はいつも通りクローンして作業環境を用意します。
```console
lulzneko@PC:~$ git clone git@github.com:lulzneko/validate-typescript.git
Cloning into 'validate-typescript'...
remote: Enumerating objects: 44, done.
remote: Counting objects: 100% (44/44), done.
remote: Compressing objects: 100% (30/30), done.
remote: Total 450 (delta 16), reused 32 (delta 12), pack-reused 406
Receiving objects: 100% (450/450), 758.22 KiB | 1.76 MiB/s, done.
Resolving deltas: 100% (240/240), done.
```


## フォークした自分のリポジトリに変更を加える
プルリクを送りたい変更を、まずはフォークした自分のリポジトリに行います。
しっかりやるにはブランチを切って作業したほうが良いかと思いますが、今回は `develop` ブランチがあることと、パッチ１個という感じで継続的にコントリビュートしていわけではないので、`develop` ブランチへ直接変更を加えてしまいます。
```console
lulzneko@PC:~/validate-typescript$ git branch -a
* master
  remotes/origin/HEAD -> origin/master
  remotes/origin/develop
  remotes/origin/master

lulzneko@PC:~/validate-typescript$ git checkout -b develop origin/develop
Branch 'develop' set up to track remote branch 'develop' from 'origin'.
Switched to a new branch 'develop'
```

**変更の方針**
- `package.json` の `install` を `setup` に戻す
- 開発環境のセットアップが１回で行える機能は残す

よって、以下のように修正します (`script` 部分を抜粋)
```javascript
"setup": "npm install && cd test && npm install",
```

これによって開発環境のセットアップは `npm run setup` の １回です。
それでいて `npm install` と `cd test && npm install` の ２つが実行できます。

修正できたら、コミット＆プッシュ！
```console
lulzneko@PC:~/validate-typescript$ git add package.json

lulzneko@PC:~/validate-typescript$ git commit -m "Fix error when this npm module user installs"
[develop f3ec461] Fix error when this npm module user installs
 1 file changed, 1 insertion(+), 1 deletion(-)

lulzneko@PC:~/validate-typescript$ git push origin develop
Counting objects: 3, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 337 bytes | 25.00 KiB/s, done.
Total 3 (delta 2), reused 0 (delta 0)
remote: Resolving deltas: 100% (2/2), completed with 2 local objects.
To https://github.com/lulzneko/validate-typescript.git
   482bf65..f6a9885  develop -> develop
```


## プルリクの作成
フォークした自分のリポジトリに変更が反映され、プルリク作成ボタンが表示されるので [Compare & pull request] ボタンをクリックします。
![](/articles/assets/lulzneko/develop/validate-typescript/06.png)

プルリク作成フォームが表示されるので、確認と内容を記入し [Create pull request] ボタンをクリックします。
- 左の [base] が元のリポジトリ＆ブランチになっていることを確認
- 右の [head] がフォークした自分のリポジトリ＆作業ブランチになっていることを確認
- プルリクのタイトルと内容を記入
![](/articles/assets/lulzneko/develop/validate-typescript/07.png)

元のリポジトリにプルリクが作られました！マージされといいな。
![](/articles/assets/lulzneko/develop/validate-typescript/08.png)

[Fix error when this npm module user installs by lulzneko · Pull Request #7 · Grant-Zietsman/validate-typescript](https://github.com/Grant-Zietsman/validate-typescript/pull/7)


## 一晩でマージされた！
この記事をつらつらと書いているうちに一晩でマージされて、Version 4.0.1 がリリースされました。はやっ！！
マージされたのでブランチの削除と、必要に応じてフォークしたリポジトリを削除します。プルリクのフォームにボタンがあるので簡単です。
![](/articles/assets/lulzneko/develop/validate-typescript/06.png)



----

無事、インストールエラー問題が解決され、パッチされたバージョンがリリースされました。

ホントはライブラリ紹介を書こうとしていたのですが、先にプルリクとなり。せっかくなのでプルリクの流れも整理して書いておこうと思ったら、あっという間にマージしてくれたのでびっくりです。
2019年3月14日 0:40 JST にプルリクして、2019年3月14日 1:28 JST にリリースの返事。１時間していないという。[Grant Zietsman](https://github.com/Grant-Zietsman) さん、ありがとうございます！

※ なお英語はまったくできないのでプルリクの文章はグーグルです。
