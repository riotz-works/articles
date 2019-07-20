---
title: OpenAPI (Swagger) を利用して効率よく WebAPI の仕様を管理する
permalink: efficient-way-to-manage-api-definition
date: 2019-07-21
author: lopburny
categories: 開発
tags:
- OpenAPI
- AWS
- CircleCI
- Git
- GitHub
---

![](/articles/assets/lopburny/img/lopburny_blog_3_thumbnail.jpg)

Web 開発の現場でよく耳にする言葉として Swagger というものがあります。アプリケーション開発においてサーバー（WebAPI）とデータの送受信を行う機能は不可欠なものとなっている現在、多くの現場ではそのための設計モデルとして REST (REpresentational State Transfer) という方式が使われています。この記事では、RESTful な WebAPI の仕様ドキュメントを記述する手段として OpenAPI (Swagger) というものを紹介し、REST Client 機能を備えたインタラクティブな API 仕様ドキュメントを作成・共有する方法についてお伝えしたいと思います。

## OpenAPI (Swagger) とは？

OpenAPI Specification (OAS) といい、RESTful API の仕様を記述するためのフォーマットです。また Swagger とは、Open API を使用するツールのことで、以下のようなものがあります。
<br />
- Swagger Editor
- Swagger UI
- Swagger Codegen

OpenAPI は、元々 Swagger Specification として知られています。REST API を定義するフォーマットの標準化を推進する団体 OpenAPI Initiative (OAI) が結成され、OAI の一員であり Swagger の開発元でもある SmartBear Software により Swagger Specification が寄贈（Donation）されたことで、Open API と言うようになりました。現場では Swagger の方が馴染みがあるので、OpenAPI と言うところはまだ少ないかもしれません。OAS 2.0 は Swagger Specification 2.0 に対応しており、現在（2019年7月）最新バージョンは 3.0 です。

## OpenAPI を利用するメリット

OpenAPI を利用すると何が良いのか、いくつかピックアップしてみました。

### API 定義のバージョン管理が楽になる
基本的に YAML または JSON で記述するため、Git 等を使用してソースコードと同様に管理することができます。つまり、Word, Excel で記述された仕様書に比べて更新履歴を管理していく努力が削減されます。API 定義ファイルと実装を一緒にコミットすることで仕様と実装が乖離しにくくなるため、より正確かつ最新の情報を共有することができます。

### 仕様が明確になる
OAS のフォーマットに合わせて宣言的に書いていくことで曖昧な記述がなくなります。またデータの仕様を明記するための各種データ型がサポートされているので、仕様が明確になります。Swagger UI を利用する場合、Markdown で記述された description 項目の表示がサポートされ、より人に分かりやすく細かい仕様や注意点を表現することができます。

### WebAPI の仕様管理を仕組み化することができる
Swagger UI から API 定義ファイルを読み込むことでインタラクティブな Web ドキュメントを作成することができます。別途 REST Client を使うことなく Swagger UI 上で動作確認ができてしまいます。また Swagger Codegen を使用して WebAPI 専用のクライアントライブラリ（SDK）を自動生成することもできますので、WebAPI を叩くクライアント側の実装も楽になります。  

Amazon API Gateway を利用する場合、API 定義ファイルをインポートすることで API をデプロイすることができます。逆にソースコードから API 定義ファイルを生成する仕組みもあるので、実に色んな使い方ができます。API 定義ファイルの操作を CI に組み込むことで、仕様書の更新・クライアントSDKの生成と配布・WebAPIのデプロイなどをまとめて自動化することが可能になります。  

※ OAS 2 と 3 でフォーマットが異なりますので、サービスによってサポートされる OAS バージョンに注意する必要があります。

## OpenAPI 導入イメージ（例）
![](/articles/assets/lopburny/img/lopburny_blog_3_diagram_1.jpg)

構成の概要としては、
<br />
- API定義ファイルは GitHub で管理
- Swagger UI を SPA（シングルページアプリケーション）として静的サイトホスティング
- CircleCI に連携して API 定義ファイルをアップロード

