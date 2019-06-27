---
title: TypeScript プロジェクト用サンドボックスで簡単コード検証
permalink: easy-poc-with-sandbox-for-typescript-project
alias: /2019/05/17/easy-poc-with-sandbox-for-typescript-project/index.html
date: 2019-05-17
author: lulzneko
categories: 開発
tags:
- TypeScript
- Lint
- AWS Lambda
---

TypeScript を使うと JavaScript のコードに型を導入することができ、コードの安全性や生産性を高めることができます。いっぽうで TypeScript は JavaScript にコンパイルしてから実行する必要があり、ちょっとしたコードの確認をするにしても一手間かかります。
そこで検証を簡単にするため、プロジェクト内のファイル変更を監視し、ファイルの変更があったら自動的に再コンパイル＆実行してくれる環境を作ります。

![](/articles/assets/lulzneko/develop/develop.jpg)


TypeScript では、ちょっとしたコードを試したいときでも `tsc` でコンパイルして `node` で実行してと、コンソールでの作業が必要となります。コードの動きを試したかったり、ネットで見つけたおもしろいコードを動かしたいといっただけでも手間がかかります。
今回は TypeScript 用のサンドボックス・プロジェクトを用意して、手軽にコードを試せるようにします。(サンドボックスというとセキュリティモデル的な感じもしますが、今回はお砂場遊び的な感覚。最近はプレイグラウンドと言う？)

**環境**
本記事の開発環境は以下となります。
- Windows 10 64bit + WSL Ubuntu 18.04.1 LTS
- Visual Studio Code
- Node.js 12.2.0
- Yarn 1.15.2
- TypeScript 3.4.5


## TypeScript プロジェクトの作成
いろいろなモジュールが組み合わさるので、順を追って作っていきます。
まずは基本となる TypeScript プロジェクトを作成します。

プロジェクトのディレクトリを作成し `/package.json` ファイルを作ります。(今回は `sandbox` という名前のディレクトリ)
- `devDependencies` は、常に最新版のモジュールを使いたいためにバージョンは `"*"` としています。
- `scripts: clean` は、最新のモジュールを使いやすいように `node_modules` などを削除します。OS に依存しないよう `npx rimraf` で削除しています。
```json
{
  "name": "sandbox",
  "private": true,
  "dependencies": {},
  "devDependencies": {
    "@types/node": "*",
    "typescript": "*"
  },
  "scripts": {
    "clean": "npx rimraf yarn.lock node_modules dist",
    "setup": "yarn clean && yarn install --ignore-optional"
  }
}
```

同じくプロジェクト直下に `tsconfig.json` を作ります。
こちらは普段使う TypeScript の設定に合わせておくとよいでしょう。今回は `esnext` で最新仕様を使い、`"strict": true` でチェックを厳しくしています。
```javascript
{
  "compilerOptions": {

    /* Basic Options */
    "target": "esnext",                       /* Specify ECMAScript target version: 'ES3' (default), 'ES5', 'ES2015', 'ES2016', 'ES2017','ES2018' or 'ESNEXT'. */
    "module": "esnext",                       /* Specify module code generation: 'none', 'commonjs', 'amd', 'system', 'umd', 'es2015', or 'ESNext'. */
    "outDir": "./dist",                       /* Redirect output structure to the directory. */
    "removeComments": true,                   /* Do not emit comments to output. */

    /* Strict Type-Checking Options */
    "strict": true,                           /* Enable all strict type-checking options. */
    "noUnusedLocals": true,                   /* Report errors on unused locals. */
    "noUnusedParameters": true,               /* Report errors on unused parameters. */
    "noImplicitReturns": true,                /* Report error when not all code paths in function return a value. */
    "noFallthroughCasesInSwitch": true,       /* Report errors for fallthrough cases in switch statement. */

    /* Module Resolution Options */
    "moduleResolution": "node",               /* Specify module resolution strategy: 'node' (Node.js) or 'classic' (TypeScript pre-1.6). */
    "baseUrl": "./",                          /* Base directory to resolve non-absolute module names. */
    "paths": { "~/*": [ "./src/*" ] },        /* A series of entries which re-map imports to lookup locations relative to the 'baseUrl'. */
    "esModuleInterop": true,                  /* Enables emit interoperability between CommonJS and ES Modules via creation of namespace objects for all imports. Implies 'allowSyntheticDefaultImports'. */

    /* Experimental Options */
    "experimentalDecorators": true,           /* Enables experimental support for ES7 decorators. */
    "emitDecoratorMetadata": true,            /* Enables experimental support for emitting type metadata for decorators. */

    /* Additional Options */
    "forceConsistentCasingInFileNames": true, /* Disallow inconsistently-cased references to the same file. */
    "newLine": "LF",                          /* Use the specified end of line sequence to be used when emitting files: "crlf" (windows) or "lf" (unix). */
    "resolveJsonModule": true,                /* Allows for importing, extracting types from and generating .json files. */

    /* Specify library files to be included in the compilation. */
    "lib": [
      "esnext"
    ]
  },
  "include": [ "./src/**/*.ts", "./test/**/*.ts" ]
}
```

