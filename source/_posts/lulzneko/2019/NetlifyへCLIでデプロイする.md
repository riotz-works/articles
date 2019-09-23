---
title: Netlify へ CLI デプロイで、CI/CD する
permalink: deploy-to-netlify-via-cli-for-cicd
date: 2019-09-23
author: lulzneko
categories: 開発環境
tags:
- Netlify
- CircleCI
- Node.js
---

静的なサイトを手軽にホスティングしてくれる Netlify。独自のデプロイの仕組みに加えで CLI からもデプロイできます。CLI からデプロイすることで CI を使って継続的結合・デプロイが行えるようになります。Netlify の CLI デプロイについて解説します。

![](/articles/assets/lulzneko/develop/cicd.jpg)


Netlify は、独自のデプロイの仕組みを持っているのでウェブコンソールからの設定や netlify.toml を用意することで、連携している GitHub などのソース変更から自動的にデプロイしてくれます。通常は自動デプロイがよいのですが、初期コードの投入などで Git にプッシュせず CLI から直接デプロイしたいこともあります。また CircleCI などの CI/CD サービスを使っている場合にも、CLI からのデプロイは必要となります。

**環境**
本記事の開発環境は以下となります。
- Windows 10 64bit + WSL Ubuntu 18.04.1 LTS
- Node.js 12.10.0
- netlify-cli 2.15.0


## Netlify CLI 2.0
Node.js ベースで新しく作られた [Netlify 公式の CLI](https://www.netlify.com/docs/cli/#installation) です。[Go ベースの CLI](https://github.com/netlify/netlifyctl/blob/master/README.md) もあり、現在もサポートされていますが、Node.js ベースがアクティブとのことです。


## マニュアル・デプロイ
デプロイするプロジェクトに netlify-cli をインストールし実行します。
※ 公式ドキュメントではグローバルにインストールしていますが、グローバル汚染避けたいので個別にインストールしています。また `npx` するには重いのでプロジェクトごとに入れるのが良いでしょう。

デプロイするディレクトリを指定する場合は `-d [DIR]` で指定します。下記例では `dist` ディレクトリをデプロイします。
```console
$ yarn install netlify-cli

$ yarn netlify deploy -d dist
Logging into your Netlify account...
Opening https://app.netlify.com/authorize?response_type=ticket&ticket=4c8c9c402pe530d68dkd61d09adbXXXX
```

デプロイを実行するとブラウザが表示され、CLI アクセスの認可を求められます。CLI で、デプロイを認める場合は [Authorize] をクリックします。
![](/articles/assets/lulzneko/develop/netlify/01-001.png)

認可すると処理が続き、Netlify 上のサイトとの関連付けを聞かれます。
今回は、すでに Netlify にサイトが作られている前提で `Link this directory to an existing site` を選択しました。
```console
? What would you like to do? (Use arrow keys)
❯ Link this directory to an existing site
  +  Create & configure a new site
```

既存のサイトを指定する方法を聞かれます。今回は `Choose from a list of your recently updated sites` でリストから選択しました。
```
? How do you want to link this folder to a site? (Use arrow keys)
  Use current git remote origin
  Search by full or partial site name
❯ Choose from a list of your recently updated sites
  Enter a site ID
```

サイトを選択とするとデプロイが開始するのですが、たまに失敗するケースがあります。
法則性が読めないので何とも言えないのですが、失敗したらデプロイのコマンドを再実行すると成功します。再実行時は、先ほどの設定が保存されているので認可やサイト選択は省略されます。

デプロイが成功すると `Live Draft URL` が出力されます。
その URL にアクセスするとデプロイされたサイトが表示されます。

なおプロダクションとしてデプロイしたい場合は、コマンドライン引数に `--prod` を渡します。
e.g. `yarn netlify deploy --prod -d dist`

ここまでで設定した内容は、以下に保存されています。
※ 不要になった場合は安全のために削除しておきましょう。
- 認可情報: `~/.netlify/config.json`
- サイト情報: `.netlify/state.json` (プロジェクトのディレクトリ))


## 自動デプロイのための設定
マニュアル・デプロイの方法はブラウザで認証認可が必要となるため CI/CD では利用できません。
CI/CD で使う場合は、自動デプロイできるように設定します。

Netlify の Personal access tokens のページを表示し、[New access token] をクリックします。
https://app.netlify.com/user/applications#personal-access-tokens
![](/articles/assets/lulzneko/develop/netlify/01-002.png)

Create a new personal access token 画面が表示されるので、[Description of your token] にトークンの説明となる文字列を入力し [Generate token] をクリックします。今回はテスト用に [deploy-via-cli] としました。
![](/articles/assets/lulzneko/develop/netlify/01-003.png)

アクセストークンが生成されるので控えておきます。
![](/articles/assets/lulzneko/develop/netlify/01-004.png)

続いて、デプロイするサイトの設定を表示し、[API ID] を控えておきます。
![](/articles/assets/lulzneko/develop/netlify/01-005.png)

デプロイするプロジェクトに netlify-cli をインストールしておき、以下のコマンドでデプロイします。
- `--prod` で、プロダクションのデプロイ
- `-d [DIR]` で、デプロイするディレクトリを指定
- `-a [AUTH_TOKEN]` で、パーソナルトークンを指定
- `-s [SITE_ID]` で、デプロイするサイトの APP ID を指定
```console
$ yarn netlify deploy --prod -d dist -a [AUTH_TOKEN] -s [SITE_ID]
```

この場合だと　`package.json` なりに [AUTH_TOKEN] を書かなければなりません。環境変数を使う形にします。
CI/CD サービスで環境変数を設定できるので、そちらに設定します。
※ 環境変数名を ${NETLIFY_AUTH_TOKEN} と ${NETLIFY_SITE_ID} にした場合は、それぞれ `-a` と `-s` のオプション指定は省略できます
```console
$ yarn netlify deploy --prod -d dist -a ${NETLIFY_AUTH_TOKEN} -s ${NETLIFY_SITE_ID}
```



----

手軽にホスティングできながらも、本格的な CI/CD にも対応できる Netlify、素晴らしいですね。

ただし、個人に紐づくアクセストークンを使うので CI などに設定する場合は、その人がプロジェクトから離れた場合の引継ぎなどを忘れないようにしましょう。GitHub などはリポジトリでトークンが作れるので、今後そのようになると嬉しいです。
