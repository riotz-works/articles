---
title: CSV ファイル の バッチ連携 も AWS Lambda で サーバーレス
permalink: CSVファイルのバッチ連携もAWS-Lambdaでサーバーレス
date: 2017-12-07
author: lulzneko
categories: サーバーレス
---

![](/articles/assets/lulzneko/serverless/serverless.jpg "サーバーレス")

この記事は [Serverless Advent Calendar 2017](https://qiita.com/advent-calendar/2017/serverless) の ７日目になります。

[Serverlessconf Tokyo 2017](http://tokyo.serverlessconf.io/) で 発表させていただく機会をいただき、 [Java チームが選択した TypeScript による AWS Lambda 開発](http://riotz.works/slides/?2017-serverless-conf) というタイトルで お話をさせていただきました。

今回は そのシステムの裏手側、CSV ファイル を 連携するバッチ を サーバーレスで実現したアーキテクチャについて ご紹介します。

## システム全体の概略図
ざっくりと下図のようなシステムで AWS 上に作られています。
スマートフォン などから IoT 機器を操作するようなクラウド・サービスがあり、そのバックグラウンド・プラットフォームになります。認証や機器のデータ管理を行うシステムです。
このシステムは、図の [Amazon API Gateway](https://aws.amazon.com/jp/api-gateway/) と [AWS Lambda](https://aws.amazon.com/jp/lambda/) が ペアになっているアイコン毎が１つ１つのマイクロサービスとして作られています。つまり 10を超えるマイクロ・サービスの集合体になります！
![](/articles/assets/lulzneko/serverless/01.png)

今回の話は、この図の右上部分 "Traditional DC"、企業の伝統的なデータセンターからデータを受ける部分です。
この部分は伝統とシキタリにのっとり CSV ファイル を 受け取り、処理する仕組みが必要となります。

※ CSV の C は Comma と 信じてましたが、Character や Column 説 (e.g. [Wikipedia](https://ja.wikipedia.org/wiki/Comma-Separated_Values#character-separated_values)) が あり 広義には Comma に 限らず特定の文字でデータを区切るファイルらしい。どうでもいいけど。。。


## なぜ AWS Lambda ？
このようなシステム を AWS で 実装するには、[AWS Batch](https://aws.amazon.com/jp/batch/) や [AWS Glue](https://aws.amazon.com/jp/glue/) などを使うかと思います。しかしながら、今回は Lambda で 実装することにしました。

背景としては、以下のような点があります。
- 少人数の DevOps チームであり、AWS の マネージド度合いが高いほど嬉しかった
- データ量や処理量から想定してコスパが優れていた
- １回に連携するデータ量 が Lambda の 処理時間におさまる可能性が持てた

特に２番目のコストについては、Glue に対して圧倒的によかったというのが大きかったです。今回の処理は ETL に近いとはいえ、DWH 連携ではなく、DB の マスター・データ登録だったので Glue を 活かしきれないという点がありました。

なお Lambda の 実行ランタイムは Java に なります。
(Serverlessconf Tokyo 2017 の [発表資料](http://riotz.works/slides/?2017-serverless-conf) の 通り、Java チーム なので)


## CSV ファイル 連携 の アーキテクチャ概要図
![](/articles/assets/lulzneko/serverless/02.png)
"Traditional DC" からは、[Amazon S3](https://aws.amazon.com/jp/s3/) に CSV を おいてもらうことにします。FTP/SFTP とのリクエストがありましたが、サーバーレス厨 とは言わずとも ここは S3 へのアップロードへお願いしたいところ、何としてでも承諾いただきます。(いただきました)

S3 からは [Amazon SNS](https://aws.amazon.com/jp/sns/) へ イベント通知をし、Lambda を 起動します。その Lambda で CSV を パースして DynamoDB へ データを登録します。

複雑なデータの加工 や 外部の API/データ を 参照するような場合は、DynamoDB Streams から Lambda を 呼び出して後続の処理として実装します。このようにすることで、データの登録に特化させて処理数を稼ぐことができ、また DynamoDB Streams の 後続処理は、登録されたデータ１件ごとの処理になるので時間的な余裕が生まれます。

ここでのポイントは S3 ⇒ SNS からの Lambda は DynamoDB への登録だけに特化し、他の処理をしないことです。このようにすることで、なるべく CSV ファイルの対応に処理を振り分けるようにし、時間内で処理できる量を増やすようにしています。


## 処理能力 と アーキテクチャの改良
このような形にすることで、CSV を Lambda で 処理できるようになります。
このアーキテクチャの処理能力は、CSV ファイル １行のデータ量や、カラム数によって変わってくる部分があるかと思いますが、今回のケースでは １ファイル で 約 800行のレコードが処理できました！
て、まったく処理できてません。。。

これでは役に立たないので対応が必要となります。
Lambda は 設定するメモリの使用量によって処理速度が変わるという話もあり、最大に設定してみましたが設定による差は特にありませんでした。ファイルを読み込みながらの順次処理なので、ここでの処理性能はあまり変わらないのでしょう。また DynamoDB へ アクセスするオーバーヘッドも処理性能では大きく変わらないはずです。

では、どうするのか。

![](/articles/assets/lulzneko/serverless/03.png)
S3 に 置かれたファイルを DynamoDB へ 登録する前に、ファイル分割する処理をはさみます。
１回の Lambda で DynamoDB へ 登録できるのは 余裕を見て 500件までとして、CSV ファイルを 500行ごとのファイルに分割して S3 へ 再アップロードします。その 500行ごとのファイルを Lambda で DynamoDB へ 登録する形にしました。

これにより、30,000行まで処理できるようになり現実的なラインまで持っていけるようになりました。S3 からの Lambda に 変わりはないので、サーバーレスの形態も維持できます。


## まとめ
サーバーレスでシステムを作ろうとしても、往々にして発生してしまう既存システムとのバッチ連携。そのために FTP/SFTP やら、CSV パースするための処理やら と どうしても サーバ・インスタンスが欲しくなってしまい、せっかくの サーバーレス が サーバー有り(サーバーレスの反対語って何だろう)になってしまいます。

Glue に 見合う処理であればよいのですが、今回のように小規模なデータ登録となるとオーバースペック気味にもなります。そんな時には S3 からの Lambda で 処理することで、サーバーレスでバッチ連携をするのも手ではないでしょうか。

ポイントとしては以下になります。
- ファイルは S3 に 置いてもらう
- S3 ⇒ SNS から Lambda を 起動し DynamoDB へ データ登録に特化する
- １回の Lambda で処理できるデータ量は少ない、必要に応じてファイル分割の Lambda を はさむ


----

[Serverless Advent Calendar 2017](https://qiita.com/advent-calendar/2017/serverless) 、楽しく、そして勉強になる記事がたくさんです。

昨日 ６日目 は [@RyoheiMorimoto](https://qiita.com/RyoheiMorimoto)さん の [AWS LambdaをTest Runnerとして使ってみる](https://qiita.com/RyoheiMorimoto/items/6cc8db0e0ec249089c7d) でした. ユーザ管理のシステムをサーバーレスで 34歳 タローさん が ハナコさん に なれるかをテストしています。サーバーレス・テスト、面白いですね！

明日 ８日目 は [@narikei](https://qiita.com/narikei)さん です。何の記事か楽しみです！

----



では、ハッピー・サーバーレス・ライフ！ ハッピー・ホリデー！！
