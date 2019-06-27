---
title: Nuxt.js で Vue コンポーネントの利用とコンポーネントの相互呼出しをする
permalink: use-of-vue-components-in-nuxtjs-and-cross-call-with-it
alias: /2019/06/15/use-of-vue-components-in-nuxtjs-and-cross-call-with-it/index.html
date: 2019-06-15
author: lulzneko
categories: フロントエンド
tags:
- Nuxt.js
- PWA
- JAMStack
- TypeScript
---

Nuxt.js を使うことで JAMStack なアプリを高速に構築できます。それに加えて Vue.js のフレームワークであるため Vue コンポーネントが使えるというメリットがあります。UI のパーツをコンポーネントとして切り出すことで実装をシンプルにし、また再利用性を高めることができます。

![](/articles/assets/lulzneko/develop/nuxtjs/nuxtjs.png)


これまで、[Nuxt.js](https://ja.nuxtjs.org/) の [PWA](https://developer.mozilla.org/ja/docs/Web/Progressive_web_apps) with [TypeScript](https://www.typescriptlang.org/) として、ベースとなるアプリを作ってきました。このままページを追加していくことで簡単にアプリ画面を増やしていけますが、その前に Nuxt.js (というか Vue.js 系フレームワーク) の、強力な機能である「Vue コンポーネント」を使い実装をシンプルにしたいと思います。

説明の都合により下記シリーズのソースを使いますが一般的な Vue コンポーネントの使い方ですので、下記 "開始時のアプリソース" ではなくても大丈夫です。

**シリーズの記事**
- [Nuxt.js で PWA(Progressive Web Apps) のベースアプリを作る](https://riotz.works/articles/lulzneko/2019/05/09/develop-base-app-for-pwa-with-nuxtjs/)
- [Nuxt.js PWA(Progressive Web Apps) のベースアプリをTypeScript対応する](https://riotz.works/articles/lulzneko/2019/05/10/typescripting-base-app-of-nuxtjs-pwa/)
- [Nuxt.js PWA のベースアプリを GitHub Pages へデプロイする](https://riotz.works/articles/lulzneko/2019/05/13/deploy-nuxtjs-pwa-base-app-to-github-pages/)
- **Nuxt.js で Vue コンポーネント利用と相互呼出しする** (本記事)

**環境**
本記事の開発環境は以下となります。
- Windows 10 64bit + WSL Ubuntu 18.04.1 LTS
- Visual Studio Code
- Node.js 12.2.0
- Yarn 1.15.2
- Nuxt.js 2.6.3
- 開始時のアプリソース https://github.com/riotz-works/samples-pwa-base-app/tree/0.0.4


## 絵文字ボタンを Vue コンポーネントにする
以下はページ(`/pages/index.vue`) へ追加した絵文字ボタンに関連するコードです。絵文字に応じた `tada` などの文字列が異なるだけで、ほぼ同じコードの繰り返しとなっています。

**`/pages/index.vue`** (該当箇所のみ抜粋)
```html
<div class="actions">
  <a @click="addTada" class="button--action">🎉 {{ tada }}</a>
  <a @click="addSparkles" class="button--action">✨ {{ sparkles }}</a>
  <a @click="addThumbsup" class="button--action">👍 {{ thumbsup }}</a>
  <a @click="addHeart" class="button--action">🧡 {{ heart }}</a>
</div>

<script lang="ts">
export default Vue.extend({
  data() {
    return {
      tada: 0,
      sparkles: 0,
      thumbsup: 0,
      heart: 0
    }
  },
  methods: {
    addTada: function(): void {
      this.tada++
    },
    addSparkles: function(): void {
      this.sparkles++
    },
    addThumbsup: function(): void {
      this.thumbsup++
    },
    addHeart: function(): void {
      this.heart++
    }
  }
})
</script>
```

これを Vue コンポーネント化して、再利用可能な絵文字ボタン・コンポーネントにします。
まず、ボタン１つとして表現される `/components/EmojiButton.vue` を作ります。ボタン１つ分なので `<a>` タグが１つ、汎用的なメソッド名、変数名にします。絵文字は、親コンポーネントから渡された絵文字を使いたいので `props: { emoji: String }` として受け取るようにします。

**`/components/EmojiButton.vue`** (コード全文)
```html
<template>
  <a @click="add" class="button--action">{{ emoji }} {{ counter }}</a>
</template>


<script lang="ts">
import Vue from 'vue'

export default Vue.extend({
  props: {
    emoji: String
  },
  data() {
    return {
      counter: 0
    }
  },
  methods: {
    add: function(): void {
      this.counter++
    }
  }
})
</script>


<style scoped>
.button--action {
  display: inline-block;
  border-radius: 4px;
  border: 1px solid #3b70d0;
  color: #3b70d0;
  text-decoration: none;
  margin: 0 2px;
  padding: 10px 10px;
  cursor: pointer;
}

.button--action:hover {
  color: #fff;
  background-color: #3b70d0;
}
</style>
```

絵文字ボタンを利用するページ側(`/pages/index.vue`) では、ハードコードしていた絵文字ボタンをコンポーネントの利用に変更します。コンポーネントのタグは `<emoji-button>` で、 `data() { emojis }` の配列を使った　`v-for` の繰り返しで作ります。また古いハードコードしていた絵文字ボタンのコードは削除します。

**`/pages/index.vue`** (該当箇所のみ抜粋)
```html
<template>
  <section class="container">
    <div>
      <div class="actions">
        <emoji-button v-for="e in emojis" :key="e" :emoji="e" />
        <a @click="clear" class="button--grey">Clear</a>
      </div>
    </div>
  </section>
</template>


<script lang="ts">
import Vue from 'vue'
import EmojiButton from '~/components/EmojiButton.vue'
import Logo from '~/components/Logo.vue'

export default Vue.extend({
  components: {
    Logo,
    EmojiButton
  },
  data() {
    return {
      emojis: [ '🎉', '✨', '👍', '🧡' ]
    }
  },
  computed: {
    counter(): number {
      return 0
    }
  },
  methods: {
    clear: function(): void {
    }
  }
})
</script>
```

これにより絵文字ボタンがコンポーネント化され、再利用可能なボタンとなりました。
それぞれのボタンに絵文字が表示され、クリックするとクリックされたボタンのカウンターが増えます。似たようなコードがなくなりスッキリしました。


## 子コンポーネントから、親コンポーネントにイベントを送る
絵文字ボタンのコンポーネントはできましたが、トータルのカウンターは機能しなくなりました。絵文字ボタン・コンポーネントのカウンターの合計だけロゴ(`/components/Logo.vue`) コンポーネントを回転する機能がありましたが、現在は回転しません。

これまではページ側(`/pages/index.vue`) に、すべての情報があったのでボタン・クリック数の合計をロゴ・コンポーネントに送れました。しかし各カウンターが絵文字ボタン・コンポーネントへ移動してしまったため、カウンターの合計がページ側では作れなくなりました。

そこで、絵文字ボタン・コンポーネント(子コンポーネント)はクリックされたことを、ページ(親コンポーネント)へ伝えるようにします。ページ側では、その各絵文字ボタン・コンポーネントから伝えられたクリックの回数をカウントして合計としてロゴ・コンポーネントへ送るようにします。

そのクリックされたことを伝えるために「イベント」を使います。絵文字ボタン・コンポーネント(子コンポーネント)から、ページ(親コンポーネント)へ「イベント通知」をすることで、クリックされたことを伝えます。


### 子コンポーネント「絵文字ボタン・コンポーネント」を修正
絵文字ボタン・コンポーネントのカウントアップ処理にイベント通知 `this.$emit('countup')` を追加します。

**`/components/EmojiButton.vue`** (該当箇所のみ抜粋)
```html
<script lang="ts">
import Vue from 'vue'

export default Vue.extend({
  methods: {
    add: function(): void {
      this.counter++
      this.$emit('countup')
    }
  }
})
</script>
```

`$emit` は、Vue コンポーネント間でイベントを通知するための仕組みです。今回は絵文字ボタン・コンポーネントが子コンポーネントなので、親コンポーネントであるページへイベントを通知します。

イベント通知は `$emit('イベント名', [...引数])` の書式です。引数は可変長配列で、イベントの通知先へ渡されます。今回は `countup` という名前のイベント名で引数無しの通知を行っています。

**参考情報**
- [vm.$emit( eventName, […args] ) — API — Vue.js](https://jp.vuejs.org/v2/api/#vm-emit)


### 親コンポーネント「ページ」を修正
親コンポーネントであるページ側で、子コンポーネントである絵文字ボタン・コンポーネントからのイベント通知を受け取ります。

**`/pages/index.vue`** (該当箇所のみ抜粋)
```html
<template>
  <section class="container">
    <div>
      <div class="actions">
        <emoji-button v-for="e in emojis" :key="e" :emoji="e" v-on:countup="add" />
        <a @click="clear" class="button--grey">Clear</a>
      </div>
    </div>
  </section>
</template>


<script lang="ts">
export default Vue.extend({
  data() {
    return {
      total: 0,
      emojis: [ '🎉', '✨', '👍', '🧡' ]
    }
  },
  computed: {
    counter(): number {
      return this.total
    }
  },
  methods: {
    add: function(): void {
      this.total++
    },
  }
})
</script>
```

`<emoji-button>` タグに `v-on:countup="add"` 属性を追加し、絵文字ボタン・コンポーネントの `countup` イベントを受け取ります。属性値 `add` は、メソッド呼出しで `add()` メソッドを呼び出すという定義です。これにより子コンポーネントのイベントを受け取って、親コンポーネントで処理をするという形が作れます。

上記定義で呼び出されるメソッドを `<script>` に追加します。これは通常のメソッド定義と同様です。今回は `data() { total }` をインクリメントするメソッドとします。

これにより各絵文字ボタン・コンポーネントがクリックされると、ページ側に `countup` イベントが伝わります。そのイベントの回数を `total` にインクリメントしていくことで、全絵文字ボタン・コンポーネントのカウンター合計が作れます。

そして `total` を `computed: { counter() }` につなぐことで、これまで機能していたロゴの回転が復旧します。

**参考情報**
- [v-on — API — Vue.js](https://jp.vuejs.org/v2/api/#v-on)


## 親コンポーネントから、子コンポーネントにイベントを送る
最後に [Clear] ボタンを復旧します。[Clear] ボタンは絵文字ボタンと異なり１つだけですし、すべての絵文字ボタン・コンポーネントのカウンターをリセットする特別な役割があります。そのため親コンポーネントに残しました。

[Clear] ボタンの機能であるリセットですが、親コンポーネント内の `total` を `0` にリセットすることはできます。それによりロゴの回転を止めて、カウンターのリセットもできます。

しかしながら子コンポーネントのカウンターは直接リセットできません。こちらもイベント通知を使いリセットします。先ほどと逆なり、親コンポーネントから子コンポーネントへイベントを通知します。


### 親コンポーネント「ページ」を修正
ページ側はイベントを通知する先の子コンポーネントである、全絵文字ボタン・コンポーネントを特定してイベント通知します。今回は絵文字ボタン・コンポーネントしかないので(正確には、ロゴ・コンポーネントも子コンポーネントですが)、送り先を意識しにくいですが多数のコンポーネントを抱えている親コンポーネントであるからこそ、どのコンポーネントへイベント通知するか考える必要があります。

**`/pages/index.vue`** (該当箇所のみ抜粋)
```html
<template>
  <section class="container">
    <div>
      <div class="actions">
        <emoji-button v-for="e in emojis" :key="e" :emoji="e" v-on:countup="add" ref="EmojiButtons" />
        <a @click="clear" class="button--grey">Clear</a>
      </div>
    </div>
  </section>
</template>


<script lang="ts">
export default Vue.extend({
  methods: {
    clear: function(): void {
      this.total = 0;
      (this.$refs.EmojiButtons as Vue[]).forEach((value: Vue) => value.$emit('clear'))
    }
  }
})
</script>
```

`<emoji-button>` タグの `ref="EmojiButtons"` 属性は、`EmojiButtons` の文字列でタグを参照されるための設定です。`ref` 属性は通常の HTML タグと、子の Vue コンポーネント・タグのどちらにも使用できます。

参照する側は `$refs.[ref 属性の値]` で行います。今回は `EmojiButtons` で登録したので `$refs.EmojiButtons` でアクセスします。`clear()` メソッドでは `EmojiButtons` で登録された全絵文字ボタン・コンポーネントを取得し、`$emit('clear')` で `clear` というイベント名で引数無しの通知を送っています。`$emit()` の使い方は、子コンポーネントからイベントを送った時と同じです。

**参考情報**
- [ref — API — Vue.js](https://jp.vuejs.org/v2/api/#ref)
- [vm.$refs — API — Vue.js](https://jp.vuejs.org/v2/api/#vm-refs)


### 子コンポーネント「絵文字ボタン・コンポーネント」を修正
絵文字ボタン・コンポーネントで　`clear` イベントの通知を受けます。

**`/components/EmojiButton.vue`** (該当箇所のみ抜粋)
```html
<script lang="ts">
import Vue from 'vue'

export default Vue.extend({
  created: function() {
    this.$on('clear', () => {
      this.counter = 0;
    })
  },
})
</script>
```

Vue コンポーネントのライフサイクル `created` で、`$on` を使い、イベント受け取りの登録をしておきます。今回は、イベント名 `clear` で引数無しです。イベントを受けたらコンポーネント内の `counter` をリセットします。これにより、各絵文字ボタン・コンポーネントのカウンターがクリアできます。

**参考情報**
- [ライフサイクルダイアグラム — Vue インスタンス — Vue.js](https://jp.vuejs.org/v2/guide/instance.html#%E3%83%A9%E3%82%A4%E3%83%95%E3%82%B5%E3%82%A4%E3%82%AF%E3%83%AB%E3%83%80%E3%82%A4%E3%82%A2%E3%82%B0%E3%83%A9%E3%83%A0)
- [vm.$on( event, callback ) — API — Vue.js](https://jp.vuejs.org/v2/api/#vm-on)


## ソースコード
今回作成した部分までのソースを GitHub へアップしました。(Tag: `0.0.5`)
<div class="iframely-embed"><div class="iframely-responsive" style="padding-bottom: 50%; padding-top: 120px;"><a href="https://github.com/riotz-works/samples-pwa-base-app/tree/0.0.5" data-iframely-url="//cdn.iframe.ly/q0auNiW"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>

GitHub Pages にホスティングもしました。動作している状態を確認する場合は、こちらへアクセスしてください。
(公開サイトは１つのため記事公開に合わせて変わり、本記事の内容とは異なることがあります)
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://riotz.works/samples-pwa-base-app/" data-iframely-url="//cdn.iframe.ly/VPvSEzZ"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>



----

似たようなコードをコンポーネント化し再利用できるようにしました。Vue コンポーネントを使うことで、画面内のコードもスッキリさせることができます。(同じようなものは集約してプログラムで処理したいところですよね、0.0.4 までのソースはサンプル用とはいえ心苦しかった)

一方でコンポーネント間の連携は意外と難しいところがあります。まずは親子間のイベント通知が基本です。

今回のサンプルは、スタータープロジェクトにボタンを付けたり、ロゴを回したりと地味な感じではありますが、２種類の子コンポーネント間を親コンポーネントがつなぐ形となっています。まさにコンポーネント間連携の基本なので、実装を確認いただければと思います。

[Clear] ボタンが子コンポーネントになると、兄弟間の連携となり、また一歩踏み込むことになりますが次の機会に！
