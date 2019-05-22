---
title: ブログで使っている Hexo の SNS 共有リンクに記事タイトルを入れる
permalink: add-article-title-to-sns-share-link-of-hexo-used-in-blog
date: 2019-05-22
author: lulzneko
categories: フロントエンド
tags:
- JAMStack
- Hexo
- Visual Studio Code
---

本ブログで使っている Hexo および、デフォルトのテーマ landscape。とても汎用的で便利ですが、汎用性が高い反面細かいところは自分で作りこむ余地が多くあります。記事の下にある SNS 共有リンクについて、記事タイトルを入れる強化をはかります。

![](/articles/assets/lulzneko/serverless/hexo/hexo.png)

これまで「[SNS 共有リンクをダイアログから展開](https://riotz.works/articles/2019/04/11/improve-sns-shared-links-usability-of-hexo-used-in-blog/)」したり、「[はてなブックのリンクを追加](https://riotz.works/articles/2019/04/10/add-hatena-bookmark-to-sns-share-link-of-hexo-used-in-blog/)」したりと強化してきました。
今回、ブログメンターの [カック@ブロガー / k9u(@kakakakakku)さん](https://twitter.com/kakakakakku) から、ツイートボタンにタイトルが入ってないことを教えていただき、確認したところ URL だけが入っていることが確認できました。この部分を改善します。


## 現在の状態
まずは現在の状態を確認します。
記事の下まで行って [共有] リンクから [Twitter のアイコン] をクリックします。[リンクをあなたのフォロワーに共有する] ダイアログが表示されて、確かに URL だけが入っています。
![](/articles/assets/lulzneko/serverless/hexo/04-01.png)

このままでは、いくら URL が Twitter Card で展開されるとはいえ、ただの URL をツイートしただけになってしまいます。また一生懸命考えて付けたタイトルは(使ってくれるかは別として)できればツイートにのっけてもらいたいものです。要改修です。


## 改修する場所を探して修正
ソースの改修する場所を探します。[Hexo](https://hexo.io/) の landscape テーマが生成している部分で、自分で書いてないので思い当たる箇所はありません。Chrome DevTools の力を借りることにします。

[共有] の [Twitter アイコン] を右クリックして [検証] を選択します。
Chrome DevTools が表示され `<a>` タグがハイライトされます。その中から全文検索用のキーワードを考えます。今回は `class` 属性の `article-share-twitter` で検索すると良さそうです。
![](/articles/assets/lulzneko/serverless/hexo/04-02.png)

Visual Studio Code で全文検索したところ `article.ejs` がかかりました。(残りは自分の記事と、スタイルシート)
![](/articles/assets/lulzneko/serverless/hexo/04-03.png)

該当箇所を確認すると以下のコードになっています。
```
      <a href="https://twitter.com/intent/tweet?url=<%- post.permalink %>" class="article-share-twitter" target="_blank" title="Twitter"></a>
```

`https://twitter.com/intent/tweet` に、パラメーターで記事の URL を渡しているだけのようです。ここにタイトルの文字列が入れば OK ということで、Twitter の URL の仕様を確認します。

[Web Intent — Twitter Developers](https://developer.twitter.com/en/docs/twitter-for-websites/tweet-button/guides/web-intent.html) によると、クエリーパラメーター `text` で、UTF-8 および URL エンコードされた文章を設定すれば良さそうです。また、表示されるダイアログではテキストが選択された状態になり、ユーザーが削除や編集しやすいようになっているとのこと。

他にも `hashtags` などがありますが、今回はブログ記事をツイートしてもらうためのボタンであり、何かのハッシュタグを付けてアクションするようなアプリではないので使わないことにします。この辺は投稿者さんの任意ですね。

**参考情報**
- [Guides — Twitter Developers](https://developer.twitter.com/en/docs/twitter-for-websites/tweet-button/overview)
- [Web Intent — Twitter Developers](https://developer.twitter.com/en/docs/twitter-for-websites/tweet-button/guides/web-intent.html)

改修して、下記にします。
クエリーパラメーター `text=<%- encodeURI(post.title) %>` で URL(URI) エンコードされたタイトルを入れています。
```
      <a href="https://twitter.com/intent/tweet?text=<%- encodeURI(post.title) %>&url=<%- post.permalink %>" class="article-share-twitter" target="_blank" title="Twitter"></a>
```


## できた！
![](/articles/assets/lulzneko/serverless/hexo/04-04.png)

よく見るような感じになりました。



----

今回は、ちょっとライトな感じの記事です。あえて書くまでもないような感じもありましたが、フレームワークやライブラリなどを使っている場合に、自分が作ってない部分の修正方法についての記事はあまりないのかなと思うと、せっかくだから残しておくことにします。(といっても、この記事をググるのは難しそうですが)

現在はデフォルトテーマに頼っていますが、ちゃんとしたボタンを記事の前後に配置したり、ボタンにカウントを表示したりしたいです。テーマもデフォルトでなく変えたいところですが、なかなか手が回らない。。。時間をとって、コツコツと改善ですね。
