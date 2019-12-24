---
title: Shifter Webhooks と Gridsome で作る、最初のウェブサイト on Netlify
permalink: first-website-with-shifter-webhooks-and-gridsome-on-netlify
date: 2019-12-24
author: lulzneko
categories: フロントエンド
tags:
- Shifter
- JAMstack
- PWA
- Gridsome
- WordPress
- Netlify
---

本記事は [Shifter Advent Calendar 2019](https://qiita.com/advent-calendar/2019/getshifter) の 24日目です。

![](/articles/assets/lulzneko/seminar/shifter/shifter.png)


昨日は [Tai / JOTAKI Taisuke 🍺（@tekapo）さん](https://twitter.com/tekapo) の [Shifter-LocalでWordPressテーマ開発環境を作る – わーどぷれすっ!](https://wp.tekapo.com/2019/12/23/wordpress-theme-development-on-shifter-local/) です。Shifter をローカルで動かして開発する方法が紹介されてます。スゴイ！

さぁ、今日は私の出番。アドベントカレンダーのネタや！と思っていたのですが、諸般の事情により時間切れて [Toro_Unit（山の上のとろゆに）（@Toro_Unit）さん](https://twitter.com/toro_unit) 公開の Shifter Webhooks + Gridsome にマル乗っかりさせて頂いた記事になります。(すみません💦)


## Shifter と Netlify デプロイ連携について
2019年4月3日に開催された [Shiftup! JP_Getshifter Vol3！はじめてのスタティックサイトジェネレーター](https://eventregist.com/e/xiza3ieCWYFc?lang=ja_JP) のイベントへ参加した際に、[Shiftup! JP_Getshifter Vol3！ 振り返り、Shifterのヘッドレス CMS 化に思いを馳せる](/articles/lulzneko/2019/04/06/think-of-shifters-headlesscms-nize-on-shiftup-vol3/) という記事を書きました。

それから１ヶ月ちょっと、何と公式で「[Shifter Webhooksで Netlify上のGatsbyサイトにWordPressコンテンツをインポート可能になりました](https://www.digitalcube.jp/shifter/4434/)」という神対応がありました。

詳しくは公式のブログを読んでいただくとして、概要としてしては以下になります。
GatsbyJS で作ったプロジェクトを、あらかじめ Netlify へデプロイしておきます。そして Shifter Webhooks に Netlify の BuilhHook を登録します。Shifter から `Send webhook` することで Netlify でビルド＆デプロイされます。
![](/articles/assets/lulzneko/seminar/shifter/05-03.png)


## Shifter + Gridsome で Netlify へ、デプロイ
Shifter 公式の機能としては「Shifter から Webhook を呼び出す」ものになります。GatsbyJS のコードや方法はサンプルとしての位置づけになります。そう [Gridsome](https://gridsome.org/) 推しの私としては、やはり Shifter + Gridsome で実現したいところ。

さっそく実装を検討しようとした(どころか時間切れな)ところ、[Toro_Unit（山の上のとろゆに）（@Toro_Unit）さん](https://twitter.com/toro_unit) が素晴らしいプロダクトを公開してくださりました！ Shifter 公式の GatsbyJS で解説されていた話を Gridsome 版にポーティングしたものです！！

<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://github.com/torounit/gridsome-shifter" data-iframely-url="//cdn.iframe.ly/h5tUGKQ"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>

こちらを使うと、いい感じです！
以上、私のアドベントカレンダー終了です 😇


## torounit/gridsome-shifter を使って Shifter + Gridsome on Netlify
とはいえ、さすがに 24日、ここで引くわけにはいかないので、[torounit/gridsome-shifter](https://github.com/torounit/gridsome-shifter) を使わせていただいて、Shifter + Gridsome on Netlify をやってみます。
※ あらかじめ Shifter と GitHub、Netlify へサインアップし、ログインしておきます


### Shifter のサイト作成
Shifter の Create new site https://go.getshifter.io/admin/sites/create へアクセスします。
今回は Webhooks を使いますので [Tier 2] の [Select] をクリックします。
![](/articles/assets/lulzneko/develop/shifter/01-008.png)

注文の確認とサイト名の確認が表示されるので `Tier 2` であることを確認し、[Site name] を入力します。サイト名が決まったら [Create] をクリックします。
![](/articles/assets/lulzneko/develop/shifter/01-009.png)

Shifter で、サイトが立ち上がりました！
[CloudFront Domain] をひかえておきます。
![](/articles/assets/lulzneko/develop/shifter/01-010.png)

続いて [Start WordPress] & [Dashbord] をクリックして記事を投入します。Webhooks を使うために以下の３点を作成しておきます。
- 最低1つ以上の投稿があること（タグ設定済みであること）
- 最低1つ以上の固定ページがあること
- 現時点では投稿に画像を入れておくこと ([sourceUrl error · Issue #15 · gridsome/gridsome-starter-wordpress](https://github.com/gridsome/gridsome-starter-wordpress/issues/15))
![](/articles/assets/lulzneko/develop/shifter/01-014.png)


### GitHub プロジェクトの作成
https://github.com/torounit/gridsome-shifter へアクセスします。
画面右上の [Fork] ボタンをクリックして、自分のアカウントへフォークします。
※ 私の環境だと [Deploy to netlify] ボタンをうまく動作させられなかったのですが、こちらを使うともっと簡単のはず
![](/articles/assets/lulzneko/develop/shifter/01-001.png)

また、今回のタイミングが悪かっただけと思いますが以下のエラーが発生したため Gridsome のバージョンをあげました。変更する場合は `package.json` を直接編集で問題ありません。今回は `"gridsome": "^0.7.12"` としました。
```console
12:09:14 AM: Error:
12:09:14 AM: Vue packages version mismatch:
12:09:14 AM: - vue@2.6.10
12:09:14 AM: - vue-server-renderer@2.6.11
12:09:14 AM: This may cause things to work incorrectly. Make sure to use the same version for both.
```


### Netlify でサイト構築
https://app.netlify.com/ へアクセスし、[New site from Git] をクリックします。
![](/articles/assets/lulzneko/develop/shifter/01-015.png)

[GitHub] をクリックします。
※ はじめての Netlify/GitHub 連携になる場合は GitHub のサイトが開き Netlify への認可を求められるので [Authorize netlify] します
![](/articles/assets/lulzneko/develop/shifter/01-016.png)

リポジトリの一覧が表示されるので、先ほど Fork したリポジトリを選択します。
![](/articles/assets/lulzneko/develop/shifter/01-017.png)

設定項目は、とくに変更する必要がないので [Deploy site] をクリックします。 
![](/articles/assets/lulzneko/develop/shifter/01-018.png)

ビルドが失敗しますが気にせず [Site settings] をクリックします。
※ この段階ではビルドの Webhook で起動していないためです
![](/articles/assets/lulzneko/develop/shifter/01-004.png)

画面左のメニューから [Build & deploy] を選択し、下へスクロール Build hooks から [Add build hook] をクリックします。
![](/articles/assets/lulzneko/develop/shifter/01-005.png)

Build hooks の設定が表示されます。
[Build hook name] 管理しやすい名称を入力し [Save] をクリックします。
![](/articles/assets/lulzneko/develop/shifter/01-006.png)

ビルド用の Webhook URL が生成されるのでひかえておきます。
![](/articles/assets/lulzneko/develop/shifter/01-007.png)


### Shifter Webhooks を設定！
お待ちかねの Shifter Webhooks を設定します。

Shifter の WordPress ダッシュボードの左メニュー下の [Shifter] から [Webhook] を選択します。
[Webhook URL] に、先ほど取得した Netlify のビルド Webhook URL を入力し [Save Changes] をクリックします。
![](/articles/assets/lulzneko/develop/shifter/01-012.png)

画面上部のメニューバーから [Shifter] - [Send webhook] をクリックします。
![](/articles/assets/lulzneko/develop/shifter/01-013.png)

Netlify へ戻るとサイトが公開されていることが確認でき、サイトの URL をクリックすると Shifter で投稿した内容が表示されます。
(画像とかが抜けているのは Publish したけど Generate してないからかもです。これから確認。)
![](/articles/assets/lulzneko/develop/shifter/01-019.png)
![](/articles/assets/lulzneko/develop/shifter/01-020.png)
※ メンテナンスのため、画像 URL のサイトはアクセスできません。



----

まだ最初の連携なので CSS どころか画像も表示できてない状態ですが、これは連携や Gridsome での開発を楽しめるところ。まずは Shifter で記事を投稿し、それが Gridsome を通して Netlify へ公開できるところが素晴らしいです。

そして何よりも、素晴らしいプロジェクトを公開してくださった [Toro_Unit（山の上のとろゆに）（@Toro_Unit）さん](https://twitter.com/toro_unit) ありがとうございます！！

明日は〆で [Seiji Akatsuka（@seijiakatsuka）さん](https://twitter.com/seijiakatsuka) の「来年の抱負？」です。楽しみですね♪

では、ハッピーホリデー！！
