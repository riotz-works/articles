---
title: Vuetify 2.0 で追加された v-banner を使ってユーザーのアクションを促すボタン付きバナーを作る
permalink: v-banner-introduction-of-vuetify-2.0
date: 2019-07-26
author: lopburny
categories: フロントエンド
tags:
- Vuetify
- Material Design
- Vue.js
- Nuxt.js
---

先日、Vuetify 2.0 がリリースされ、新しく追加されたコンポーネントの中で個人的にほしかったものを一つ紹介したいと思います。  
※ 2.0のリリースで追加された機能とコンポーネントの一覧は、[こちら🔗](https://github.com/vuetifyjs/vuetify/releases#user-content-new-components-and-features)で確認できます。

## Banner

![](/articles/assets/lopburny/img/lopburny_blog_4_pic_1.jpg)

Material Design 2 で登場した Banner ですが、ユーザーに「確認してほしい」メッセージと、「やってほしい」アクションのボタンを組み合わせて提示するコンポーネントです。Dialog や Snackbar でも似たようなことは実現できますが、その使い方については以下のように定義されています。

| コンポーネント | 優先度 | ユーザーアクション |
|--------------|-------|-----------------|
| Snackbar     | 低    | 任意。Snackbar は自動で表示されなくなる。 |
| Banner       | 中    | 任意。Banner はユーザーによって非表示ボタンが押されるか、Banner を出す原因となった状態が解決されるまで表示される |
| Dialog       | 高    | 必須。Dialog はユーザーが提示されたアクションを実行するか閉じるまでアプリの使用をブロックする。 |

※ Material Design 2 > Banners > Usage 抜粋 [🔗](https://material.io/design/components/banners.html#usage)

Banner は画面に一つのみ表示されるものとして、ユーザーにとって見やすい場所、つまり Top App Bar とコンテンツの間に配置されることが想定されています。Material Design 公式ドキュメントで紹介されている使い方の他にも、重要なメンテナンス予定の告知、アンケートやフィードバックの依頼、キャンペーン等の用途にも応用できるので、結構助かります。わざわざ Dialog を実装するほどでもないし、自動で表示されなくなる Snackbar だとユーザーに気づかれない場合に Banner を使うと便利かなと思います。

## VBanner (v-banner)

Vuetify 2.0 のリリースにて VBanner コンポーネントが追加され、Web UI で使えるようになりました。Vuetify では、single-line / two-line / multi-line とメッセージの行数を指定、メッセージとボタンに加えてアイコンを表示することができます。下記、一般的に使われそうな用例を２つ紹介します。

### single-line + アクションボタン

#### 画面イメージ

![](/articles/assets/lopburny/img/lopburny_blog_4_pic_2.png)

#### サンプルコード
```html
<v-banner single-line>
  ここにバナーのメッセージが入ります。
  メッセージが長くなれば文末が ... という感じで省略されます。
 
  <template v-slot:actions>
    <v-btn text color="deep-purple">DISMISS</v-btn>
    <v-btn text color="deep-purple">RETRY</v-btn>
  </template>
</v-banner>
```


### アイコン + two-line + アクションボタン

#### 画面イメージ
![](/articles/assets/lopburny/img/lopburny_blog_4_pic_3.png)

#### サンプルコード
```html

<v-banner two-line>
  <v-avatar slot="icon" color="deep-purple" size="40">
    <v-icon color="white">
      device_unknown
    </v-icon>
  </v-avatar>
 
  デバイスの画面サイズに合わせてメッセージの文字数を確認しておく必要があります。
  通常は 1-2 行が望ましく、簡潔で理解しやすい文章にすることが重要です。
 
  <template v-slot:actions>
    <v-btn text color="deep-purple">LEARN MORE</v-btn>
    <v-btn text color="deep-purple">GOT IT</v-btn>
  </template>
</v-banner>
```

<br>

いかがだったでしょうか。  

Vuetify の公式ドキュメントには色んな設定値を適用して表示を確認できる [Playground🔗](https://vuetifyjs.com/ja/components/banners#playground) が提供されています。是非試してみてください。

現時点（2019年7月下旬）では Vuetify 2.0 がリリースされて間もないので、いきなり更新すると色々問題もあったりするみたいですが、そのうち改善されることを期待しています。これからのアップデートも楽しみですね！