これで `/src` ディレクトリ以下の TypeScript ファイルをコンパイルして実行する環境ができました。
`/src/index.ts`
```typescript
console.debug('Hello World !');
```

コンパイルして実行。
```console
$ yarn tsc -p .
$ node dist/index.js
Hello World !
```


## ts-node で TypeScript を直接実行
コンパイルの手間をなくして、TypeScript をコンソールから直接実行できるようにします。
```console
$ yarn add -D ts-node@* tsconfig-paths@*
```

`/package.json` に以下の `start` スクリプトを追加します。(`scripts` のみ抜粋)
- `ts-node` で `src/index.ts` を実行。(以降、プログラム実行の起点は `index.ts` になります)
- TypeScript の alias path を `ts-node` で使えるように `-r tsconfig-paths/register` を登録します。
```json
{
  "scripts": {
    "clean": "npx rimraf yarn.lock node_modules dist",
    "setup": "yarn clean && yarn install --ignore-optional",
    "start": "ts-node -r tsconfig-paths/register src/index.ts"
  }
}
```

実行！新たに１行追加しましたが `tsc` なしで動作します。また先ほど作られた `dist` ディレクトリは削除して大丈夫です。
`/src/index.ts`
```typescript
console.debug('Hello World !');
console.debug('Hello TypeScript !!');
```

```console
$ yarn start
Hello World !
Hello TypeScript !!
```


## nodemon でファイルの変更を検知して TypeScript を直接実行
さらにファイルの変更を検出して、自動的に `ts-node` を実行できるようにします。
```console
$ yarn add -D nodemon@*
```

`/package.json` の `start` スクリプトを修正します。(`scripts` のみ抜粋)
- `nodemon -w src` で、`nodemon` から起動し `src` ディレクトリを監視します
- `-x ts-node` で、`nodemon` から実行するプログラム `ts-node` を指定しています
```json
{
  "scripts": {
    "clean": "npx rimraf yarn.lock node_modules dist",
    "setup": "yarn clean && yarn install --ignore-optional",
    "start": "nodemon -w src -x ts-node -r tsconfig-paths/register src/index.ts"
  }
}
```

実行！今回は `yarn start` から先に始めます。
先ほどまでの Hello World, TypeScript がコンソールに表示され、その後に `nodemon` が変更待ちをしている状態になります。
```console
$ yarn start
...(省略)
Hello World !
Hello TypeScript !!
[nodemon] clean exit - waiting for changes before restart
```

ここで `/src/index.ts` を変更し、保存します。
```typescript
console.debug('Hello World !');
console.debug('Hello TypeScript !!');
console.debug('Hello Sandbox Project !!!');
```

するとファイルの変更を検知して、コンソールに新しい結果が自動的に出力されます。
```console
...(省略)
[nodemon] restarting due to changes...
[nodemon] starting `ts-node -r tsconfig-paths/register -r dotenv/config src/index.ts`
Hello World !
Hello TypeScript !!
Hello Sandbox Project !!!
```


## コードの検証で利用
実際にコードの検証で使ってみます。

