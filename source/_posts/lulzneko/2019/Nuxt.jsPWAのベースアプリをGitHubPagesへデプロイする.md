---
title: Nuxt.js PWA のベースアプリを GitHub Pages へデプロイする
permalink: deploy-nuxtjs-pwa-base-app-to-github-pages
alias: /2019/05/13/deploy-nuxtjs-pwa-base-app-to-github-pages/index.html
date: 2019-05-13
author: lulzneko
categories: フロントエンド
tags:
- Nuxt.js
- PWA
- JAMStack
- TypeScript
- GitHub
- CircleCI
---

Nuxt.js を使うことで手軽に PWA なアプリを作ることができます。そして開発した PWA を世に出すためには、サーバー環境へデプロイする必要があります。Nuxt.js を SPA の JAMStack な作りにしている場合は、ウェブサイトをホスティングできる環境であれば、どこでも大丈夫です。今回は GitHub Pages でホスティングをします。

![](/articles/assets/lulzneko/develop/nuxtjs/nuxtjs.png)


前回までの実装で [Nuxt.js](https://ja.nuxtjs.org/) の [PWA](https://developer.mozilla.org/ja/docs/Web/Progressive_web_apps) with [TypeScript](https://www.typescriptlang.org/) ができました。アプリとしての機能はまったくないですが、やはりデプロイして実際にスマートフォンからアクセスしてみたいところです。今回は簡単にデプロイできる GitHub Pages でホスティングします。また、一手間かかりますが CircleCI とも連携して、アプリのソース管理とホスティングを同じリポジトリで行い、ソースコードを変更したら自動的にビルド＆デプロイする方法も紹介します。

**シリーズの記事**
- [Nuxt.js で PWA(Progressive Web Apps) のベースアプリを作る](https://riotz.works/articles/2019/05/09/develop-base-app-for-pwa-with-nuxtjs/)
- [Nuxt.js PWA(Progressive Web Apps) のベースアプリをTypeScript対応する](https://riotz.works/articles/2019/05/10/typescripting-base-app-of-nuxtjs-pwa/)
- **Nuxt.js PWA のベースアプリを GitHub Pages へデプロイする** (本記事)

**環境**
本記事の開発環境は以下となります。
- Windows 10 64bit + WSL Ubuntu 18.04.1 LTS
- Visual Studio Code
- Node.js 12.2.0
- Yarn 1.15.2
- Nuxt.js 2.6.3
- 開始時のアプリソース https://github.com/riotz-works/samples-pwa-base-app/tree/0.0.2


## GitHub Pages へデプロイ
[GitHub Pages](https://pages.github.com/) は、ソースコード管理 [Git](https://git-scm.com/) のホスティングサービス [GitHub](https://github.com/) が提供する、ウェブサイト・ホスティングの機能です。
ユーザーや組織のウェブサイト、またはアプリなどのプロジェクト・ウェブサイトを公開できます。GitHub Pages はプログラムなどを実行できませんが、静的なサイトを手軽に公開できます。

まず GitHub にホスティング用のリポジトリを作成します。
[リポジトリ名] は、任意の名前です。 (今回は `pwa-base-app` としました)
※ プロジェクトサイトを想定しています。ユーザーまたは組織サイト(リポジトリ名が `username.github.io` のパターン)の場合は設定が異なるので各項目で注釈を入れます。ご注意ください。

リポジトリをクローンしてローカルの開発ディレクトリに、アプリのソースをコピーしておきます。
![](/articles/assets/lulzneko/develop/nuxtjs/20.png)

GitHub リポジトリの [Settings] から GitHub Pages を有効にします。
- [develop] は `master branch` を選択します
![](/articles/assets/lulzneko/develop/nuxtjs/21.png)

`nuxt.config.ts` に以下の設定を追加します。(必要箇所のみ抜粋)
- `build: publicPath` は、CSS や JS などが置かれるディレクトリでデフォルトの `_nuxt` の `_` 始まりが GitHub Pages で使えないため変更します。(`_` で始まらなければ任意の文字列で大丈夫です)
- `router: base` は、GitHub Pages のプロジェクトサイトがサブパスのリポジトリ名に配置されるので `[リポジトリ名]` を設定します。
※ ユーザーまたは組織サイトの場合はドメイン直下に配置されるため `router: base` の設定は不要です
```typescript
const config: NuxtConfiguration = {
  // ...(省略)

  build: {
    publicPath: '/static/',
    extend(config, ctx) {
    },
  },

  router: {
    base: '/pwa-base-app/'
  },
}
```

GitHub Pages デプロイ用のモジュールを導入します。
```console
$ yarn add -D gh-pages
```
※ npm の場合 `npm i -D gh-pages`

デプロイの実行！
以下のコマンドでビルドとデプロイをします。
```console
$ yarn build
$ yarn gh-pages -d dist -b master -m 'Update Site'
```

コンソールに `Published` が表示されたら GitHub Pages の設定で表示されていた URL にアクセスします。(ちょっとタイムラグがあるかもしれません。表示されなかったら少し待って再アクセスしてください。)
![](/articles/assets/lulzneko/develop/nuxtjs/22.png)

以上、簡単に GitHub Pages へデプロイでした。
毎回コマンドを打つ必要がありますが、GitHub Pages のリポジトリを用意するだけで簡単にホスティングできます。


## CircleCI と連携した自動デプロイ
GitHub Pages へデプロイするのはコマンド１つで簡単ですが、開発中にちょくちょく実行するのは手間です。ソースがプッシュされたら自動でビルドしてデプロイしておいて欲しいです。これは CircleCI と GitHub を連携させることで実現できます。

ちょっと手間がかかりますが、設定を１回するだけで後はリポジトリにプッシュすると自動的にデプロイしておいてくれるので便利です。なにより「常にアクセス可能なプロトタイプ環境」が実現できるのでありがたいです。


## CircleCI とは
[CircleCI](https://circleci.jp/) は Continuous Integration and Delivery と、サイトのタイトルにつけている通り CI(Continuous Integration)/CD(Continuous Delivery) の機能を提供するサービスです。
ソースコードの変更を受け、あらかじめ定義されたビルドやテスト、デプロイといったことを自動的に行ってくれます。無料で始められるので、CI/CD の入門や小規模なプロジェクトでも使いやすいですし、大規模なプロジェクトにもしかりと対応してくれます。


### GitHub リポジトリにデプロイキーを追加
GitHub に CircleCI からデプロイできるようにデプロイキーを追加します。
まず `ssh-keygen` で、パスフレーズなしの ECDSA キーペア(`~/deploy_key` と `~/deploy_key.pub`)を作ります。
※ すべての作業が終わったらローカルのキーペアファイルは削除します
```console
$ ssh-keygen -t ecdsa-sha2-nistp521 -C "" -f ~/deploy_key
```
※ ECDSA が使えない場合は `ssh-keygen -t rsa -b 4096 -C "" -f ~/deploy_key`

そして `~/deploy_key.pub` の内容を、GitHub リポジトリの [Settings] - [Deploy keys] に貼り付けます。
- [Title] は、あとでわかるようにします。(今回は CircleCI 用なので `CircleCI` としました)
- [Key] は、`~/deploy_key.pub` の内容です。
- [Allow write access]: チェックを付けます。これを忘れると CircleCI からプッシュできません。
![](/articles/assets/lulzneko/develop/nuxtjs/23.png)

追加されたキーの `Fingerprint` をひかえておきます。
![](/articles/assets/lulzneko/develop/nuxtjs/24.png)


### プロジェクトの npm スクリプトを設定
プロジェクト直下 `/package.json` に `deploy` スクリプトを追加します。`generate` の行末にカンマを忘れないようにします。(scripts のみ抜粋)
これにより先ほどのデプロイコマンドを `yarn deploy` だけで実行できるようになります。`[ci skip]` は CircleCI を動作させないためのコミットメッセージになります。`master` ブランチは GitHub Pages の公開用 HTML なので CI は使わないためです。
```javascript
  "scripts": {
    "dev": "nuxt",
    "build": "nuxt build",
    "start": "nuxt start",
    "generate": "nuxt generate",
    "deploy": "gh-pages -d dist -b master -m 'Update Site [ci skip]'"
  },
```

### CircleCI の設定ファイルを作成
プロジェクト直下に `.circleci` ディレクトリ、そのディレクトリに `config.yml` ファイルを追加します。
`/.circleci/config.yml` に以下の部分を環境に合わせて変更したものを書きます。
- 58行目 [fingerprints]: 上記 GitHub に追加したデプロイキーの `Fingerprint`
- 64行目 [user.name]: GitHub の `master` ブランチへプッシュするユーザーの名前 (コミットログ用、私は `CircleCI` を使うことが多いです)
- 65行目 [user.email]: GitHub の `master` ブランチへプッシュするユーザーメールアドレス (コミットログ用、自分または開発チームのメーリングリストなど)
- 21行目 [branches: only]: 後述の開発ブランチ名、今回は `develop` としました
```yaml
version: 2.1

executors:
  default:
    working_directory: ~/workspace
    docker:
      - image: circleci/node:12
    environment:
      TZ: /usr/share/zoneinfo/Asia/Tokyo

commands:
  system_info:
    steps:
      - run:
          name: System information
          command: |
            echo "Node $(node -v)"
            echo "Yarn v$(yarn -v)"
  setup:
    steps:
      - restore_cache:
          name: Restore Yarn Package Cache
          key: yarn-packages-{{ checksum "yarn.lock" }}
      - run:
          name: Setup
          command: yarn install
      - save_cache:
          name: Save Yarn Package Cache
          key: yarn-packages-{{ checksum "yarn.lock" }}
          paths:
            - ~/.cache/yarn

jobs:
  build:
    executor:
      name: default
    steps:
      - system_info
      - checkout
      - setup
      - run:
          name: Build
          command: yarn build
      - store_artifacts:
          path: dist
      - persist_to_workspace:
          root: ~/workspace
          paths:
            - ./*
  deploy:
    executor:
      name: default
    steps:
      - attach_workspace:
          at: ~/workspace
      - add_ssh_keys:
          fingerprints:
            - "[GitHub に追加したキーの Fingerprint]"
      - run:
          name: Deploy
          command: |
            mkdir -p ~/.ssh
            ssh-keyscan github.com >> ~/.ssh/known_hosts
            git config --global user.name "[your name]"
            git config --global user.email "[email@example.com]"
            yarn deploy

workflows:
  build-deploy-flow:
    jobs:
      - build:
          filters:
            branches:
              ignore:
                - master
      - deploy:
          requires:
            - build
          filters:
            branches:
              only:
                - [開発ブランチ名]
```

ちょっと長いですがざっくり以下です。(個々の詳細は、こちら [CircleCI を設定する - CircleCI](https://circleci.com/docs/ja/2.0/configuration-reference/#section=configuration) をご確認ください)
- `executors` で、実行環境を定義しています
- `commands` で、再利用可能な実行ステップ `system_info` と `setup` を定義しています
- `jobs` で、ワークフローから実行するジョブ `build` と `deploy` を定義しています
- `workflows` で、GitHub のブランチに応じて実行するジョブの定義をしています

`[開発ブランチ名]` にプッシュすると `build` ジョブに次いで `deploy` ジョブが実行されます。
`build` ジョブの中心は、`yarn install` と `yarn build` の実行です。
`deploy` ジョブの中心は、`yarn deploy` です。


### 空ブランチへプッシュ
プロジェクトのディレクトリで以下のコマンドを実行します。今回は [ブランチ名] を `develop` としました。ブランチ名は、先に作成した `/.circleci/config.yml` と合わせる必要があります。
```console
$ git checkout --orphan [ブランチ名]
$ git add .
$ git commit -m "initial commit"
$ git push -u origin [ブランチ名]
```

`git checkout --orphan` は、これまでのブランチとは関係のない空のブランチを作るオプションです。これにより `master` ブランチとはつながっていない `develop` の空ブランチが作られます。
ここに開発用のソースをコミットして GitHub へプッシュします。
GitHub でブランチが追加されていることが確認できます。
![](/articles/assets/lulzneko/develop/nuxtjs/25.png)

また [Insights] - [Network] では、`master` ブランチと途切れて `develop` ができていることが確認できます。(`master` ブランチは GitHub Pages 用で基本的にみることがないので [Settings] - [Branches] からデフォルトを `develop` にしておくと便利です)
![](/articles/assets/lulzneko/develop/nuxtjs/26.png)


### CircleCI にプロジェクトを追加
CircleCI https://circleci.com/dashboard へ GitHub のアカウントでログインします。
左のメニューから [➕ ADD PROJECTS] を選択します。
![](/articles/assets/lulzneko/develop/nuxtjs/27.png)

リポジトリが列挙されるので、今回のリポジトリ名の横 [Set Up Project] をクリックします。
設定と手順が書かれていますが、設定済みなので [Start building] をクリックします。
![](/articles/assets/lulzneko/develop/nuxtjs/28.png)

ワークフロー画面が表示され、しばらく待つとビルド失敗が表示されます。(デプロイキーを CircleCI に登録していないため)
![](/articles/assets/lulzneko/develop/nuxtjs/29.png)

ワークフロー画面の左メニュー、プロジェクト名横の [⚙] 歯車アイコンをクリックします。
左メニューから [SSH Permissions] を選び、画面右の [Add SSH Key] ボタンをクリックします。
SSH Key 追加ダイアログが表示されるので情報を入力し、[Add SSH Key] ボタンをクリックします。
- [Hostname]: `github.com`
- [Private Key]: `~/deploy_key` ファイルの内容
![](/articles/assets/lulzneko/develop/nuxtjs/30.png)

再ビルドするので、先ほどのワークフローのビルド失敗画面から [Rerun] - [Rerun from beginning] をクリックします。
(左メニューアイコン２段目の WORKFLOWS です)
![](/articles/assets/lulzneko/develop/nuxtjs/29.png)

以上で自動ビルド＆デプロイ、「CI/CD」の完成です！
`develop` ブランチへプッシュするたびに CircleCI が自動的にビルドし、GitHub Pages へデプロイしてくれます。


### ローカルのキーペアファイルのクリーンアップ
ビルドが通り、GitHub Pages でアプリが見れるようになったらローカルのキーペアファイル `~/deploy_key` と `~/deploy_key.pub` を削除します。
とくに秘密鍵側 `~/deploy_key` は、リポジトリに書き込みができてしまうので注意してください。


## ソースコード
今回作成した部分までのソースを GitHub へアップしました。
(プロジェクトのソース管理の都合上、リポジトリ名 `samples-pwa-base-app`、`source` ブランチになります。)
https://github.com/riotz-works/samples-pwa-base-app/tree/0.0.3

GitHub Pages にホスティングもしています。
(公開サイトは１つのため記事公開に合わせて変わり、本記事の内容とは異なります)
https://riotz.works/samples-pwa-base-app/



----

Nuxt.js アプリを、GitHub Pages へデプロイできるようになりました。
ホスティング先はいろいろ選べますが、小規模で手軽に扱いたい場合は GitHub Pages を使うと簡単です。

今回 CircleCI 連携をしました。今回のようなサンプルアプリでも CI/CD を使うことで簡単にホスティングできるようになるので便利ですし、本格的に使う場合はテストも CI で実行して早期にビルドエラーやテストの失敗を気づけるようにします。

私は CircleCI を好んで使っていますが、他にもさまざまなサービスがあるので環境にあったものを使うとよいでしょう。
