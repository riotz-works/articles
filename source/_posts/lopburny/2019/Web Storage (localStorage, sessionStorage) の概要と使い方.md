---
title: Web Storage (localStorage, sessionStorage) の概要と使い方
permalink: introduction-and-use-of-web-storage
date: 2019-09-06
author: lopburny
categories: フロントエンド
tags:
- Web
- HTML
- Browser
- JavaScript
- Cookie
---

ウェブアプリケーション開発に、localStorage や sessionStorage といった Web Storage を使うことが多くなってきています。

![](/articles/assets/lopburny/img/lopburny_blog_10_thumbnail.jpg)

この記事では、Web Storage についての概要と使い方についてまとめてご紹介します。

## Web Storage とは何か

ブラウザのローカル領域に key-value 形式のデータを保存する仕組みです。Web Storage の仕様をサポートするブラウザは Web Storage API が使えるようになっています。JavaScript でこの API を利用することで Web Storage にアクセスすることができます。保存できるデータ型は文字列のみで、オリジンごとに区切られた保存領域（5MBまで）を持ち、異なるオリジンの Web Storage にアクセスすることはできません。

「オリジン」について少し具体的に説明すると、
<br>
- URL のスキーム（e.g. `http`, `https`）
- ホスト（e.g. `example.com` 等のドメイン）
- ポート（e.g. `80`, `443`）

上記の項目すべてが同じであれば、Web Storage にアクセスしてデータを出し入れすることができます。  
例えば、以下２つの URL においては同じオリジンとして Web Storage が共有されます。
```
https://example.com/main/index.html
https://example.com/sub/index.html

```

また、以下についてはドメインが異なる（オリジンが異なる）ため、お互いの Web Storage にアクセスすることはできません。
```
https://example1.com/index.html
https://example2.com/index.html
```

サブドメインでも Web Storage は異なります。
```
https://app1.example.com/index.html
https://app2.exapmle.com/index.html
```

平たく言えば、同じウェブサイト内・アプリ内であれば使えるということになりますね。

Web Storage は、2019年現在、モバイルを含めほとんどのモダンブラウザにてサポートされていますが、正確には[こちら](https://developer.mozilla.org/ja/docs/Web/API/Web_Storage_API#Browser_compatibility)をご参考ください。

実際に Web Storage を使っている様子を確認することもできます。Google Chrome の場合、ディベロッパーツールのコンソールを開き、Application タブ > 左側メニュー Storage から Local Storage または Session Storage をクリックして中身を見ることができます。

![](/articles/assets/lopburny/img/lopburny_blog_10_pic_1.jpg)

ここまでの内容は以下のドキュメントを参考にしていますので、詳細を知りたい方は一度読んでみてください。

<br>

<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://developer.mozilla.org/ja/docs/Web/API/Web_Storage_API" data-iframely-url="//cdn.iframe.ly/rll6VAC"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>

<br>

<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://developer.mozilla.org/ja/docs/Glossary/Origin" data-iframely-url="//cdn.iframe.ly/gsgAcFm"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>

## localStorage と sessionStorage
Web Storage は２つのタイプのものがあります。

### localStorage
<br>

- 保存したデータは削除しない限り永続的に保存される（なくならない）
- 別タブやウィンドウでも共有される
- ブラウザを閉じたりもう一度開いたりしてもデータは残る

### sessionStorage
<br>

- 保存したデータはブラウザを閉じるまでの間のみ保存される
- 別タブやウィンドウでも共有されない

## 用途
どういったユースケースがあるのでしょうか？実際のところ、シンプルかつ便利なゆえに本当に色んな用途で使われていますが、よく見かけるケースとしていくつかピックアップしてみると、以下のようなことが挙げられます。
<br>
- 表示に関する各種設定情報（e.g. お知らせ、通知表示の有無）
- ユーザーの操作やアクションに関する情報（e.g. お気に入り、閲覧したページ、商品）
- クライアント側の使用状況をまとめてサーバーに送信するための一時保管（e.g. エラーIDをまとめてログサーバーに送信）
- サーバーとのデータ不整合を防止するためのバージョン情報

一般的には localStorage を利用するケースが多いと思います。また、すべての場面において使って問題ないわけではありません。いくつか注意点がありますので、これについては別記事で紹介する予定です。ぜひチェックしてみてください。

## Cookie との違いは？
クライアント側でデータを保存する仕組みということで Cookie と似たような印象を持たれることも多いかもしれません。念のため簡単に触れておきたいと思います。

### 仕組み
Cookie は、サーバーと HTTP 通信をする度に HTTP ヘッダーに含まれて送信され、サーバー側からセットすることもできます。よって、サーバーとのやり取りに伴う状態を管理する場合に使われることが多いです。（サーバーとのセッション管理等）

一方、Web Storage はサーバーとの通信とは関係なくクライアント側（ブラウザ）でのみデータを保持します。

### 容量制限
Cookie は 4KB、Web Storage は 5MB です。

### JavaScriptによる操作
通常、Cookie も Web Storage も JavaScript で操作することが可能です。

しかし Cookie 場合、httponly 属性をつけることで JavaScript から操作することを無効にすることができます。これにより悪意のある第３者によって任意のJSが実行される（e.g. XSS: Cross Site Scripting）ような場合でも、Cookie の中身を JavaScript 経由で見ることができなくなります。

Web Storage の場合、そのような機能はありません。

 
他にも、Cookie には様々な属性（domain, path, 有効期限… etc.）が用意されているため、挙動を細かく制御することができます。

## 基本的な使い方
Web Storage API を利用します。localStorage も sessionStorage も、同じく Storage インタフェースを実装する形になっており、使い方は同じです。

```javascript
// Storage.setItem(key, value)
// Storage.getItem(key)

localStorage.setItem('dummy_key', 'dummy_value');
const item1 = localStorage.getItem('dummy_key');

console.log(item1); // dummy_value

sessionStorage.setItem('dummy_key', 'dummy_value');
const item2 = sessionStorage.getItem('dummy_key');

console.log(item2); // dummy_value
```

Web Storage の key と value に使えるデータ型は、文字列のみとなります。配列やオブジェクトのような形でデータを保存したい場合は、一度 JSON 文字列に変更して保存します。

```javascript
localStorage.setItem('dummy_key_2', JSON.stringify({ name: 'John', age: 20 }));
const item3 = localStorage.getItem('dummy_key_2');

const parsed1 = JSON.parse(item3); // {"name":"John","age":20}

sessionStorage.setItem('dummy_key_2', JSON.stringify({ name: 'John', age: 20 }));
const item4 = sessionStorage.getItem('dummy_key_2');

const parsed2 = JSON.parse(item4); // {"name":"John","age":20}
``` 

保存したデータを削除することもできます。

```javascript
// Storage.removeItem(key)
// Storage.clear()

localStorage.removeItem('dummy_key');
sessionStorage.removeItem('dummy_key');

// すべて削除する
localStorage.clear();
sessionStorage.clear();
``` 

いかがだったでしょうか。

Web Storage はシンプルながら何かと役に立つ場面が多く、大変便利ですが、いくつが注意点もあります。これについては別記事にて紹介する予定ですので、合わせてチェックしてみてください。

最後まで読んでいただきありがとうございました。





















