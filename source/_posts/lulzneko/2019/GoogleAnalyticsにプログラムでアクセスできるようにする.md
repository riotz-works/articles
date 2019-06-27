---
title: Google Analytics にプログラムでアクセスできるようにする
permalink: programmatically-access-google-analytics
alias: /2019/04/17/programmatically-access-google-analytics/index.html
date: 2019-04-17
author: lulzneko
categories: 開発
tags:
- Hexo
---

ウェブサイトのアクセス解析のサービス、Google Analytics。
この Google Analytics はブラウザでアクセスして解析データを見るほかに Web API 経由でデータを取得できます。今回は Google Analytics の Web API へプログラムからアクセスできるようにします。

![](/articles/assets/lulzneko/develop/develop.jpg)


Google Analytics へプログラムを使ってアクセスすることで、分析サイトを作ったり、必要なデータだけを定期的に取得することなどができます。たとえば週間 PV をチャットに流すといったような使い方が考えられます。
本記事では [sfarthin/ga-analytics](https://github.com/sfarthin/ga-analytics) の CLI でデータを取得するところまでとなりますが、次回ブログ環境で使っている Hexo と組み合わせた話を紹介します。

**環境**
本記事の開発環境は以下となります。
- Windows 10 64bit + WSL Ubuntu 18.04.1 LTS
- OpenSSL 1.1.0g (WSL)
- Visual Studio Code
- Node.js 8.10.0
- npm 5.6.0
- sfarthin/ga-analytics 0.0.7


## Google Developer Console で Analytics API の準備
まず Google Analytics の Web API を使えるように Google Developer Console へアクセスしてプロジェクトを作成し、認証情報を設定します。

https://console.developers.google.com/ へアクセスし、利用する Google Account でログインします。
はじめて作成する場合は空のダッシュボードから [作成] をクリックしてプロジェクトとを作ります。
また、すでに使っている場合はヘッダーの [プロジェクトの選択] - [新しいプロジェクト] から作ります。
![](/articles/assets/lulzneko/develop/google/analytics/01.png)

新しいプロジェクト作成ページが表示されるので情報を入力し [作成] をクリックします。
今回は以下としました。実際に使うときには [プロジェクト ID] も設定しておくとよいでしょう。
- プロジェクト名: `analytics-access`
- 場所: `組織無し`（変更なし）
![](/articles/assets/lulzneko/develop/google/analytics/02.png)

プロジェクトが作成され、プロジェクトのダッシュボードが表示されます。(ヘッダーにプロジェクト名が出てる)
Google Analytics へアクセスるための機能を追加するために [＋ API とサービスを有効化] をクリックします。
![](/articles/assets/lulzneko/develop/google/analytics/03.png)

検索ボックスに [Analytics API] を入力して絞り込み [Analytics API] のボックスをクリックします。
※ 似ている名前でアイコンがついている [Google Analytics Reporting API] がありますが違うので注意
![](/articles/assets/lulzneko/develop/google/analytics/04.png)

Analytics API の説明が表示されるので利用規約 [Google APIs Terms of Service](https://console.developers.google.com/tos?id=universal) を確認し同意できたら [有効にする] をクリックします。
![](/articles/assets/lulzneko/develop/google/analytics/05.png)


## Analytics API の認証情報を作成
Analytics API のダッシュボードが表示され、認証情報の作成を求められます。
[認証情報を作成] をクリックします。
![](/articles/assets/lulzneko/develop/google/analytics/06.png)

プロジェクトへの認証情報の追加画面が表示されるので、項目を入力し [必要な認証情報] をクリックします。
- 使用する API: `Analytics API`
- API を呼び出す場所: `その他の UI (Windows、CLI ツールなど)`
- アクセスするデータの種類: `アプリケーション データ`
![](/articles/assets/lulzneko/develop/google/analytics/07.png)

サービスアカウント作成のフォームが表示されるので、項目を入力し [次へ] をクリックします。
- サービス アカウント名: 任意の文字列 (今回は `hexo`)
- サービス アカウント ID: 任意の文字列 (今回は自動で `hexo-907`、６文字以上必要 )
- 役割: `役割を選択`（変更なし）
- キーのタイプ: `P12`
![](/articles/assets/lulzneko/develop/google/analytics/08.png)

「サービス アカウントに役割が割り当てられていません」と 表示されますが、[役割なしで作成] をクリックします。
※ 役割はなくても Web API にアクセスできますし、コンソールにアクセスする必要はないので役割なしで問題ありません。
![](/articles/assets/lulzneko/develop/google/analytics/09.png)

サービス アカウントとキーが作成され、秘密鍵のファイルがダウンロードされます。ダウンロードした秘密鍵にはパスワードが設定されています。表示された文字列をひかえておきます。(キャプチャ用に画像はそれっぽく作っています)
ダウンロードが完了したら [閉じる] をクリックします。
![](/articles/assets/lulzneko/develop/google/analytics/10.png)

認証情報画面が表示されるので [サービス アカウントの管理] をクリックします。
![](/articles/assets/lulzneko/develop/google/analytics/11.png)

サービスアカウントの情報が表示されるので [メール] と [名前] をひかえておきます。
![](/articles/assets/lulzneko/develop/google/analytics/12.png)


## Google Analytics の認証情報を作成
https://analytics.google.com/analytics/ へアクセス Google Analytics を表示します。
左のメニューから [管理] をクリックし、アクセスしたい [アカウント] - [プロパティ] - [ビュー] を設定します。

アクセスする [ビュー] の [ビューの設定] をクリックします。
![](/articles/assets/lulzneko/develop/google/analytics/13.png)

[ビュー ID] をひかえ、[ユーザー管理者] をクリックします。
![](/articles/assets/lulzneko/develop/google/analytics/14.png)

ビューの権限画面から右上の [＋] をクリックし、[ユーザーを追加] を選択します。
![](/articles/assets/lulzneko/develop/google/analytics/15.png)

権限の追加画面に必要な情報を入力し [追加] をクリックします。
- メールアドレス: ひかえておいたメールの文字列 (今回は `hexo-907@analytics-access-237909.iam.gserviceaccount.com`)
- 新規ユーザーにメールで通知する: チェックを外す (チェックしてても問題はありません)
- 権限: 表示と分析だけチェック（変更なし）
![](/articles/assets/lulzneko/develop/google/analytics/16.png)

追加されました。
![](/articles/assets/lulzneko/develop/google/analytics/17.png)


## sfarthin/ga-analytics でデータを取得
実際にコマンドからデータを取得してみます。ここでは簡単にデータを取得してくれるライブラリ [sfarthin/ga-analytics](https://github.com/sfarthin/ga-analytics) を使います。 sfarthin/ga-analytics は Analytics API を使って、Google Analytics のデータを取得してくれる Node.js のモジュールです。詳しくは [README](https://github.com/sfarthin/ga-analytics#readme) をご参照ください。

まずはダウンロードした秘密鍵のファイルフォーマットを pem形式に変換します。
```console
$ openssl pkcs12 -in [ダウンロードした秘密鍵のファイル] -nocerts -passin pass:[秘密鍵ファイルのパスワード] -nodes -out google-services.pem
```

Analytics API にアクセスするための情報を環境変数に設定し、`ga-analytics` を実行します。
引数がない場合はトータルのセッション数が出力されます。セッション数が表示されたらサービス設定等正しく行えています。プログラムでアクセスできる準備ができました！
```console
export GOOGLEAPI_CLIENTID="[サービスアカウントの名前].apps.googleusercontent.com"
export GOOGLEAPI_EMAIL="[サービスアカウントのメール]"
export GOOGLEAPI_KEY="google-services.pem"
export GOOGLEAPI_ANALYTICS_TABLE="ga:[Google Analytics の ビュー ID]"

npx ga-analytics
┌────────────────────┐
│ sessions (INTEGER) │
├────────────────────┤
│ 826                │
└────────────────────┘

Total sessions: 826
```


## 不要となった場合
以下の２ヵ所から削除します。
- Google Developer Console https://console.developers.google.com/ の サービス アカウント
- Google Analytics https://analytics.google.com/analytics/ のビュー ユーザー管理者



----

Google Analytics からプログラムでデータを取得することができました。
しっかりとした分析を確認したりするには Google Analytics のウェブサイトが素晴らしいので、同じようなダッシュボードはあまり作りませんが、ちょっとした分析や定型レポートの出力などに使えます。

次回は、この Google Analytics と Hexo を組み合わせた Plugin の紹介をします。
