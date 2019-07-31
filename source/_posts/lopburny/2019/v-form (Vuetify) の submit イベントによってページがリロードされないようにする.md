---
title: v-form (Vuetify) の submit イベントによってページがリロードされないようにする
permalink: page-reload-issue-by-implicit-submission
date: 2019-07-31
author: lopburny
categories: フロントエンド
tags:
- Vuetify
- Material Design
- Vue.js
- HTML
---

![](/articles/assets/lopburny/img/lopburny_blog_5_thumbnail.jpg)


今回は Vuetify でシンプルな入力画面を実装するといった時に遭遇するかもしれない挙動の一つとその対応について紹介します。
v-form の中に一つの v-text-field を配置し、フォーカスを当てた状態で Enter キーを押すとページがリロードされることがあります。
投稿されて1年以上経過しているものですが、以下 Vuetify の GitHub Issue にも Bug Report がありました。  

<br>

- [Bug Report] v-form with single v-text-input submits on enter
https://github.com/vuetifyjs/vuetify/issues/3032)  

- [Bug Report] v-form reloads the page when user press enter while editing v-text-field
https://github.com/vuetifyjs/vuetify/issues/4302  


## リロードされる原因

この問題、実は Vuetify に限った話ではなく、前からある Implicit submission （暗黙の submit）という HTML の仕様によるもので、バグということではありません。  

具体的にはデフォルトボタンによる submit が適用される場合で、入力中に Enter キーによって submit イベントが発火します。HTML の仕様書を読んでみると多少ややこしい記述にはなっていますが、少し抜粋して要約・補足すると下記のようになります。  
🔗https://html.spec.whatwg.org/multipage/form-control-infrastructure.html#implicit-submission

> If the form has no submit button, then the implicit submission mechanism must do nothing if the form has more than one field that blocks implicit submission, and must submit the form element from the form element itself otherwise.

form内に submit ボタンがない時、
<br>
- 暗黙の submit をブロックする項目が２つ以上ある場合：暗黙の submit は動作しない
- 暗黙の submit をブロックする項目が１つある場合：Form document の URI へ submit する

と定義されています。

> For the purpose of the previous paragraph, an element is a field that blocks implicit submission of a form element if it is an input element whose form owner is that form element and whose type attribute is in one of the following states: Text, Search, URL, Telephone, E-mail, Password, Date, Month, Week, Time, Local Date and Time, Number

暗黙の submit をブロックする項目とは、form の中にある input 要素で、type 属性が Text, Search, URL, Telephone, E-mail, Password, Date, Month, Week, Time, Local Date and Time, Number というものです。  

つまり、HTMLとしては「一つのテキストフィールドがある」及び「submit ボタン ( `type="submit"` ) がない」状態では、 Enter キーを押すことで sutmit するのが正しい仕様ということですね。

v-form コンポーネントを使用する場合、ボタンに v-btn を利用すると `type="button"` となるため、ブラウザによって暗黙の submit として扱われるようになることが原因となります。  

CodePen で再現してみると以下のようになります。  
※ Vue.js 2.6, Vuetify 2.0 を使っています。

<p class="codepen" data-height="265" data-theme-id="0" data-default-tab="html,result" data-user="lopburny" data-slug-hash="EqWmxG" style="height: 265px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="(Vuetify) v-form reload issue sample">
  <span>See the Pen <a href="https://codepen.io/lopburny/pen/EqWmxG/">
  (Vuetify) v-form reload issue sample</a> by lopburny (ロップバーニー) (<a href="https://codepen.io/lopburny">@lopburny</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

## 解決方法

以下のように submit イベントを抑えてしまいます。

```html
<v-form ref="form" @submit.prevent>
  <v-text-field ...
```

CodePen のサンプルはこちらです。  
※ Vue.js の省略記法を使っています。
<p class="codepen" data-height="265" data-theme-id="0" data-default-tab="html,result" data-user="lopburny" data-slug-hash="gVWEPR" style="height: 265px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="(Vuetify) v-form reload issue sample - resolved">
  <span>See the Pen <a href="https://codepen.io/lopburny/pen/gVWEPR/">
  (Vuetify) v-form reload issue sample - resolved</a> by lopburny (ロップバーニー) (<a href="https://codepen.io/lopburny">@lopburny</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

[Vue.js 公式ドキュメント](https://jp.vuejs.org/v2/guide/events.html#%E3%82%A4%E3%83%99%E3%83%B3%E3%83%88%E4%BF%AE%E9%A3%BE%E5%AD%90) にも、ピンポイントで対応方法が記載されています。

```html
<!-- submit イベントによってページがリロードされません -->
<form v-on:submit.prevent="onSubmit"></form>
```

ユーザーに対して明示的に submit ボタンを押してもらうアクションを期待している場合は、これで解決します。
form 要素の submit イベントを抑制し、ボタンを押したときのクリックイベントで対応する形です。

しかし、あえて Enter キーを押した場合に submit イベントを発火したいということもあります。
Vuetify の場合、v-form コンポーネントと各入力要素の rule 属性によるバリデーションを実装することが多いので、このケースはあまりないかと思いますが、一応上記に加え下記のように対応することで実現できます。

```html
<v-text-field @keyup.enter="customMethod()"></v-text-field>
```

いかがだったでしょうか。

今回紹介した挙動については、古くからある HTML の仕様なので Vuetify に限らず多くの記事が出ておりますが、
Vuetify 特有のバグや癖だと思ってハマることもあるかと思い、記事にしてみました。
最後まで読んでいただきありがとうございました。
