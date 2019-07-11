---
title: AWS WAF を使用して IP ホワイトリストによるアクセス制限を行う方法
permalink: configuring-ip-address-access-control-with-aws-waf
date: 2019-07-12
author: lopburny
categories: 開発環境
tags:
- AWS
---

![](/articles/assets/lopburny/img/lopburny_blog_2_thumbnail.jpg)

[前回の記事](/articles/lopburny/2019/07/05/hosting-static-web-page-on-aws-with-s3-and-cloudfront/)では AWS 上に静的サイトをホスティングする構成について紹介しました。この記事では、CloudFront のディストリビューションに AWS WAF で作成した Web ACL を関連付け、IP ホワイトリストによるアクセス制限を行う方法についてお伝えしたいと思います。

例えば以下のようなコンテンツで、サイトを外部に公開したくない場合において有効です。
<br />
- WebAPIの仕様、開発ドキュメント ※ Swagger UI など
- 社内向けサイト
- 開発中のウェブアプリ

## この記事で紹介する構成

![](/articles/assets/lopburny/img/lopburny_blog_2_diagram_1.png)
<br />

- CloudFront のディストリビューションに AWS WAF を使用してアクセス制限をかけます。
- クライアントによる S3 バケットへの直アクセスはできません。
- AWS WAF は Origin の種類に関係なく関連付けることができます。

## 設定の手順

以下の一連の作業を行う必要があります。  
まず、AWS WAF のコンソールを開き、「Go to AWS WAF」 を選択します。

![](/articles/assets/lopburny/img/lopburny_blog_2_pic_1.jpg)

Create web ACL を選択すると以下の画面が表示されます。

![](/articles/assets/lopburny/img/lopburny_blog_2_pic_2.jpg)

- Web ACL name を入力します。
- Region については、「Global（CloudFront）」を指定します。
- AWS resources to associate で関連付ける AWS リソースを指定することができます。ここですでに作成済みの CloudFront ディストリビューションを指定しても OK ですが、この記事では別途 CloudFront のコンソールから指定することにします。

「Next」をクリックして次の設定に移ります。

![](/articles/assets/lopburny/img/lopburny_blog_2_pic_3.jpg)

ここでは「IP match conditions」を選択します。

![](/articles/assets/lopburny/img/lopburny_blog_2_pic_4.jpg)

ここで許可したい IP アドレスを登録します。名前・IP Version・Address を入力して、「Add IP address or range」をクリックします。複数のIPアドレスを登録することもできますので、許可したい IP アドレスがたくさんある場合はどんどん入れていきましょう。  

登録が終わったら、画面下の「Next」をクリックします。

![](/articles/assets/lopburny/img/lopburny_blog_2_pic_5.jpg)

ルールの名前、Rule type、条件（Condition）を指定します。  

先程作成した IP アドレスのリストが反映されていることを確認し、「Create」をクリックします。

![](/articles/assets/lopburny/img/lopburny_blog_2_pic_6.jpg)


作成したルールの Action は「Allow」を選択、Default action は「Block all requests that don't match any rules」を選択します。これでこのルールにマッチしなかった IP アドレスはブロックする挙動になります。  

準備ができたら「Review and create」をクリック、問題なければ「Confirm and create」します！  

先程 AWS resources to associate を設定しておいた場合、このまましばらくすると反映されるようになります。AWS WAF のコンソールではなく CloudFront のコンソールで設定する場合は、ディストリビューションの設定にて「General」タブを選択、以下の場所から設定することができます。

![](/articles/assets/lopburny/img/lopburny_blog_2_pic_7.jpg)

修正してしばらくすると適用されます。WAFによってアクセスがブロックされた場合、以下のようなエラー画面が表示されます。

![](/articles/assets/lopburny/img/lopburny_blog_2_pic_8.png)

Web ACL のコンソールを見ると、どのようなリクエストがあったか確認することができます。便利ですね！  

![](/articles/assets/lopburny/img/lopburny_blog_2_pic_9.jpg)

アクセスログを取得したい場合は Web ACL のコンソールから Logging タブを選択して有効化することもできますが、この記事では割愛させていただきます。  

AWS WAF で作成する項目として 許可する IP アドレスのリスト・ルール・Web ACL の３点を押さえておくと分かりやすいと思います。Web ACL を削除する時、この順番で消していかないとエラーになりますのでご参考ください。  

いかがだったでしょうか。  

AWS WAF は、ALB（アプリケーションロードバランサー）と API Gateway にも関連付けることができます。ただし、その場合は Web ACL のリージョンを指定する必要があります。  

IP アドレス制限のみならず、色んなルールを設定することができますので、是非試してみてください。
