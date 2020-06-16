# Articles | Riotz.works

これは http://riotz.works/articles の ウェブサイトを生成するためのソースを管理するリポジトリです。  
デフォルトで設定している `source` ブランチがウェブサイトのソースで、 `master` ブランチは GitHub Pages で 公開しているウェブサイトそのものになります。  

## 概要
これは Riotz.works の 記事を公開する「Articles | Riotz.works」のウェブサイトのソース管理です。 Riotz.worksの内部で使用しているリポジトリです。  
ツールの利用の仕方などを参考にすることは可能ですが、Riotz.works の エンジニア以外の方が直接利用することは想定していません。  


## 必要な環境
- [Node.js](https://nodejs.org/) 8+
- [Hexo](https://hexo.io/) 3.4+
- [Git](https://git-scm.com/) 2.15+

## 使い方
### 前提条件
- Node.js が インストールされている
- Git が インストールされている

### 環境構築
- このリポジトリをクローンします
- クローンしたディレクトリ(以降、ソース・ディレクトリ)で `npm run setup` を 実行します

例えば以下のようなコマンドです
```console
git clone git@github.com:riotz-works/articles.git
cd articles
npm run setup
```


### 記事ファイルの作成
- ソース・ディレクトリで `hexo new "[Article Title]"` を 実行します
- `source/_posts/[Article Title].md` が 作られるので `source/_posts/[yourname]/[Article Title].md` へ 移動します

**注意**  
`source/_posts/[yourname]/` 以下は、あなたの管理領域のため自由に整理することが可能です。  
ファイル名は任意の名前に変更することができるので、管理しやすい命名にすることが可能です。  

例えば以下のようなコマンドです
```console
hexo new "Hello World !"
mv source/_posts/Hello-World.md source/_posts/[yourname]/
```


### 記事のライティング
- 作成した Markdown の ファイルを開きます
- 先頭の `---` に挟まれる部分は [Front-matter](https://hexo.io/docs/front-matter.html) と 呼ばれるメタ情報部分になり、記事に合わせて設定します
- Front-matter は ランダムの並び順で生成されるので必要に応じて並び替えます
- `date` は 日付だけで時間は省略可能です
- `categories` と `tags` は 既存のものを確認しながら設定します
- `categories` は 一般名詞を使い製品や技術の固有名詞は避けます
- `tags` は 固有名詞を使い、製品や技術の名称を正しく設定します
- Front-matter の 下は、Markdown および HTML で 任意の内容を記述することができるので、記事の内容を作成します  
基本的には Markdown で 記述し、どうしても難しい部分は HTML で 補うようにします
- 画像などのファイルは `source/assets/[username]` 以下に配置します  
`href` などのパスは `/articles/assets/[username]` 以下になり、先頭に `/articles` が 必要です
- OGP に 対応しているため Twiiter等に URL を 投稿すると、記事の最初の画像 または サイトのトップ・ロゴが表示されます  
記事の最小のほうにアイキャッチ画像を使うとよいでしょう  

**注意**  
Front-matter の `author` は `_config.yml` の `authors` 配下に設定した自分の author 文字列と合わせます。  
`hexo new` の テンプレートは `scaffolds/post.md` なのですが、あまり使い勝手がよくないので、自分なりのテンプレートを作って再利用するのもよいでしょう。特に `author` を 忘れたり間違えると、正しく自分の記事にならないので注意してください。  


### プレビュー
Hexo の ローカルサーバ を 起動し、ブラウザで表示することで確認できます。  
`hexo server` または `hexo s` で 起動でき、 `-o` オプションをつけることでブラウザを自動起動できます。  

例えば以下のようなコマンドです
```console
hexo s -o
```


### 記事の投稿
作成した記事のファイルを GitHub へ Push します  
CircleCI で 自動的に公開するので、以上で投稿完了となります  

**注意**  
`source/_posts/[yourname]/` で 自分の記事は自分の管理下にあるので直接コミット＆プッシュします。  
他の人の記事については Issues か Pull Request で 修正等を出すようにします。  

例えば以下のようなコマンドです
```console
git add source/_posts/[yourname]/Hello-World.md
git commit -m "📝 Post "Hello World" article"
git push
```


### ブランチの活用
ドラフトの記事管理やレビューを受けたい場合などは、ブランチを活用することもできます。  
ブランチのソースは [CircleCI](https://circleci.com/gh/riotz-works) で ビルドだけ行われ、各ビルド の Artifacts として保管されます。その Artifacts の URL を 伝えることでビルド済みのサイトでレビューも可能です。  
- 新規ブランチをチェックアウトします
- 記事をライティングし、 コミットや必要に応じてプッシュします
- レビュー等が完了したら `squash` や `fixup` で コミットを１つにまとめます
- `source` ブランチへリベースします

For example, the following command:
```console
git checkout -b draft-hello-world

git add source/_posts/[yourname]/Hello-World.md
git commit -m "📝 Post "Hello World" article"
git push --set-upstream origin draft-hello-world

git add source/_posts/[yourname]/Hello-World.md
git commit -m "wip"
git push
```


### Disqus の アカウント設定
「Articles | Riotz.works」のウェブサイトは [Disqus](https://disqus.com/) の コメント・サービスを利用しています。コメントについて通知を受けるために Disqus の アカウント設定をします。  
Disqus [https://disqus.com/profile/signup/](https://disqus.com/profile/signup/) の アカウントを取得してください。  
そして登録済みアカウントを既存のコミッターへ連絡し、サイトへ追加してもらってください。  


## コントリビューション
このリポジトリは Riotz.works の エンジニアが使うことを想定しています。  
しかしながら、私たちのウェブサイトを改善するためのフィードバックを歓迎しています。詳細はこちら [CONTRIBUTING](/.github/CONTRIBUTING.md) を ご確認ください。


----
© Riots.works  
