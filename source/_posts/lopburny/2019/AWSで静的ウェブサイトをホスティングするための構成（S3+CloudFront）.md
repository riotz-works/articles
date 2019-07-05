---
title: AWS で静的ウェブサイトをホスティングするための構成（S3 + CloudFront）
permalink: hosting-static-web-page-on-aws-with-s3-and-cloudfront
alias: /2019/07/05/hosting-static-web-page-on-aws-with-s3-and-cloudfront/index.html
date: 2019-07-05
author: lopburny
categories: サーバーレス
tags:
- AWS
---

![](/articles/assets/lopburny/img/lopburny_blog_pic_1.jpg)

ここ最近、SPA（シングルページアプリケーション）や SSG（静的サイトジェネレーター）を利用してウェブサイトやアプリケーションを構築するケースが増えており、静的サイトを配信する仕組みについても興味を持つようになりました。便利なサービスとしては Netlify や Firebase Hosting, GitHub Pages など色々ありますが、ウェブサービスやシステム開発を行う上で、AWS を使っているならば AWS で完結したいということもあるかと思います。

この場合、Web サーバーを立てて実現することもできますが、AWS が提供するサービスをうまく活用することで、より簡単に実現でき、費用を抑え、運用の手間を減らすことができます。定番のやり方としては、例えば以下の AWS サービスが挙げられます。
<br />
- AWS Amplify コンソール
- S3 の静的ウェブサイトホスティング（Static website hosting）

この記事では、一見クラシックなやり方ではありますが、CloudFront を利用することで高速かつ柔軟性を持つ配信方法の一例を紹介します。AWS WAF や Lambda@Edge と組み合わせることで、単体のウェブアプリケーションだけでなく、社内利用を含め色んなシーンに対応できることが利点になります。

S3（ストレージ） や CloudFront（CDNサービス） といった AWS のサービスについての基本的な前提知識が必要となります。

## この記事で紹介する構成

![](/articles/assets/lopburny/img/lopburny_blog_diagram_1.png)

ポイントとしては、
<br />
- Origin Access Identity を使用することで、外部から直接 S3 へアクセスすることはできない
- CloudFront の各種機能をフル活用できる
- アクセス制御や流量制限など、WAF や Lambda@Edge を利用して柔軟に対応できる
- Origin を変更する場合でも、クライアントに提供されているエンドポイントを変更することなく対応できる  
※ Origin の S3 をクロスアカウントにすることも可能  

といった点です。


## 構築方法

上記の構成で静的サイトをホスティングするためには、大きく以下の２ステップに分けられます。
<br />
- Origin となる S3 バケットを作成する
- CloudFront ディストリビューションを作成する

### Origin となる S3 バケットを作成する
どのリージョンでも問題ないので、普段良く使っているリージョンを指定してバケットを作成します。  
バケット名をサイトのFQDNにする必要はありません。

![](/articles/assets/lopburny/img/lopburny_blog_pic_2.png)

設定はデフォルトで問題ないかと思います。  
アクセス許可については、パブリックアクセスができないことを確認します。

### CloudFront ディストリビューションを作成する

![](/articles/assets/lopburny/img/lopburny_blog_pic_3.png)

Web ディストリビューションを作成します。

![](/articles/assets/lopburny/img/lopburny_blog_pic_4.png)


#### Origin Settings
<br />

- Origin Domain Name をクリックして、先程作成したS3バケットを選択
- Restrict Bucket Access: Yes を選択
- Origin Access Identity: Create a New Identity を選択
- Grant Read Permissions on Bucket: Yes, Update Bucket Policy を選択

これで Origin Access Identity が作成され、この Identity からのアクセスのみ許可するように S3 のバケットポリシーが更新されます。構築後、CloudFront コンソール左側メニューの Security > Origin access identity を開くと名前と ID が確認できます。

#### Default Cache Behavior Settings
<br />

- Viewer protocol policy: Redirect HTTP to HTTPS にするとリダイレクトしてくれます。
- Object Caching については、とりあえず 全て 0 を設定します。
  - Minimum TTL: 0
  - Maximum TTL: 0
  - Default TTL: 0
- Compress Objects Automatically: Yes を選択することで、リクエストヘッダーに Accept-Encoding: gzip が含まれている場合、圧縮してくれます。

#### Distribution Settings
<br />

- AWS WAF Web ACL: WAFのACLをここで適用させることができます。
- Default Root Object を設定しないと、ルートのURLパスで index.html を見てくれません。  

ホスティングするサイトのトップページを表示するためには、Default Root Object に index.html を指定する必要があります。  
ここは色々ハマりポイントがあるので、後日、別途紹介したいと思います。  

#### ディストリビューションの作成
その他、独自ドメインや証明書、ログの設定などは一旦置いといて、  
とりあえず Create Distribution ボタンを押します。そうすると、下記のようにディストリビューションが作成されます。  
初期作成は 20 分 - 40 分ほどかかります。コーヒータイムですね。  

![](/articles/assets/lopburny/img/lopburny_blog_pic_5.png)

## サイトをリリース（更新）する

キャッシュを設定していなければ、Origin の S3 バケットのファイルを更新するだけですぐに反映されます。  
例えば、AWS CLI を利用して CI やデプロイスクリプトに下記のようなコマンドを利用することで簡単にリリースできます。  
<br />
```
aws s3 sync {リリース対象のディレクトリ} s3://{バケット名} --region {リージョン名}
```

特に問題なければ、下記のように表示されます。

![](/articles/assets/lopburny/img/lopburny_blog_pic_7.png)

また、S3 に入っていないパスやファイルを指定すると、下記のようなエラーページ(403) が表示されます。  
カスタムエラーページを指定することで、この画面を見せないようにすることができます。

![](/articles/assets/lopburny/img/lopburny_blog_pic_6.png)

## まとめ
<br />

- 一見クラシックなやり方ですが、S3 + CloudFront の組み合わせは高速かつ柔軟性が高くおすすめです。
- 単体のウェブサイトで、ベーシック認証ができる程度でいいなら AWS Amplify コンソールがいいでしょう。
- AWSに限定しなくていい・もっと手間を省きたい場合、Netlify か Firebase か GitHub Pages 等、選択肢は多くあります。

いかがだったでしょうか。

引き続き、このパターンを用いた
<br />
- 独自ドメインとSSL/TLS証明書を利用する
- AWS WAF を使用して IP ホワイトリストによるアクセス制御を行う
- Lambda@Edge を使用してベーシック認証をかける
- カスタムエラーページを指定する

といった方法や、AWS Amplify についても紹介していきたいと思います。