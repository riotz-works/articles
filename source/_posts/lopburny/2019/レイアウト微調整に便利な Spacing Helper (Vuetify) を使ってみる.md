---
title: レイアウト微調整に便利な Spacing Helper (Vuetify) を使ってみる
permalink: arrange-vuetify-components-with-spacing-helper
date: 2019-08-12
author: lopburny
categories: フロントエンド
tags:
- Vuetify
- Vue.js
- CSS
---

Web UI を実装する時、追加したパーツが思った通りの位置に定まらず、どうしても微調整をしたくなることがあります。

![](/articles/assets/lopburny/img/lopburny_blog_6_thumbnail.jpg)

Vuetify のような UI フレームワークを使用している場合、自前で CSS を書き足したり修正するようなカスタマイズは最小限にしておくことが良いと考えられますが、それでも「ここだけなんとかしたい」という場面が出てくると思います。具体的には margin や padding を指定して要素間の間隔を整えたりすることで、例えば以下のように Helper CSS を定義して対応することが多いのではないでしょうか。
<br>

```css
.p-0 {
  padding: 0px;
}

.pt-0 {
  padding-top: 0px;
}

.pb-0 {
  padding-bottom: 0px;
}

.pl-0 {
  padding-left: 0px;
}

.pr-0 {
  padding-right: 0px;
}

...
```

しかし、Bootstrap や Material-UI など、最近のメジャーな UI フレームワークには上記のような CSS Helper Class が予め用意されているので、これらを上手く活用することで、作業効率を高めることができ、きれいなレイアウトに仕上げることができます。この記事では、こういった UI フレームワークの一つ、Vuetify の Spacing Helper Class について紹介します。
※ 詳細は [Vuetify 公式ドキュメント](https://vuetifyjs.com/ja/styles/spacing) にて確認いただけます。

## 使い方
微調整を行いたい要素に、下記フォーマットを用いてクラスを指定します。

```
{property}{direction}-{size} // e.g. <p class="mt-1" ...
```

### property
margin か padding のどれかを指定します。
<br>

- `m` : margin 
- `p` : padding

### direction
direction は指定した property の方向を定めます。

| direction | 方向 |
|-----------|-----|
|  `t`  | top（トップ） |
|  `b`  | bottom（ボトム） |
|  `l`  | left（左） |
|  `r`  | right（右） |
|  `x`  | left と right |
|  `y`  | top と bottom |
|  `a`  | left, right, top, bottom 全てに適用 |

### size
size は property のサイズが 4px 単位で指定されます。`0` - `12` の数字が指定できます。
くっつけたい場合は `0` を指定します。

## サンプルコード
以下、Title + Description 構成のリストに使用したサンプルです。各項目には `py-5` を適用して、間隔を広くしています。また description の行には `ml-5` を適用して、インデントを作ってみました。

<p class="codepen" data-height="265" data-theme-id="0" data-default-tab="html,result" data-user="lopburny" data-slug-hash="OKBZRQ" style="height: 265px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="[Vuetify] Arrange components with spacing helpers ">
  <span>See the Pen <a href="https://codepen.io/lopburny/pen/OKBZRQ/">
  [Vuetify] Arrange components with spacing helpers </a> by lopburny (ロップバーニー) (<a href="https://codepen.io/lopburny">@lopburny</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

このサンプルは挙動を確認するためのシンプルなものですが、v-layout や v-card など色んなコンポーネントに適用できますので、何かと微調整が必要な場合に使用してみると便利だと思います。

いかがだったでしょうか。

この Spacing Helper については、前述のように Vuetify に限らず色んな UI フレームワークで提供されています。個人的には特にデザインの要件が無い限り自前で CSS を書きたくない人なのでこういう機能を重宝しますが、好き嫌いに関係なく必要に応じて便利な機能を使っていく姿勢が大事だと思います。記事を読んでいただきありがとうございました。
