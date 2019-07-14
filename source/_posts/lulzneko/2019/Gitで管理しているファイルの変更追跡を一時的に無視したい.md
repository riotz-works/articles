---
title: Git で管理しているファイルの変更追跡を一時的に無視したい
permalink: temporarily-ignore-change-tracking-for-files-managed-by-git
date: 2019-07-14
author: lulzneko
categories: 開発環境
tags:
- Git
---

Git を使って開発しているプロジェクトで、トラブル対応や機能のテストのために一時的に変更を加えた状態で作業をしたい、ただし一時的な変更なのでコミットしたくないといったことがあります。このような場合に skip-worktree を使うと、変更の追跡を一時的に無視できます。

![](/articles/assets/lulzneko/develop/develop.jpg)


特定のオプションを有効にしている場合に発生する問題への対応のために設定ファイルを変更している、しかしオプションはコミットしない。そうそうあることでもないですが、このようなパターンで一部変更しながらもコミットしたくないというケースがあります。そして慎重に作業を進めつつも、うっかりコミットして面倒なことに。。。

最初からコミットするつもりがない変更については明示的に外してしまうのも手です。

**環境**
本記事の開発環境は以下となります。
- Windows 10 64bit + WSL Ubuntu 18.04.1 LTS
- Git 2.17.1 (WSL Ubuntu 18.04.1 LTS)

**参考情報**
- [Git - git-update-index Documentation](https://git-scm.com/docs/git-update-index)
- [SKIP-WORKTREE BIT: Git - git-update-index Documentation](https://git-scm.com/docs/git-update-index#_skip_worktree_bit)


## git update-index --skip-worktree で無視
指定するパスを Git の変更追跡から無視する設定が `git update-index --skip-worktree <path>` です。

このオプションを指定すると変更追跡から無視されるので、ファイルを更新しても Git の変更に乗りません。たとえば変更したファイル `config.json` があったとします。Git で状態を見ると以下のようになります。
```console
$ git status
ブランチ master
Your branch is up to date with 'origin/master'.

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   config.json

no changes added to commit (use "git add" and/or "git commit -a")
```

そこで変更追跡から無視して再度確認してみます。変更対象に入っていないことがわかります。
```console
$ git update-index --skip-worktree config.json
$ git status
ブランチ master
Your branch is up to date with 'origin/master'.

nothing to commit, working tree clean
```

また `skip-worktree` 設定しているファイルは以下のコマンドから確認できます。
```console
$ git ls-files -v | grep ^S
S config.json
```

これにより誤りコミットを避けることができます。


## 変更管理対象に戻す
戻すためのオプションは `--no-skip-worktree` です。最初に `no` が入っています。
```console
$ git update-index --no-skip-worktree config.json
$ git status
ブランチ master
Your branch is up to date with 'origin/master'.

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   config.json

no changes added to commit (use "git add" and/or "git commit -a")
```


## リモートで更新された場合
変更追跡から無視している状態で、そのファイルがリモートで更新されたらどうなるでしょうか。

もし、まだ変更していない場合(無視する設定だけした)は、リモートの更新をマージできます。これは通常の Git 管理と変わらず特別なことは何もありません。

変更してた場合、残念ながらマージできません。いったん `--no-skip-worktree` して、変更を stash するか、何らかの対応をします。



----

ちょっと変更をキープしながら、作業をしなければならない時などに便利です。

ただしリモートで変更されてしまうと結構手間なので、長期にわたって使うものではないかもしれません。なにしろローカルの変更があるのでマージできないというエラーが出るのに `git status` は `nothing to commit, working tree clean` って、無視設定したことを忘れてると混乱しますから。