といったところになります。  

※ Swagger UI を自前でホスティングしないで SwaggerHub 等のサービスを利用する方法もありますが、この記事では割愛させていただきます。詳細は[こちら](https://swagger.io/tools/swagger-ui/download/)をご参考ください。

### Open API を利用して API 定義を作成する
YAML または JSON で API 定義を作成します。個人的には宣言的でコメントも書ける YAML の方がオススメです。作成にはどのようなエディタを使っても問題ないですが、編集時にリアルタイムで Swagger UI のプレビューを表示してくれる Swagger Editor が便利です。正しくないフォーマットが記述された場合、Validation してくれるのでとても助かります。フォーマットの仕様については、[こちら](https://swagger.io/specification/)をご参考ください。  

※ OAS 2 と 3 でフォーマットが異なりますので、Web 等に掲載されているスニペットを参考にする場合は注意が必要です。  

引き続き、作成した API 定義を Swagger UI で表示するため、メニューの「File」から「Save as YAML」を選択し、サンプルの API 定義を保存しておきます。

![](/articles/assets/lopburny/img/lopburny_blog_3_pic_1.jpg)

### Swagger UI を使用して API 定義ファイルを読み込む
Swagger UI を API 仕様ドキュメントとして活用するためには、Swagger UI (SPA) と API 定義ファイルをホスティングする必要があります。例えば、以下のような構成にすることができます。

| 項目 | 内容 |
|-----|-----|
| Swagger UI | `https://docs.sample-api.dev/swagger-ui/index.html` |
| API 定義ファイル | `https://docs.sample-api.dev/api-definitions/{Gitブランチ名}/sample.yml` |

クエリパラメータに API 定義ファイルの URL を指定することで読み込むことができます。

```
https://docs.sample-api.dev/swagger-ui/index.html?url={API定義ファイルのURL}
```

また、API 定義ファイルのパスに Git ブランチ名を指定して CI を回すことで、API 定義ファイルそのもののバージョン管理ができるだけでなく、レビュー中のブランチについても仕様の確認が容易にできるような仕組みづくりが可能になります。

### Swagger UI を SPA としてホスティングする
Swagger UI の GitHub リポジトリにて、最新の Swagger UI Dist をダウンロードします。npm を利用するか、GitHub リポジトリからソースコードをダウンロードして解凍、 dist というディレクトリをまるごと静的サイトホスティングすれば OK です。

![](/articles/assets/lopburny/img/lopburny_blog_3_pic_2.jpg)

クエリパラメータを指定しない場合、API 定義のデフォルト値が使用されます。そのまま他の API 定義を読み込みたい場合は、下の赤枠に URL を入力して右側「Explore」ボタンを押します。各 API の項目を広げて「Try it out」、パラメータを入力して「Execute」することで、Swagger UI 上で API の動作確認ができます。別途 REST Client を使う必要がなく、とても便利です！  

静的サイトホスティングについては色んな方法がありますが、AWS 環境であれば[別の記事](https://riotz.works/articles/lopburny/2019/07/05/hosting-static-web-page-on-aws-with-s3-and-cloudfront/)でやり方を紹介しておりますので是非ご参考ください。Swagger UI を用意して社内向けまたは開発パートナー向けにアクセス制限をかけて共有するなど、色んな利用シーンが考えられるのではないでしょうか。


## まとめ

<br />

- OpenAPI をうまく利用することで、API 仕様の明確化・管理の効率化が図れる  
- Swagger UI をインタラクティブな API 仕様ドキュメントとして活用する


いかがだったでしょうか。  

OAS で API 定義を書いていくと、かえって記述量が増えて疲れるようになったといった意見もありますので、この記事の内容がすべての現場にフィットするとは限りません。ですが、ある程度開発規模が大きく、人の入れ替わりも多いような現場であれば、仕様ドキュメントの管理をしっかり行いたいということもあるのではないかと思います。今後も OAS と Swagger の使い方について具体的に紹介していきたいと思いますので、まだ使ったことないよー！という方がいましたら、是非試してみてください。