最近気になったのが、こちら [ソート可能なUUID互換のulidが便利そう - Qiita](https://qiita.com/kai_kou/items/b4ac2d316920e08ac75a) の記事。
よく UUID v4 でランダムな ID 発行を使っています。それに対してソート可能なランダムな ID 発行できるというのは興味深いです。記事は Python ですが Node.js/TypeScript にも [ulid/javascript](https://github.com/ulid/javascript) モジュールがあります。

モジュールを導入し、サンドボックス・プロジェクトを起動しておきます。
```console
$ yarn add uuid ulid
$ yarn add -D @types/uuid
$ yarn start
```

単なるコンソール出力ですがコードを書いて保存するだけで結果が見れます。
```typescript
import { ulid } from 'ulid';
import * as uuid from 'uuid';

console.debug(`ULID: ${ ulid() }`);
console.debug(`UUID: ${ uuid.v4() }`);
```

ランダムな値を生成するので保存するたびに変わるのがわかります。
"また UUID との 128 ビット互換性" ですが、表現方法は異なるようです。ソートの必要があるかはシステム次第ですが、文字数が詰まるのは良さそうです。また TypeScript 対応されているのも Good！
```console
ULID: 01DB2S8RCY1EM7XBJG6P6A2KFE
UUID: 67d6e72b-97d9-4a65-ac92-35c476e02f7b
```


もうひとつ、こちら [日付時刻操作ライブラリをmomentからdayjsへ乗り換えた - Qiita](https://qiita.com/yagi_suke/items/2848c8981ea6d9f26587) の記事。

普段、日時を扱う場合には Moment.js を使っていますが、もっと軽量の [iamkun/dayjs](https://github.com/iamkun/dayjs) があるとのこと。
サーバサイドの開発では軽量に越したことはありませんがシビアに考えることは少ないですが、フロントでは大事なポイントです。

さっそくモジュールを導入し、サンドボックス・プロジェクトを起動しておきます。
```console
$ yarn add dayjs moment
$ yarn start
```

ここではコードをまとめて書いていますが、保存しながら書き足している感じで作業しています。コーディングしながら `Ctrl + S` して、コンソールが流れている間に次のコードといった感じです。
デフォルトのタイムゾーンとフォーマットが異なるものの、通常は ISO 文字列で使ったり、フォーマットのテンプレートを指定したりするので問題はないでしょう。2KB の軽さは魅力的です。
```typescript
import dayjs from 'dayjs';
import moment from 'moment';

console.debug(`Day.js:    ${ dayjs() }`);
console.debug(`Moment.js: ${ moment() }`);

console.debug(`Day.js:    ${ dayjs().unix() }`);
console.debug(`Moment.js: ${ moment().unix() }`);

console.debug(`Day.js:    ${ dayjs().toISOString() }`);
console.debug(`Moment.js: ${ moment().toISOString() }`);
```

徐々にコンソール出力が増えていくのを確認しながら、動きの差異などを確認しています。
```console
Day.js:    Fri, 17 May 2019 13:52:02 GMT
Moment.js: Fri May 17 2019 22:52:02 GMT+0900
Day.js:    1558101122
Moment.js: 1558101122
Day.js:    2019-05-17T13:52:02.637Z
Moment.js: 2019-05-17T13:52:02.637Z
```



----

以上、ちょっとしたコードの検証に使えるサンドボックス、お砂場プロジェクトでした。

このようなプロジェクトを１つ作っておくと、TypeScript で型を調べたり、新しいライブラリの使い方を確認したりといった際に便利です。

nodemon まで入ったところで Git にコミットしておくと、散らかってもクリーンな環境へ簡単に戻せます。
私は AWS Lambda で環境変数を使うことが多いので、さらに [motdotla/dotenv](https://github.com/motdotla/dotenv) も追加しています。また、TSLint もかけています。また Strict なコーディングの確認や練習のために [TSLint](https://palantir.github.io/tslint/) も設定しています。（ESLint へ引っ越さないと💦）
ソースはこちら [lulzneko/sandbox](https://github.com/lulzneko/sandbox)
