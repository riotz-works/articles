---
title: TSLint の prefer-function-over-method ルールについて悩む
permalink: thinking-about-prefer-function-over-method-rule-of-tslint
date: 2019-03-28
author: lulzneko
categories: 開発
tags:
- TypeScript
- Lint
---

コーディングをしていく上で良いお作法でできるようにしたり、チーム開発でスタイルを合わせたりするために Lint 系 の ツールを 使います。今回 TypeScript で 開発をしていて、TSLint の ルールを厳しくしたところチェックに引っかかったところが出たのですが、ルールの意図を汲み取れず悩み中の備忘録。

![](/articles/assets/lulzneko/develop/lint/tslint.png)


**環境**
開発者の環境は以下となります。
- Windows 10 64bit + WSL Ubuntu 18.04.1 LTS
- Visual Studio Code
- Node.js 8.10.0
- Yarn 1.13.0
- TypeScript 3.3.4000
- TSLint 5.14.0


## TSLint 概要
[TSLint](https://palantir.github.io/tslint/) [はTypeScript](https://www.typescriptlang.org/) コード の 読みやすさ や 保守性を維持するための静的分析ツールです。
不具合を起こしやすいコードを指摘したり、コーディングスタイルをそろえるためのチェックなどをしてくれます。

[TSLint](https://palantir.github.io/tslint/) は [TypeScript](https://www.typescriptlang.org/) に 特化してチェックしてくれるのですが、最近では [TypeScript 開発チームのロードマップ](https://github.com/Microsoft/TypeScript/issues/29288) で [ESLint](https://eslint.org/)、JavaScript/ECMAScript 用の Lint を サポートしていくことが発表され、[ESLint](https://eslint.org/) からも [TypeScript](https://www.typescriptlang.org/) の [ESLint](https://eslint.org/) 対応への歓迎とチームメンバーが開始した [typescript-eslint プロジェクト](https://github.com/typescript-eslint/typescript-eslint) に ついての[記事が公開](https://eslint.org/blog/2019/01/future-typescript-eslint)されます。

今後は [ESLint](https://eslint.org/) を 使っていくことになるかと思いますが、安定するまでは [TSLint](https://palantir.github.io/tslint/) を 使っていくのかなと思います。
[TSLint](https://palantir.github.io/tslint/) プロジェクトでも、移行に関するイッシュー [Roadmap: TSLint -> ESLint · Issue #4534 · palantir/tslint](https://github.com/palantir/tslint/issues/4534) が上がっており、一時的にはオーバーラップする期間が発生して、一時的には両方のツールを使うことが推奨されています。

新しいプロジェクトの [typescript-eslint](https://github.com/typescript-eslint/typescript-eslint) を 使う方法については、[@typescript-eslint ことはじめ - teppeis blog](https://teppeis.hatenablog.com/entry/2019/02/typescript-eslint) さん が 詳しいです。


## prefer-function-over-method ルール と 発生した事象
今回悩みの種となったのが、[TSLint](https://palantir.github.io/tslint/) の [prefer-function-over-method](https://palantir.github.io/tslint/rules/prefer-function-over-method/) ルールになります。

TypeScript で クラスメソッドを作った時に `this` を使わなかった場合に `Class method does not use 'this'. Use a function instead.` という指摘をしてくれます。

以下のコード例では `ok` メソッドは `this.template` で `this` を 使っているので問題ありません。一方で `ng` メソッドは `this` を 使っていないので [prefer-function-over-method](https://palantir.github.io/tslint/rules/prefer-function-over-method/) ルール違反となります。
```typescript
export class Action {

  private readonly template = 'Hello %s !!';

  public ok(name: string): void {
    console.debug(this.template, name);
  }

  public ng(message: string): void {
    console.debug(message);
  }
}
```

まぁ、確かに `ng` メソッドのほうは内部状態を触っていないので static なりにしたほうがよさそうです。


## 悩み事
ここで気になって悩んでいるのが "static にすべき" ではなく、"function に すべき" と メッセージが出ている点です。

つまり、下記のような修正ではなく (必要箇所のみ抜粋)
```typescript
export class Action {
  public static ng(message: string): void {
    console.debug(message);
  }
}
```

このようにする (必要箇所のみ抜粋)
```typescript
export class Action {
  public ng = (message: string): void => {
    console.debug(message);
  }
}
```

ルールの名前も prefer function over method だから、メソッドより関数を優先して使いましょうということなんですよね。。。

クラスの中に関数を書いても文法上問題はないわけですが、どうして static ではなく関数なのかなと。せっかくなので理解しておきたいのですが、[prefer-function-over-method](https://palantir.github.io/tslint/rules/prefer-function-over-method/) は Rationale、論拠の項目が書かれてないのです。

たとえば [only-arrow-functions](https://palantir.github.io/tslint/rules/only-arrow-functions/) ルール「JavaScript の 伝統的な関数定義ではなく、アロー関数を使いましょう」ですが、以下のように書かれています。

> Rationale
> Traditional functions don’t bind lexical scope, which can lead to unexpected behavior when accessing ‘this’.

`this` に アクセスしたときに予期しない動作を引き起こす可能性があるから、といった説明がされています。
この文章だけだとわかりにくいかもしれませんが、これをもとに調べることもできます。

しかしながら、[prefer-function-over-method](https://palantir.github.io/tslint/rules/prefer-function-over-method/) は Rationale が 書かれていない以上、どのように扱うのが望ましいのかゼロから調べるしかないわけです。
- ルールに従い関数に修正する
- ルールの修正方針とは異なるが static にする (これでも指摘はなくなる)
- ルールがプロジェクトにマッチしないので無効化する


## ESLint では、どうなる？
この疑問をつぶやいたときに、お仕事チームの仲間が ESLint の情報で応えてくれました！（ありがとうございます)

[typescript-eslint/ROADMAP.md at master · typescript-eslint/typescript-eslint](https://github.com/typescript-eslint/typescript-eslint/blob/master/packages/eslint-plugin/ROADMAP.md) に TSLint と ESLint の 対応表があるとのことで、[prefer-function-over-method](https://palantir.github.io/tslint/rules/prefer-function-over-method/) は [class-methods-use-this](https://eslint.org/docs/rules/class-methods-use-this) に あたるとのこと。

では、ESLint の [class-methods-use-this](https://eslint.org/docs/rules/class-methods-use-this) は どうなっているかというと「static メソッドにすること」。
メソッドと関数が混在するよりも、メソッドに統一して static と 分けて実装するとのことで、わかりやすいです。

しかしながら、こちらも論拠は書かれてませんでした。
「関数を使う」という選択肢がなければ、そうだよねってことで static メソッドにするのですが、今回は関数が使えることを知ってしまったので「static と 関数 の どちらにすべきか」という疑問が生じてしまいました。困った。


## TypeScript の コンパイル後を確認する
Lint ルールに明確な論拠がないので、出力された JavaScript/ECMAScript を 見て考えたいと思います。
TypeScript の ソース。
```typescript
export class Action {

  private readonly template = 'Hello %s !!';

  public ok(name: string): void {
    console.debug(this.template, name);
  }

  public ng(message: string): void {
    console.debug(message);
  }

  public ngFunction = (message: string): void => {
    console.debug(message);
  }

  public static ngStatic(message: string): void {
    console.debug(message);
  }
}
```

コンパイルされた結果。
※ ターゲットはクラス構文が導入されていない ES5 にしています。
```javascript
"use strict";
Object.defineProperty(exports, "__esModule", { value: true });
var Action = (function () {
    function Action() {
        this.template = 'Hello %s !!';
        this.ngFunction = function (message) {
            console.debug(message);
        };
    }
    Action.prototype.ok = function (name) {
        console.debug(this.template, name);
    };
    Action.prototype.ng = function (message) {
        console.debug(message);
    };
    Action.ngStatic = function (message) {
        console.debug(message);
    };
    return Action;
}());
exports.Action = Action;
```

結果を見ると 指摘のあった `ng` メソッドは `Action.prototype.ng` に 関数として追加されています。
TSLint の [prefer-function-over-method](https://palantir.github.io/tslint/rules/prefer-function-over-method/) に 従って関数に修正した `ngFunction` は コンストラクタ内で `this.ngFunction` に 追加されています。
ESLint の [class-methods-use-this](https://eslint.org/docs/rules/class-methods-use-this) に 従って static にした `ngStatic` は `Action.ngStatic` に 追加されています。

こうしてみると以下の順が良さそうです。
- static にする
- メソッドのままにする (TSLint [prefer-function-over-method](https://palantir.github.io/tslint/rules/prefer-function-over-method/) を 無効化)
- 関数にする (TSLint [prefer-function-over-method](https://palantir.github.io/tslint/rules/prefer-function-over-method/) に従う)

static にすればクラスレベルで追加されるので効率が良いでしょう。
メソッドと関数の違いについてですが、関数にすると各インスタンスに関数が追加されています。それに対してメソッドは `prototype` に 追加しています。特に理由がなければ `prototype` に 追加したほうが良い(はずだったと記憶)。
この辺の裏付けができていないのと、クラウス構文が入ってからも変わりがないのかはわからないので、決定打とはならないですが説明の方針としては１つ線が引けそうです。


## 現在の状況
この記事を書いている時点での対応は以下としました。
- static に できる場合は、static メソッドにする
- static に できない場合は、ルールを無効化してメソッドにする

今回、継承して実装するメソッドが一部指摘されていました。
簡易フレームワーク的な作りになっていて、親クラスから決められた abstract メソッドを実装するようになっており、その部分が引数だけで `this` を使わないで完結できるような処理になったりもします。
この部分は `// tslint:disable-next-line: prefer-function-over-method` で ルールを無効化してメソッドのままとしました。


## 参考情報
- [TypeScript Roadmap: January - June 2019 · Issue #29288 · Microsoft/TypeScript](https://github.com/Microsoft/TypeScript/issues/29288)
- [The future of TypeScript on ESLint - ESLint - Pluggable JavaScript linter](https://eslint.org/blog/2019/01/future-typescript-eslint)
- [typescript-eslint/typescript-eslint: Monorepo for all the tooling which enables ESLint to support TypeScript](https://github.com/typescript-eslint/typescript-eslint)
- [@typescript-eslint ことはじめ - teppeis blog](https://teppeis.hatenablog.com/entry/2019/02/typescript-eslint)
- [typescript-eslint/ROADMAP.md at master · typescript-eslint/typescript-eslint](https://github.com/typescript-eslint/typescript-eslint/blob/master/packages/eslint-plugin/ROADMAP.md)

----

Lint 系 の ツールはコードに対して指針を与えるので、やはり論拠が欲しいところですね。
特にいくつかの選択肢が出てきてしまうと、いろいろと考えてしまいます。

今回は残念ながら TypeScript力 というか、JavaScript/ECMAScript力 が たりなくて明確な根拠を持って指針化できませんでしたが、考え方は整理できたように思います。
裏付けをとるのは、ちょっと難しいかな。。
