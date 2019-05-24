---
title: Nuxt.js PWA(Progressive Web Apps) のベースアプリをTypeScript対応する
permalink: typescripting-base-app-of-nuxtjs-pwa
date: 2019-05-10
author: lulzneko
categories: フロントエンド
tags:
- TypeScript
- Nuxt.js
- PWA
- JAMStack
---

Nuxt.js を使うことで簡単に PWA なアプリを作ることができます。そのアプリを作る際に TypeScript を使うとコードの品質を高めることができ、また複数人やチームでの開発生産性を向上させることができます。今回は Nuxt.js で作ったアプリに TypeScript を導入する方法を紹介します。

![](/articles/assets/lulzneko/develop/nuxtjs/nuxtjs.png)


前回 [Nuxt.js で PWA(Progressive Web Apps) のベースアプリを作る](https://riotz.works/articles/2019/05/09/develop-base-app-for-pwa-with-nuxtjs/) で、[Nuxt.js](https://ja.nuxtjs.org/) を使った [PWA](https://developer.mozilla.org/ja/docs/Web/Progressive_web_apps) のベースアプリを作りました。そのまま JavaScript で開発を進めることができますが、TypeScript を導入することで変数や関数に型定義が行えるようになり開発生産性や保守性を向上できます。今回のベースアプリはゼロから作っているので最初から入れてしまうのがよいでしょう。また TypeScript の関連モジュールを導入したとしても、必ずしも TypeScript の利用が必須ではなく、JavaScript で書いていって、TypeScript にできるところから適用していく段階的な利用も可能です。そのような観点からも入れてしまうのがよいでしょう。

**シリーズの記事**
- [Nuxt.js で PWA(Progressive Web Apps) のベースアプリを作る](https://riotz.works/articles/2019/05/09/develop-base-app-for-pwa-with-nuxtjs/)
- **Nuxt.js PWA(Progressive Web Apps) のベースアプリをTypeScript対応する** (本記事)

**環境**
本記事の開発環境は以下となります。
- Windows 10 64bit + WSL Ubuntu 18.04.1 LTS
- Visual Studio Code
- Node.js 12.1.0
- Yarn 1.15.2
- Nuxt.js 2.6.3


## TypeScript とは
[TypeScript](https://www.typescriptlang.org/) は JavaScript に型とクラスを導入する AltJS のプログラミング言語で、JavaScript のスーパーセットです。クラスは ES6 で導入されたので TypeScript 固有ではなくなりましたが、やはり「型」の導入が TypeScript のメリットといえるでしょう。

型の例で、たとえば JavaScript で以下のようなコードがあったとします。
単純な足し算を想定したコードですが、型がないので引数に任意の値を投入できます。その結果、戻り値が想定外になることもあります。
```javascript
const add = (a, b) => {
  return a + b;
}

console.log(add(1, 2));               // 3
console.log(add(1, "2"));             // 12
console.log(add(1, [ 2 ]));           // 12
console.log(add(1, { value: "2" }));  // 1[object Object]
```


TypeScript では以下のようになります。
引数と戻り値に `:number` で、数値型を明示します。それにより数値でない引数を渡している部分はコンパイルエラーとなり実行コード(JavaScript)が生成できません。
```typescript
const add = (a: number, b: number): number => {
  return a + b;
}

console.log(add(1, 2));
console.log(add(1, "2"));             // error TS2345: Argument of type '"2"' is not assignable to parameter of type 'number'.
console.log(add(1, [ 2 ]));           // error TS2345: Argument of type 'number[]' is not assignable to parameter of type 'number'.
console.log(add(1, { value: "2" }));  // error TS2345: Argument of type '{ value: string; }' is not assignable to parameter of type 'number'.
```

基本的には型定義が入っただけで書いていることに変わりはありません。またコンパイラや Lint の設定で、制約を強くしたり、甘くしたりできます。最初から使う場合はある程度強くできるでしょうし、途中から導入したり慣れていない場合などは徐々に強くしていくこともでき、状況に合わせて使えます。

このぐらいの関数でしたら型がなくてもとくに問題ないかもしれませんが、もっと大きなものや他の人が書いたコードなどになってくると話が異なります。関数の引数が何を期待しているのか、戻り値は何が返ってくるのか、型情報があるだけでだいぶ変わってきます。

そんな TypeScript ですが、最近はとくに注目されており Meetup イベントが東西で開催されます！抽選枠で、まだ応募可能です。
TypeScript Meetup #1 にエントリーしましたが、案内メールが来て一瞬で定数枠の 60 を超え、本記事執筆時点で 500 人を超えてます。スゴイ勢い！
- [TypeScript Meetup #1 - connpass](https://typescript-jp.connpass.com/event/127747/)
- [kansai.ts #1 - connpass](https://kansaits.connpass.com/event/130120/)

Riotz.works は、TypeScript で開発するチームなのですが、実はずっと Java で開発をしてきたチームでした。あるトラブルがあって TypeScript に舵を切り、以来 TypeScript で開発するチームとなったのですが、その際の発表資料がこちらになります。もしよかったらご参照ください。(いつかの Meetup の機会でお話しできるといいな)
<div class="slide"><iframe src="https://riotz.works/slides/?2017-serverless-conf"></iframe></div>


## Nuxt.js のプロジェクトに TypeScript を導入
前回作成 [Nuxt.js で PWA(Progressive Web Apps) のベースアプリ]() に TypeScript を導入します。

### TypeScript サポートのモジュールを追加
まずは TypeScript サポートのモジュールを追加します。プロジェクトのディレクトリで下記コマンドを実行します。
```console
$ yarn add -D @nuxt/typescript ts-node
```
※ npm の場合、`npm i -D @nuxt/typescript ts-node`


### TypeScript の設定ファイルを生成
TypeScript の設定ファイル `tsconfig.json` を作成します。
※ この [feat(ts): auto generate tsconfig.json by kevinmarrec · Pull Request #4776 · nuxt/nuxt.js](https://github.com/nuxt/nuxt.js/pull/4776) プルリクがリリースされると `touch` しなくても済みそうですが、2019年5月現在、まだ事前に作成が必要です。
```console
$ touch tsconfig.json
```

Nuxt.js 開発サーバーを起動し、tsconfig.json の定義を生成します。起動情報に `TypeScript support is enabled` が出ていることがわかります。
```console
$ yarn dev
   ╭──────────────────────────────────────────╮
   │                                          │
   │   Nuxt.js v2.6.3                         │
   │   Running in development mode (spa)      │
   │   TypeScript support is enabled          │
   │                                          │
   │   Listening on: http://localhost:3000/   │
   │                                          │
   ╰──────────────────────────────────────────╯
```

`tsconfig.json` の内容が更新されます。JSON ファイルをモジュールとして扱える定義 `resolveJsonModule` を追加します。これは追加したほうが便利なのと、後述の `nuxt.config.ts` で `package.json` を扱えるようにするためです。
```javascript
{
  "compilerOptions": {
    // ...(省略)
    "resolveJsonModule": true,
  }
}
```

この定義は追加するプルリクと削除するプルリクが流れているようで、日付的には消すほうが最後なので自分で追加が必要そうです。
"Remove resolveJsonModule as , after little thoughts, we should not force it to users - [#4842](https://github.com/nuxt/nuxt.js/pull/4842)" のだそうで、フレームワークのポリシーや考え方ですね。(なぜなのかは知りたかった)
- [feat(ts): auto generate tsconfig.json by kevinmarrec · Pull Request #4776 · nuxt/nuxt.js](https://github.com/nuxt/nuxt.js/pull/4776/commits/b0f475b6fca6e31e49a8674811719a44c045387f#diff-86a77182c8558eb2dc1eb2fc2fcef9c6R11)
- [fix(ts): fix default `tsconfig.json` by kevinmarrec · Pull Request #4842 · nuxt/nuxt.js](https://github.com/nuxt/nuxt.js/pull/4842/files#diff-96d6626cb8a1282050f1ed2f1163f3d6L11)


### Nuxt.js の設定ファイルを TypeScript 化
続いて設定を TypeScript で書くため `nuxt.config.js` ファイルを `nuxt.config.ts` にリネームします。
ファイルの内容、先頭と最後を以下のように変更します。とくに `'./package.json'` の `.json` をつけ忘れないように注意します。この `package.json` を読み込むのに先ほどの `resolveJsonModule` が必要でした。

**変更前**
```typescript
import pkg from './package'

export default {
  // ...(省略)
}
```

**変更後**
```typescript
import NuxtConfiguration from '@nuxt/config'
import pkg from './package.json';

const config: NuxtConfiguration = {
  // ...(省略)
}

export default config
```

これにより設定ファイル内で `Ctrl + Space` などの入力補完が使えるようになります。


## ページやコンポーネントを TypeScript 化
プロジェクトが TypeScript 対応したので、ページやコンポーネントを TypeScript 化します。

今回はスクリプトベースで実装します。基本的な構文は下記です。
- `<script>` タグに `lang="ts"` を追加して TypeScript として認識させます
- 全体に型定義を効かせるために `Vue.extend` を追加します
- `</script>` タグ前の括弧閉じに ')' を追加するのを忘れないよう注意します
```html
<script lang="ts">
import Vue from 'vue'

export default Vue.extend({
})
</script>
```

ベースアプリの `/pages/index.vue` は以下のようになります。
```html
<script lang="ts">
import Vue from 'vue'
import Logo from '~/components/Logo.vue'

export default Vue.extend({
  components: {
    Logo
  }
})
</script>
```

また、Visual Studio Code の Vue 拡張 [Vetur](https://marketplace.visualstudio.com/items?itemName=octref.vetur) を使っている場合 `/components/Logo.vue` に以下の空実装コードを追加します。
(`nuxt` コマンドはエラーではないので、Vetur を使わない場合は追加しなくても大丈夫です)
```html
<script lang="ts">
import Vue from 'vue'
export default Vue.extend({})
</script>
```

**Note**
公式ドキュメント [TypeScript サポート - Nuxt.js](https://ja.nuxtjs.org/guide/typescript/#%E3%82%B3%E3%83%B3%E3%83%9D%E3%83%BC%E3%83%8D%E3%83%B3%E3%83%88) では、今回のようなスクリプトベースではなくクラスベースの "[vue-property-decorator](https://github.com/kaorun343/vue-property-decorator) を利用することを強くお薦め - [公式サイト](https://ja.nuxtjs.org/guide/typescript/#%E3%82%B3%E3%83%B3%E3%83%9D%E3%83%BC%E3%83%8D%E3%83%B3%E3%83%88)" しています。
しかしながらクラス構文にすると、実装方法が Vue.js/Nuxt.js とまったく異なってしまいます。今回は、これまでの実装経験やサイト、書籍を活かすためにスクリプトベースとしました。まったくゼロから勉強するとのことでしたらクラス構文を使うのも手かもしれません。


## カウンターを設置して TypeScript の実装を確認
TypeScript を導入したので実装を確認したいと思います。
トップページにボタンを設置し、クリックした数をカウントする簡単なアクションを追加します。

### 絵文字ボタンとカウンターの導入
**`/pages/index.vue`** の [Documentation] と [GitHub] の下、`<div class="links"></div>` ブロックの直後に以下を追加します。
```html
      <div class="actions">
        <a @click="addTada" class="button--action">🎉 {{ tada }}</a>
        <a @click="addSparkles" class="button--action">✨ {{ sparkles }}</a>
        <a @click="addThumbsup" class="button--action">👍 {{ thumbsup }}</a>
        <a @click="addHeart" class="button--action">🧡 {{ heart }}</a>
        <a @click="clear" class="button--grey">Clear</a>
      </div>
```

カウンター表示用のデータプロパティを追加します。(`data` のみ抜粋)
```typescript
export default Vue.extend({
  // ...(省略)
  data() {
    return {
      tada: 0,
      sparkles: 0,
      thumbsup: 0,
      heart: 0
    }
  },
})
```

`@click` で呼び出されるメソッドを追加します。(`methods` のみ抜粋)
各絵文字ボタンから呼び出されるメソッドは対応する変数をインクリメント、[Clear] は全変数を `0` に初期化します。各メソッドは TypeScript の型定義を導入し、戻り値 `:void` と明示しています。
```typescript
export default Vue.extend({
  // ...(省略)
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
    },
    clear: function(): void {
      this.tada =  0
      this.sparkles = 0
      this.thumbsup = 0
      this.heart = 0
    }
  }
})
```

以上で、絵文字ボタンを押すと各ボタン横のカウンターが増える動作となります。
カウンターぐらいだと型のメリットが感じられませんが、たとえば `clear` の初期化で `"0"` のような文字列を入れるとコンパイルエラーとなります。しっかり TypeScript の型チェックが効いています。


### カウンターのトータル回数、ロゴを回すアニメーションの導入
カウンターの値を使ったアクションを作りたいので、各カウンターのトータルを計算し、その値の回数ロゴを回してみます。

**`/pages/index.vue`** に、すべての絵文字カウンターの総計を持つ算術プロパティを追加します。(`computed` のみ抜粋)
`counter()` メソッドを作り、戻り値は `number` 型、すべての絵文字カウンターの合計です。
```typescript
export default Vue.extend({
  // ...(省略)
  computed: {
    counter(): number {
      return this.tada + this.sparkles + this.thumbsup + this.heart
    }
  },
})
```

ロゴを回転させるために、ロゴのコンポーネントへカウンターの値を渡します。
HTML テンプレートの `<logo>` タグに `v-bind:counter="counter"` 属性を追加し `counter` の値をロゴのコンポーネントで使えるようにします。
```html
      <logo v-bind:counter="counter" />
```

**`/components/Logo.vue`** で、カウンターの値を受け取りロゴを回転する CSS を追加します。
ロゴ全体を回転させるため、現在の HTML テンプレートの実装部分を` <div :style="rotation">` でラップします。
回転の CSS は `style` 属性直接に、`rotation` プロパティ経由で設定します。
```html
<template>
  <div :style="rotation">
    <div class="VueToNuxtLogo">
      <div class="Triangle Triangle--two" />
      <div class="Triangle Triangle--one" />
      <div class="Triangle Triangle--three" />
      <div class="Triangle Triangle--four" />
    </div>
  </div>
</template>
```

後は `counter` の値を使って CSS を作るだけですが、CSS アニメーションを再実行できるようにするため、一手間かけています。(スクリプト全文)
- 親コンポーネント `/pages/index.vue` で絵文字ボタンを押されると、`props: counter` に新しい値がセットされます。それを `watch` で監視していて、変化があると `watch: counter` を呼び出します。
- `watch: counter` は、`data` プロパティにアニメーションの CSS をオブジェクトとして設定します。いったんアニメーション無し `{ animation: 'none' }` をセットし、10ミリ秒後に `{ animation: `rotate 1s linear 0s ${ value } forwards` }` をセットします。(アニメーション再実行のトリック)
- 最後に `computed: rotation` 経由で作られた CSS オブジェクトを HTML テンプレートへ渡します。
```typescript
export default Vue.extend({
  props: {
    counter: Number
  },
  data() {
    return {
      animation: {}
    }
  },
  computed: {
    rotation: function(): object {
      return this.animation
    }
  },
  watch: {
    counter: function(value: number): void {
      this.animation = { animation: 'none' }
      setTimeout(() => {
        this.animation = { animation: `rotate 1s linear 0s ${ value } forwards` }
      }, 10)
    }
  }
})
```

だいぶ TypeScript の型が登場しました。
`computed: rotation`  では、戻り値が `object` で CSS の文字列ではないことがわかります。
`watch: counter` では、引数が `number` 型です。(フレームワークからの呼出し＆テンプレートリテラルなので型明示のメリットはあまりないですが)

なお、CSS のアニメーション定義は下記です。
```css
@keyframes rotate {
  0% {
    transform: rotateY(0);
  }
  100% {
    transform: rotateY(360deg);
  }
}
```

このくらいの規模だと型定義によるメリットはまだまだありませんが、アニメーションのためのトリックとは言え色々と取り回ししているので、他人の書いたこんなコードが急に出てきたと思うと型情報ぐらいあると嬉しいのではないでしょうか。


### 動作確認！
`yarn dev` で開発サーバーを起動し、ブラウザで `http://localhost:3000/` へアクセスします。
![](/articles/assets/lulzneko/develop/nuxtjs/11.png)

※ 開発サーバーは常に立ち上げておいて大丈夫です。実際の開発に当たってはホットリロードを活用し、コードを保存するとブラウザが自動でリロードしてくれます。


## ソースコード
今回作成した部分までのソースを GitHub へアップしました。
https://github.com/riotz-works/samples-pwa-base-app/tree/0.0.2

GitHub Pages にホスティングもしました。
(公開サイトは１つのため記事公開に合わせて変わり、本記事の内容とは異なります)
https://riotz.works/samples-pwa-base-app/



----

ベースアプリを TypeScript 化できました。
今回のカウンターボタンぐらいだとコーディング量が増えるだけでメリットが感じられませんが、他の人が書いた共通ライブラリを使うときなどに型があることで助かることが増えてきます。

型を導入できたところで Lint も入れたいところですが、こちらの話はまた重いので別途書きたいと思います。
もし使たい場合は、公式ドキュメント [ESLint を使った Linting - TypeScript サポート - Nuxt.js](https://ja.nuxtjs.org/guide/typescript/#eslint-%E3%82%92%E4%BD%BF%E3%81%A3%E3%81%9F-linting) をご参照ください。
