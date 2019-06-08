---
title: Git の設定をリポジトリごとに自動で使い分ける
permalink: automatically-swich-git-configuration-for-each-repository
date: 2019-06-08
author: lulzneko
categories: 開発環境
tags:
- Git
---

Git で開発をしている中でリポジトリによって設定を変えたいことがあります。たとえばコミット時の名前やメールアドレスなどを使い分けたいといったケースです。このような場合に有用な Git の Conditional includes を使った自動切り替えの方法について紹介します。

![](/articles/assets/lulzneko/develop/develop.jpg)


[Git](https://git-scm.com/) でコミットする際の名前やメールアドレス、多くの場合は全体設定 `git config --global` にしているかと思います。通常は問題ないのですが、リポジトリによって設定を変えたいこともあります。そのような場合は `git config --local` を使いリポジトリ単体の設定ができます。

しかしながら、そのようなリポジトリが増えてくるとクローンするたびごとに設定が必要となり手間です。場合によっては設定忘れでエラーになったり、悪い場合はアカウント名を間違えてコミットすることもあり得ます。できればリポジトリをまとめるディレクトリ単位などで設定したいところです。

今回は特定のパスに配置したリポジトリへ設定をインクルードする [Conditional includes](https://git-scm.com/docs/git-config#_conditional_includes) を使い、設定をリポジトリごとに自動で使い分けるような仕組みを紹介します。

**環境**
本記事の開発環境は以下となります。
- Windows 10 64bit + WSL Ubuntu 18.04.1 LTS
- Git 2.21.0.windows.1 (Windows 10 64bit)
  Git 2.17.1 (WSL Ubuntu 18.04.1 LTS)

**参考情報**
- [Conditional includes - git-config Documentation](https://git-scm.com/docs/git-config#_conditional_includes)


## Git Conditional includes とは
バージョン 2.13.0 から追加された機能で、条件へマッチした場合に指定された設定ファイルをインクルードします。

条件は `gitdir` と `gitdir/i` で指定します。
どちらも glob パターンでパスを指定して、現在のディレクトリがパターンにマッチするかで判定されます。違いは `gitdir/i` が大文字小文字を区別せずに判定します。


## 自動切り替えの設定例
設定は git config --global includeIf."<条件>".path "<設定ファイル>" のコマンドを実行します。
(とはいえコマンドは分かりにくいので、設定ファイルを直接編集してます)

たとえば、ホームディレクトリ以下の `repos` に `work` と `oss` を作り、その下に関連するリポジトリを配置しているとします。
```console
~/repos
|-- work
|   `-- wondrous-product
`-- oss
    |-- astounding-software
    `-- local-config-service
```

それぞれのディレクトリ名をインクルードするファイル名の一部に使い以下のようにしました。
※ 末尾が `/` で終わる場合は、暗黙的に `/**` の glob パターンになります
```console
$ git config --global includeIf."gitdir:~/repos/work/".path ".gitconfig_work"
$ git config --global includeIf."gitdir:~/repos/oss/".path ".gitconfig_oss"
```

上記コマンドを実行した際の設定ファイル `~/.gitconfig` は以下のようになります。(必要箇所のみ抜粋)
※ 今回はユーザー名とメールアドレスを切り替える想定なので `useConfigOnly = true` でデフォルトのユーザー名を無効にしています
```config
[user]
	useConfigOnly = true

[includeIf "gitdir:~/repos/work/"]
	path = .gitconfig_work
[includeIf "gitdir:~/repos/oss/"]
	path = .gitconfig_oss
```

続いてインクルードされるファイル `~/.gitconfig_work` と `~/.gitconfig_oss` を作ります。

`~/.gitconfig_work`
```config
[user]
	name = Formal account name used at work
	email = my-work@example.com
```

`~/.gitconfig_oss`
```config
[user]
	name = Cool account name used at OSS activities
	email = my-oss@example.org
```


## 自動切り替えの確認
それぞれのリポジトリ用ディレクトリ `~/repos/work/` と `~/repos/oss/` の下にリポジトリを配置して設定を確認します。どの設定ファイルが使われたかわかるように `git config --show-origin --get user.name
` を実行します。

`~/repos/work`
```console
~/repos/work/wondrous-product$ git config --show-origin --get user.name
file:/home/username/.gitconfig_work     Formal account name used at work
```

`~/repos/oss`
```console
~/repos/oss/astounding-software$ git config --show-origin --get user.name
file:/home/username/.gitconfig_oss     Cool account name used at OSS activities
```

設定が切り替わっていること、また参照しているファイルも異なることが確認できます。
今回はユーザー名とメールアドレスを切り替えましたが、他の設定項目も同様に切り替えることができます。


## 注意点
- ローカルの設定が優先される
  設定ファイルの優先度は、そのまま適用されるので `global` と `local` では `local` が優先されます。今回の設定は `global` にしているので、`local` の設定がある場合はそちらが優先されます。
  ```console
  ~/repos/oss/local-config-service$ git config --show-origin --get user.name
  file:.git/config        Hot account name configured in local
  ```

- シンボリックリンクの場合は両方設定しておく
  現在のディレクトリのパスが判定されているので、シンボリックのパスと、オリジナルのパスで有効にするには両方設定しておきます。

  私の環境は Windows 10 64bit + WSL Ubuntu 18.04.1 LTS で、Visual Studio Code のターミナルは WSL です。
  しかしながら Windows の Eclipse でも作業することがあり、リポジトリの実態は Windows 側にあり `/mnt/c/develop/repos/` を `~/repos` にシンボリックリンクして使っています。(特殊なケースではありますが。。。)

  Visual Studio Code でターミナルを開くとオリジナルパスの `/mnt/...` が使われますが、自分でコマンドを打っているときは `cd ~/repos/...` のようにシンボリックリンクから移動することが多いです。このような場合にマッチするパスが変わるので両方指定しておかないと切り替えが機能しません。

  余談ですが、Eclipse は 2019年6月現在 [543171 – Support for includeif in git config](https://bugs.eclipse.org/bugs/show_bug.cgi?id=543171) が上がっており includeif に対応していません。

- Windows の場合は、パスの大文字小文字を区別したくないので `gitdir/i` を使う
  これはドライブレターの `C:` と `c:` のレベルですでに大文字小文字の違いが出てしまうので区別しないようにします。またパスの区切りは Windows の `\` ではなく、Linuxt などで使われる `/` です。
  ```config
  [user]
    useConfigOnly = true

  [includeIf "gitdir/i:C:/develop/repos/work/"]
      path = .gitconfig_work
  [includeIf "gitdir/i:C:/develop/repos/oss/"]
      path = .gitconfig_oss
  ```



----

これで作業環境ごとに `git config --local` で設定を追加する必要がなくなりました。
とくにアカウント名やメールアドレスは、複数の作業グループがある時に間違えコミットしないように注意が必要です。そのために `useConfigOnly = true` を設定しますが、そうするとリポジトリごとに設定が必要となります。その際に、今回の設定をしておくと特定のディレクトリ以下に配置しておくだけで設定が反映されるので便利です。

最近は「[OSS-Friday 活動 - 2019年5月まとめ](https://riotz.works/articles/2019/06/05/summary-of-oss-friday-activities-in-2019-05/)」に書きましたように、わずかながら OSS への貢献活動も始めたのでリポジトリのクローンをすることが増え、今回のようにディレクトリで自動に切り替わってくれるのは助かります。
