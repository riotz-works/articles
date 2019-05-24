---
title: Validate TypeScript による入力データ検証を試す
permalink: trial-input-data-validation-with-validate-typescript
date: 2019-03-22
author: lulzneko
categories: 開発
tags:
- TypeScript
---

Web API で リクエスト パラメーターなりボディでクライアントからデータを受け取るケースがあります。
その際にリクエストを受け付けてよいか入力データの検証（入力チェックとか呼んだりも）しますが、最近 Validate TypeScript というライブラリを試してみたので ご紹介。

![](/articles/assets/lulzneko/develop/validate.jpg)


**環境**
本記事の開発環境は以下となります。
- Windows 10 64bit + WSL Ubuntu 18.04.1 LTS
- Visual Studio Code
- Node.js 8.10.0
- Yarn 1.13.0
- TypeScript 3.3.4000
- validate-typescript 4.0.1


## Validate TypeScript 概要
[Validate Typescript](https://github.com/Grant-Zietsman/validate-typescript) - https://github.com/Grant-Zietsman/validate-typescript はスキーマベースのバリデーターで、JSON などのオブジェクトの値や型をチェックすることができる Node.js のライブラリです。[MIT ライセンス](https://github.com/Grant-Zietsman/validate-typescript/blob/master/LICENSE)の元 OSS で公開されています。
豊富な検証ルールをあらかじめ備えているほか、自分で拡張した検証ルールを使うこともできます。
![](/articles/assets/lulzneko/develop/validate-typescript/01.png)

Web API をはじめ、ウェブでリクエストを受け取る機能を作った場合に、クライアントからリクエストしてきた内容を受け入れてよいかチェックする必要があります。
たとえばメールのアドレスは半角の英数字から始まり`@`が間にあって、ドメイン名のルールが(実際にはもっと複雑)などと形式があっているかチェックする必要があります。他にもドロップダウンから選択したデータが選択できる範囲の値であるか(不正データを送ってきてないか)や、必須入力ではないけど送ってきたら数値である必要がある、などと単純な型チェックだけでもいろいろとやることがあります。

[Validate Typescript](https://github.com/Grant-Zietsman/validate-typescript) は、その「型チェック」を支援してくるライブラリになります。(逆に言うとメールアドレスが存在するかのチェックや、DB に存在する値との整合性、入力データ間の整合性などはしてくれません。こちらはこちらで必要なことは別途実装する必要があります。)

その「型チェック」ですが色々なやり方があります。各リクエストパラメーターごとにチェックの関数を呼び出したり、リクエストパラメーターに対応するクラスを定義してデコレーター(アノテーション)を付けたり。ｚ
今回 [Validate Typescript](https://github.com/Grant-Zietsman/validate-typescript) に着目したのは "スキーマベース" という点で、スキーマをコード内のオブジェクトで渡せるところが良いと思い使ってみることにしました。

こちらのライブラリを使おうとしたところ、インストールエラーが出てしまってプルリクを出して直してもらったことがありました。詳しくは「[Validate Typescript に インストールエラーの修正についてのプルリクを送る](https://riotz.works/articles/2019/03/13/pull-request-to-validate-typescript-about-installation-errors/)」の記事になります。こうしてプルリクで改修や機能の提案できるのが OSS のよいところですね。


## Validate TypeScript のインストールとサンプルコード
インストールは Node.js プロジェクトで NPM パッケージとして導入します。
`yarn add validate-typescript`
(npm の場合は `npm install validate-typescript`)

公式の[サンプルコード](https://www.npmjs.com/package/validate-typescript#example) 抜粋（一部修正）
```typescript
import { Alias, Email, ID, Optional, Options, RegEx, Type, validate } from 'validate-typescript';
import { AssertionError, ValidatorError } from 'validate-typescript/lib/errors';

const zaPhoneNumber = (): string => Alias(RegEx(/^((\+27|0)\d{9})$/), zaPhoneNumber.name);

class CustomMessage {
  public message!: string;
}

const schema = {
  id: ID(),
  children: [ID()],
  username: Type(String),
  email: Email(),
  gmail: RegEx(/.+@gmail.com/),
  phone: zaPhoneNumber(),
  gender: Options(['m', 'f', 'o']),
  married: Type(Boolean),
  names: {
    first: Type(String),
    middle: Optional(Type(String)),
    last: Type(String)
  },
  message: Type(CustomMessage)
}

const data = {
  id: '17s',
  children: [1, 2, '3s'],
  username: 'solomon',
  email: 'solomon@validate-typescript.com',
  gmail: 'solomon@gmail.com',
  phone: '+27824392186',
  gender: 'm',
  married: true,
  names: {
    first: 'Solomon',
    last: 1
  },
  message: Object.assign(new CustomMessage(), { message: 'Sawubona Mhlaba' })
};

try {
  const input = validate(schema, data);
  console.debug(input); // no validation error
} catch (error) {
  console.debug(JSON.stringify(error)); // validation error

  const violations: ValidatorError[] = [];
  JSON.parse(JSON.stringify(error), (key: string, values: ValidatorError[]) => {
    if (key === 'child_errors') {
      Array.prototype.push.apply(violations, values.filter((value: ValidatorError) => value.property ? value : undefined));
    }
    return values;
  });

  for (const violation of violations) {
    console.debug('%s=%s %s', violation.property, violation.value, (violation.child_error as AssertionError).details);
  }
}
```

前半の `zaPhoneNumber` はカスタムの入力検証で南アフリカ共和国の `+27` または `0` 始まりの電話番号チェックです。
`CustomMessage` はデータ構造の一部をクラスとして表現し、オブジェクトリテラルとの複合を試している感じでしょうか。

`const schema` が本題の入力検証でチェックするデータスキーマです。
`データとして受けとるキー: 入力検証のルール` という形式で記述します。
たとえば `id: ID()` は入力データ `id` というキーに `ID()` の形式(実態は数値)になっているかをチェックするといった具合です。
`Type(String)` のような単純な型チェックや、`Email()` などの組み込み型、`RegEx()` による正規表現のチェックなどがあります。
また必須ではないが受け取った場合はチェックするの `Optional()` に、来た場合のチェックを引数にルールを入れる。
具体的な値の選択肢である `Options()` などもあります。
ひととおりのチェックルールはまかなえています。

サンプルでは `const data` で入力データを記述していますが、実際には Web API などを作っているフレームワークなりの入力を受けて、` validate(schema, data);` を実行します。
入力検証が通ると `value` が戻り値で返ってきますが、問題がある場合はエラーがスローされてくるので呼び出すだけでもよいかもしれません。

問題があった場合のエラーの受け取りですが、ValidatorError と AssertionError の再帰構造となっているようです。
入力データの構造が深くなると、それに合わせて `child_errors` という形でエラーも深くなるようになっています。
必要な部分だけを抜粋すると以下のような構造になります。下記は `id` の値が数値でなかった場合のエラーです。
```json
{
  "message": "Validate TypeScript",
  "validator": "ID",
  "property": ".id",
  "value": "17-error",
  "child_error": {
    "message": "Validate TypeScript",
    "value": "17-error",
    "converter": "toInt",
    "details": "could not be converted to an integer"
  }
}
```

`property` と `value` で、キーと受け取った値がわかります。(キーの先頭にドットがついてますが)
`child_error` の `details` にエラーメッセージが入っています。英語ですが、そのまま使えそうな感じです。

ただしデータの階層が深い場合は、このデータ構造が `child_errors` という形で深く連なっていくので、そのままでは扱いにくいです。
そのためサンプルに加えて以下のようなコードを追加してフラットに変換しています。
```typescript
const violations: ValidatorError[] = [];
JSON.parse(JSON.stringify(error), (key: string, values: ValidatorError[]) => {
  if (key === 'child_errors') {
    Array.prototype.push.apply(violations, values.filter((value: ValidatorError) => value.property ? value : undefined));
  }
  return values;
});
```

JSON をパースする際に [reviver 関数](https://developer.mozilla.org/ja/docs/Web/JavaScript/Reference/Global_Objects/JSON/parse#Using_the_reviver_parameter) を使って `child_errors` から `property` が存在する場合に、その値を取得して配列で保持するようにしています。
[reviver 関数](https://developer.mozilla.org/ja/docs/Web/JavaScript/Reference/Global_Objects/JSON/parse#Using_the_reviver_parameter) は JSON をパースする際に値を変換することができるのですが、再帰構造から必要なものだけをうまく引き出せなかったので、横から取り出すようにしました。
これで具体的な入力検証エラーの部分だけを取り出せるたので、必要に応じてレスポンスするためのオブジェクトに詰めなおすことができます。


----

シンプルなスキーマで、その場に書けるのでコーディングがしやすくきになるらいぶらりなのですが、入力検証エラー時の構造体がちょっと扱いにくいのと、エラーの型定義がサブモジュールのインポートになってしまうため、TSLintを厳しくしていると怒られるという点があり気になりました。

入力検証は似て非なるデータ構造のチェックになり、クラスのデコレータベースは使いにくいと思っているので、スキーマベースという点が気に入っているのですが、エラーのデータ構造がちょっと気になります。
とくに同じキー `child_errors` に入っていて `property` があるかないかで、末端のエラーが情報か、中間のコンテナーかを判別しないといけないのが、あとからコードを見た時に何をしたいのか分かりにくいだろうなぁと。

もう少し使いやすいものを探してみつつ、時間切れとなったら [Validate Typescript](https://github.com/Grant-Zietsman/validate-typescript) を使わせてもらおうかな。
