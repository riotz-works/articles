---
title: TypeScript で複数のモジュールをまとめて export, import する
permalink: using-typescript-re-export-and-import-syntax-to-improve-module-arrangement
date: 2019-08-28
author: lopburny
categories: 開発
tags:
- TypeScript
- Node.js
---

TypeScript を使って Node.js のアプリを開発していると、だんだんモジュールの数が増えていき、export, import 文が長くなったり冗長な感じになったりします。また、プロジェクト全体の共通機能をまとめてモジュール化する仕組みを考えたりと、モジュールの構成をどうするかについては常々悩ましいところがあります。

![](/articles/assets/lopburny/img/lopburny_blog_9_thumbnail.jpg)

この記事では、モジュール構成のリファクタリングに使える `re-exports` とそれを import する方法について紹介します。元々は ES Modules の仕様なので、Interface など TypeScript の機能を除き JS でも同じく使える仕組みになります。

※ただし、ES Modules の構文（JS）をそのまま Node.js 上で実行する場合は mjs 拡張子の使用・実験フラグ付きの起動が必要になり、詳細については割愛させていただきます。

## export

例えば、以下データモデルの Class または Interface を定義して models ディレクトリ以下に配置します。  
index.ts を作成、それらをまとめて export しておきます。
<br>

- models/index.ts
```typescript
export * from './User'    // User.ts
export * from './Book'    // Book.ts
export * from './Contact' // Contact.ts

// default は使えないので注意
```

## import

必要なモジュールを以下のように適宜 importします。  

<br>

- １モジュールのみ import
```typescript
import { User } from './models'
```
<br>

- 複数のモジュールを import
```typescript
import { User, Book } from './models'

const user: User = { id: 'user_id' }
const book: Book = { id: 'book_id' }
```
<br>

- 別名をつけてまとめて import
```typescript
import * as Model from './models'

const user: Model.User = { id: 'user_id' }
const book: Model.Book = { id: 'book_id' }
const contact: Model.Contact = { id: 'contact_id' }
```


## 公式ドキュメント

公式ドキュメントでこの内容を確認したい場合は、下記をご参考ください。

<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://www.typescriptlang.org/docs/handbook/modules.html#re-exports" data-iframely-url="//cdn.iframe.ly/Kp3ho7j"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>

## まとめ

`export * from '{PATH}'` でまとめて export 、必要に応じて適宜 import することができます。

アプリロジックの機能またはレイヤーごとにモジュールをまとめたり、プロジェクト共通の Util クラスをまとめて外部モジュール化したりと、色んな場面で利用できると思いますので、一度試してみてはいかがでしょうか。

最後まで読んでいただきありがとうございました。
