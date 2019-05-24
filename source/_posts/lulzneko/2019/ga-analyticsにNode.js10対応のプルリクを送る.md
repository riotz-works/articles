---
title: ga-analytics に Node.js 10 以上対応のプルリクを送る
permalink: pull-request-to-ga-analytics-about-support-nodejs10
date: 2019-05-24
author: lulzneko
categories: プルリクエスト
tags:
- Node.js
- AWS Lambda
- Hexo
---

AWS Lambda が Node.js 10 に対応したので、Node.js 8 で作っていたプログラムの Node.js 10 対応を進めています。ところどころでエラーが出るので修正。このブログで使っているライブラリにも Node.js 10 で動かした時にエラーの出る部分があったのでプルリクを送りました。

![](/articles/assets/lulzneko/develop/develop.jpg)


2019年5月現在、[Node.js](https://nodejs.org/ja/) の最新バージョンは v12 です。[AWS Lambda が v10 に対応](https://aws.amazon.com/jp/about-aws/whats-new/2019/05/aws_lambda_adds_support_for_node_js_v10/)。ようやくといった感じも受けますが、Node.js の LTS は v10 なので現行ともいえるでしょう。

Node.js のロードマップは、こちら https://nodejs.org/ja/about/releases/
![](/articles/assets/lulzneko/develop/pullreq/ga-analytics/01-001.svg)


AWS Lambda のバージョンアップに合わせて各プログラムのバージョンアップ対応をしています。その中で本ブログの Node.js バージョンを上げたところエラーが発生しました。せっかくなので修正とともにプルリクエストを上げます。
※ 本ブログは Static Site Generator の [Hexo](https://hexo.io/) を使っており、最近の Node.js v12 を使えますが、全体(= AWS Lambda を使ってるプロジェクト)に合わせて Node.js v8 を使っていました。今回も同様に v12 ではなく v10 にします。ただし検証と修正は v12 で動作するところまで確認。

**環境**
本記事の開発環境は以下となります。
- Windows 10 64bit + WSL Ubuntu 18.04.1 LTS
- Visual Studio Code
- Node.js 8.10.0 / 10.15.3 / 12.3.0
- sfarthin/ga-analytics 0.0.7


## プルリクを出すことになった背景
本ブログのプロジェクトを Node.js v10 で起動すると、以下のエラーが表示されます。
```
username@pc:~/website$ yarn dev
yarn run v1.15.2
$ node -r dotenv/config node_modules/hexo-cli/bin/hexo server
fs.js:126
  throw new ERR_INVALID_CALLBACK(cb);
  ^

TypeError [ERR_INVALID_CALLBACK]: Callback must be a function.
    at maybeCallback (fs.js:126:9)
    at Object.writeFile (fs.js:1159:14)
    at /home/username/website/node_modules/ga-analytics/module.js:89:6
    at /home/username/website/node_modules/ga-analytics/module.js:69:7
    at FSReqCallback.readFileAfterClose [as oncomplete] (internal/fs/.js:61:3)
error Command failed with exit code 1.
info Visit https://yarnpkg.com/en/docs/cli/run for documentation about this command.
```

プロジェクト配下の `node_modules/ga-analytics/module.js` の 89行目で `Callback must be a function.` とのこと。

インストールされた npm モジュール [`ga-sfarthin/ga-analytics`](https://github.com/sfarthin/ga-analytics) にトラブルがあるようです。今回、直接インストールしたものではありませんが、ブログの人気記事リストを作る [tea3/hexo-related-popular-posts](https://github.com/tea3/hexo-related-popular-posts) plugin が使っています。

詳しくは、こちらの関連記事をご参照ください。
- [ブログで使っている Hexo に人気の記事リストを表示したい！](https://riotz.works/articles/2019/04/19/want-to-display-list-of-popular-posts-on-hexo-used-in-blog/)
- [Google Analytics にプログラムでアクセスできるようにする](https://riotz.works/articles/2019/04/17/programmatically-access-google-analytics/)

問題を把握したところで重複レポートを避けるために GitHub Issue / Pull Request を確認します。どちらにも該当はありませんでした。
- [Issues · sfarthin/ga-analytics](https://github.com/sfarthin/ga-analytics/issues)
- [Pull Requests · sfarthin/ga-analytics](https://github.com/sfarthin/ga-analytics/pulls)


## エラーの原因特定
では改修できるのか、プルリクを出せるか、ソースを確認します。(ファイル先頭の `require` と、後続処理も合わせて抜粋)
エラーログが指しているのは、下記抜粋の９行目 `fs.writeFile(sessionFile, JSON.stringify(result));` です。
```
var googleapis 	= require('googleapis'),
	OAuth2 		= googleapis.auth.OAuth2,
	fs			= require("fs"),
	_ 			= require("lodash"),
	moment 		= require("moment");

// ...(省略)

		fs.writeFile(sessionFile, JSON.stringify(result));

		oauth2Client.setCredentials({
			access_token: result.access_token,
			refresh_token: result.refresh_token
		});
```

この `fs.writeFile()` は Node.js の基本 API で、ファイルにデータを非同期処理で書き込みをします。非同期処理なので、ファイル書き込み処理が始まると完了を待たずに後続処理、ここでは `oauth2Client.setCredentials({})` へ進みます。
そしてファイルの書き込みが完了すると `fs.writeFile()` のコールバック関数が実行されます。

問題となっているエラーは `Callback must be a function.` です。ざっくり言うと「コールバックは `function(関数)`にして」です。

つまり `fs.writeFile()` にコールバックの関数を渡してほしいと。そして現状どうなっているかというと `fs.writeFile(sessionFile, JSON.stringify(result))` で、**コールバック関数を渡していない**です。
※ コールバック関数は第３または第４引数で渡します。

**参考情報**
- [File System | Node.js v12.3.1 Documentation](https://nodejs.org/api/fs.html#fs_fs_writefile_file_data_options_callback)


## 検証
原因が分かったところで検証します。
今回は完全に独立したコードで検証できるので [TypeScript プロジェクト用サンドボックスで簡単コード検証](https://riotz.works/articles/2019/05/17/easy-poc-with-sandbox-for-typescript-project/) の環境を使います。TypeScript プロジェクトですが、TypeScrip は JavaScript のスーパーセットで JavaScript プロジェクトとしても動作します。検証は簡単に実行できれば、どのような環境でも大丈夫です。

`index.ts` に以下のコードを書きます。
```typescript
const fs = require('fs');
fs.writeFile('/tmp/test.txt', JSON.stringify({ text: 'test'}));
```

まずは、最新の環境 v12 で試します。
エラー内容は TypeScrip になっていますが、エラーメッセージが出ています。
```console
v12.3.0
TypeError [ERR_INVALID_CALLBACK]: Callback must be a function. Received undefined
```

私の環境 v10、メッセージが少し変わりましたがエラーです。
```console
v10.15.3
TypeError [ERR_INVALID_CALLBACK]: Callback must be a function
```

これまでの環境 v8、動作します。
しかしながら `without callback is deprecated`、「コールバック無しは非推奨」の警告が出ています。
```console
v8.16.0
(node:242) [DEP0013] DeprecationWarning: Calling an asynchronous function without callback is deprecated.
```

これでコールバック無しの呼出し方法が「v8 非推奨」が「v10 廃止、エラー」になったことがわかりました。
では、コールバック有りに修正すれば OK です。

検証用の `index.ts` では下記。
```javascript
const fs = require('fs');
fs.writeFile('/tmp/test.txt', JSON.stringify({ text: 'test'}), function(err) {
  console.error(err);
});
```

sfarthin/ga-analytics のコードでは下記(必要箇所のみ抜粋)
```javascript
		fs.writeFile(sessionFile, JSON.stringify(result), function(err) {
			final_callback(err)
		});
```

なお TypeScrip で実装するとすぐにわかるのですが、型チェックエラーで引数が足らずコンパイルも実行もできないです。型チェック素晴らしいですね。
![](/articles/assets/lulzneko/develop/pullreq/ga-analytics/01-002.png)

[補足]
[`sfarthin/ga-analytics`](https://github.com/sfarthin/ga-analytics) の最終更新は 2014-11-21 のバージョン 0.0.7 。当時は Node.js v6 (v8 のリリースは 2017-05-30)なので、非推奨もされてない時代です。
TypeScrip の [Node.js 定義](https://github.com/DefinitelyTyped/DefinitelyTyped/blob/master/types/node/v6/base.d.ts#L2765) も `writeFile(filename: string, data: any, callback?: (err: NodeJS.ErrnoException | null) => void)` と `callback?` なのでオプショナルです。

それから５年間メンテが止まりつつも、実行環境で問題はなかったのかもしれません。v8 は非推奨でも動作するし、LTS でサポートもまた続いているので大きな影響が出てなかったのかもしれません。


## プルリクの作成
さっそくプルリクを作って送ります。
[`sfarthin/ga-analytics`](https://github.com/sfarthin/ga-analytics) のリポジトリから [Fork] で、自分のアカウントにフォークしてローカルへクローンします。
ボタンを押してクローするだけなので今回は省略しますが、詳しい手順を確認したい場合はこちらをご参照ください。
- [フォークしてプルリク作成の環境用意 - hexo-related-popular-posts にサブパス対応のプルリクを送る](https://riotz.works/articles/2019/04/20/pull-request-to-hexo-related-popular-posts-about-subpath-enablement/#%E3%83%95%E3%82%A9%E3%83%BC%E3%82%AF%E3%81%97%E3%81%A6%E3%83%97%E3%83%AB%E3%83%AA%E3%82%AF%E4%BD%9C%E6%88%90%E3%81%AE%E7%92%B0%E5%A2%83%E7%94%A8%E6%84%8F)
- [フォークしてプルリク作成の環境用意 - Validate TypeScript にインストールエラーの修正についてのプルリクを送る](https://riotz.works/articles/2019/03/13/pull-request-to-validate-typescript-about-installation-errors/#%E3%83%95%E3%82%A9%E3%83%BC%E3%82%AF%E3%81%97%E3%81%A6%E3%83%97%E3%83%AB%E3%83%AA%E3%82%AF%E4%BD%9C%E6%88%90%E3%81%AE%E7%92%B0%E5%A2%83%E7%94%A8%E6%84%8F)

今回も単発のプルリクなので、１つしかない `master` ブランチへ直接コミットして上げてしまいます。
ただし、今回は注意点があります。
- インデントがタブ
- 空行もインデントされている
- ファイル末尾に改行コードがない

これを壊さないように変更しコミットする必要があります。
私の環境の Visual Studio Code と Eclipse は、すべて反対の設定になっているため難しいです。今回はコンソール WSL Ubuntu 18.04.1 LTS から `vi` で修正することにしました。しかし `vi` でもファイル末尾に改行コードを自動的に入れてくるので、保存する前に `:set binary noeol` を実行してから保存します。(お使いの vi/vim によって異なるかもしれません)
![](/articles/assets/lulzneko/develop/pullreq/ga-analytics/01-003.png)

フォークした自分のリポジトリから [New pull request] ボタンでプルリクを作り送ります。
![](/articles/assets/lulzneko/develop/pullreq/ga-analytics/01-004.png)

取り込まれますように 🙏
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://github.com/sfarthin/ga-analytics/pull/2" data-iframely-url="//cdn.iframe.ly/teblWd5"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>



----

Node.js のバージョンアップに伴いエラーが発生したのを修正するプルリクでした。今回はコーディング規約(明文化されてないけど読み取って)の部分で苦戦しました。普段のコミットも同じですが、本来変更したい部分と関係ないところに差分があると考えてしまいます。無用な差分が発生しないように変更してプルリクを出したいところです。(自分のプロジェクトだとつい許容してしまいますが、できればコミットを分けたいところ)

そして "[オープンソース フライデー | 今週の金曜日は、利用しているお気に入りのソフトウエアを数時間助けてみよう。](https://opensourcefriday.com/)" に登録してみました。(typo してる。。。 ⇒ ×始めて見る　〇はじめてみる)
<blockquote class="twitter-tweet" data-lang="ja"><p lang="ja" dir="ltr">ちょっとずつだけど、始めて見る<a href="https://t.co/6mPGDlJ6kq">https://t.co/6mPGDlJ6kq</a> <a href="https://t.co/BvRQ6KQwps">pic.twitter.com/BvRQ6KQwps</a></p>&mdash; lulzneko (ラルズネコ) (@lulzneko) <a href="https://twitter.com/lulzneko/status/1131196885023088640?ref_src=twsrc%5Etfw">2019年5月22日</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

直近３ヶ月、本記事を含めて月１つのペースでプルリクを出していました。そして、ブログメンターの [カック@ブロガー / k9u(@kakakakakku)さん](https://twitter.com/kakakakakku) とお話をしている中で「自分が使っているプロダクトの Issues を修正するのは喜ばれることだし、自分も助かる。２倍嬉しい。」と勧められました。エラーの修正は必然なのでともかく、機能の追加改善などは自分のためだけでなくみんなのためにするべきです。前回の記事 [ブログで使っている Hexo の SNS 共有リンクに記事タイトルを入れる](http://localhost:4000/articles/2019/05/22/add-article-title-to-sns-share-link-of-hexo-used-in-blog/) なども自分だけではなくプルリクで上げたほうが良かったです(後で上げます)。そうしたことから、"オープンソース フライデー" として、何かしらの OSS 活動を意識するような日にしたいと考えました。金曜日は OSS の取り組みを少しだけでもするようにします。

**参考情報**
- [オープンソース フライデー | 今週の金曜日は、利用しているお気に入りのソフトウエアを数時間助けてみよう。](https://opensourcefriday.com/?locale=ja)
- [「Open Source Friday」をGitHubが提唱。金曜日は自分の好きなオープンソースに貢献しよう － Publickey](https://www.publickey1.jp/blog/17/open_soruce_friday_github.html)
- [OSS（オープンソースソフトウェア）の開発に参加したいあなたへ：新春特別企画｜gihyo.jp … 技術評論社](https://gihyo.jp/dev/column/newyear/2018/start-oss-development?page=1)
