---
title: OpenAPI (Swagger) 3.0 で Bearer トークンの使用を定義する
permalink: describe-bearer-scheme-in-openapi-3
date: 2019-08-17
author: lopburny
categories: 開発
tags:
- OpenAPI
- Swagger
- WebAPI
---

Bearer トークンを使用して WebAPI 呼び出しをする場合、OpenAPI (Swagger) 3.0 ではどのように記述するのでしょうか。

![](/articles/assets/lopburny/img/lopburny_blog_7_thumbnail.jpg)

OpenAPI (Swagger) で WebAPI の仕様を記述する際、HTTP 認証・認可を行うための手段として Basic 認証・Bearer スキーム・API キー等の使用を定義することができます。この記事ではアクセストークン等による Bearer スキームの記述方法について紹介します。

※ OpenAPI (Swagger) については以下の記事で取り上げていますので、合わせてご確認ください。
<br>

<div class="iframely-embed"><div class="iframely-responsive" style="padding-bottom: 41.1101%; padding-top: 120px;"><a href="https://riotz.works/articles/lopburny/2019/07/21/efficient-way-to-manage-api-definition/index.html" data-iframely-url="//cdn.iframe.ly/EKy6Ncm"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>

## Bearer スキームとは
HTTP 認証スキームの一つで、WebAPI へアクセスするためのセキュリティトークン（アクセストークン等）を `Authorization: Bearer {トークン}` という形で HTTP ヘッダーにセットし、トークンの受け渡しを行う仕組みです。ここでは詳細を割愛させていただきますが、気になる方は以下をご参考ください。

<br>

<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://qiita.com/uasi/items/cfb60588daa18c2ec6f5" data-iframely-url="//cdn.iframe.ly/iXzaVCj"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>

## やりたいこと
WebAPI の仕様として Bearer トークンの使用を明記し、Swagger UI の動作に反映させることです。Swagger UI で見ると、以下のように Authorize ボタンが表示されます。

![](/articles/assets/lopburny/img/lopburny_blog_7_pic_1.jpg)

赤枠のボタンをクリックすると以下のダイアログが表示され、Bearer トークンを入力することができます。

![](/articles/assets/lopburny/img/lopburny_blog_7_pic_2.jpg)

トークンを入れて Authorize ボタンをクリックすると、Bearer スキームが指定されている API は Swagger UI 上から  Bearer トークン付きでリクエストが送信されます。

![](/articles/assets/lopburny/img/lopburny_blog_7_pic_3.jpg)

Bearer スキームの Authorization ヘッダーがついていますね。

※ `Bearer` という文字列も挿入されますので、こちらで入れる必要はありません。

## 記述方法

ここでは OpenAPI の記述に YAML 形式を使用します。
<br>

1. `components.securitySchemes` に Bearer スキームを追加します。
```yaml
components:  
  securitySchemes:
    Bearer:
      type: http
      scheme: bearer
      description: Credentials or access token for API
```

<br>
2. Bearer スキームを使用する API を指定します。
```yaml
paths:
  /users/me:
    get:
      # ...
      security:
      - Bearer: []
```

※ ルートレベルの `security` を定義すると、すべての API に適用されます。
```yaml
security:
- Bearer: []
```

記述する場所を分かりやすくするため、簡単な図で表現すると以下のようになります。  
OpenAPI Object の詳細は[こちら](https://github.com/OAI/OpenAPI-Specification/blob/OpenAPI.next/versions/3.0.0.md#openapi-object)でご確認ください。

![](/articles/assets/lopburny/img/lopburny_blog_7_pic_4.png)

ご参考までに、OpenAPI (Swagger) 2 では、ルートレベルに `securityDefinitions` を定義して各 API に指定します。OpenAPI 3 になって項目が多少変わっていますので注意が必要です。

いかがだったでしょうか。

前回の記事に続き、Swagger UI を利用する便利さをお伝えしたく、WebAPI 呼び出しに Bearer トークンが必要となる場合の API 定義について記事にしてみました。最後まで読んでいただきありがとうございました。
