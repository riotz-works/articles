---
title: draw.io を利用する３つの方法
permalink: 3-ways-to-use-draw-io
date: 2019-08-24
author: lopburny
categories: 開発環境
tags:
- draw.io
- システム構成図
- ツール
- お絵かきツール
---

システム構成図を書く時、ツール選びに悩むことってありませんか？

![](/articles/assets/lopburny/img/lopburny_blog_8_thumbnail.jpg)

システム構成図に限らずとも、情報を分かりやすく表現する手段として、いわゆる「お絵かきツール」はとても需要があるのではないでしょうか。例えば Nulab の Cacoo だったり、MS Visio・Excel・PowerPoint だったりと、人によって・現場によって多種多様だと思いますが、今回はその中でも個人的によく使っている draw.io について紹介し、その使い方について共有したいと思います。

## draw.io とは？

システム構成図や図面等の作成に便利なお絵かきツールで、ブラウザ上で動くウェブアプリケーションです。オープンソース化（Apache License 2.0）されており、提供元 JGraph の GitHub リポジトリにてソースコードが公開されています。
<br>

<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://github.com/jgraph" data-iframely-url="//cdn.iframe.ly/5hWsFl4"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>


公式サイトがあるので、合わせてチェックしてみてください。
<br>
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://about.draw.io/" data-iframely-url="//cdn.iframe.ly/h85zveL"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>

作成したデータは XML で出力され、ローカルに保存できるだけでなく、Google Drive・OneDrive・GitHub などと連携して保存することができます。もちろん、PNG、SVG、PDF 等の形式でのエクスポートもサポートされています。簡単ですが個人的な観点で以下まとめてみました。

### draw.io のいいところ
<br>

- 単体での利用は、商用利用も含めて無料 [※公式サイト（Pricing）](https://about.draw.io/pricing/)
- 会員登録不要
- 作成したデータの管理は自分次第（クライアントサイドのみで動くため）
  - ローカルに保存するものよし、クラウドサービスに保存するのもよし
  - 気軽にクラウドサービスを利用できない環境では特に便利
- 図形のパーツが豊富
  - 最新のクラウドサービス（AWS・Azure・GCP）の各種アイコン
  - Material Design のパーツ等

### draw.io の不便なところ
<br>

- 複数の人で共同編集できない
- コメントを書く機能がない
- 履歴を管理する機能がない
- 書いた図を共有する仕組みがない（※Plugin などを利用すれば可能）

これらの項目については、Cacoo を使うと非常に便利な形で提供されていますので、気になる方は一度試してみてください。

## draw.io を利用する方法
draw.io は様々な形で利用することができます。個人的にはこの点が draw.io を使うようになったポイントになりましたので、紹介させていただきます。


### 1. ブラウザ上で利用する
最も簡単かつシンプルな方法です。ブラウザで [draw.io](https://www.draw.io/) を開くだけなので、他に必要なものは何もないですね。普段私は MacBook Air 13 (Early 2015) を使って Chrome 上で作業しますが、サクサク動いてくれて快適です。

![](/articles/assets/lopburny/img/lopburny_blog_8_pic_1.jpg)

サポートされているブラウザについて、[GitHub リポジトリ](https://github.com/jgraph/drawio#supported-browsers)に記載されている内容を引用しておきます。モダンなブラウザであれば問題なさそうですね。

> Supported Browsers
> draw.io supports IE 11, Chrome 32+, Firefox 38+, Safari 9.1.x, 10.1.x and 11.0.x, Opera 20+, Native Android browser 5.1.x+, the default browser in the current and previous major iOS versions (e.g. 11.2.x and 10.3.x) and Edge 23+.

### 2. デスクトップアプリを利用する
人によっては「ブラウザよりもデスクトップアプリの方が作業しやすい」ということもあるのではないでしょうか。Slack 等もそうですが、人それぞれの作業スタイルがありますので、Electron ベースで作られているデスクトップアプリを利用するのも良い選択です。こちらもオープンソース化（※[drawio-desktop](https://github.com/jgraph/drawio-desktop)）されています。

![](/articles/assets/lopburny/img/lopburny_blog_8_pic_2.jpg)

下記ページを開くと「draw.io Desktop」とありますので、Windows/Mac/Linux 環境に合わせてダウンロードすることができます。
<br>
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://about.draw.io/integrations/" data-iframely-url="//cdn.iframe.ly/5RR58lK"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>

Chrome App としても提供されていますね。
<br>
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://chrome.google.com/webstore/detail/drawio-desktop/pebppomjfocnoigkeepgbmcifnnlndla?hl=en-GB" data-iframely-url="//cdn.iframe.ly/EfSPqIV"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>

### 3. クラウドサービス等と組み合わせて利用する
draw.io 単体ではなく、プラグインとして他のサービスと組み合わせて利用する方法です。

### Confluence & Jira app
定番の Atlassian 製ソフトウェア開発ツールとの組み合わせです。Cloud版・Server版の両方に対応しており、多くの現場で導入しやすいのではないかと思います。Confluence の場合、Page 内で図を作成・管理することができ、作成した図を他のページに埋め込むこともできます。
<br>
<div class="iframely-embed"><div class="iframely-responsive" style="padding-bottom: 48.913%; padding-top: 120px;"><a href="https://marketplace.atlassian.com/apps/1210933/draw-io-diagrams-for-confluence" data-iframely-url="//cdn.iframe.ly/mKRd2Kg"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>

ただし、有料となりますので注意が必要です。先程 draw.io の不便なところとして「書いた図を共有する仕組みがない」と書きましたが、このパターンですとある程度解決します。また、リアルタイムでは無理ですが複数の人が図を更新していくことが可能になります。

料金は2019年8月時点で 10 ユーザーまで 570円、以降 100 ユーザーまで 1 ユーザーあたり 60 円となっていますね。

Confluence の場合、登録を完了すると、スペースのメニューに「draw.io Diagrams」が表示されます。

![](/articles/assets/lopburny/img/lopburny_blog_8_pic_3.png)

また、「マクロの選択」にて図の作成と埋め込みの機能が追加されます。

![](/articles/assets/lopburny/img/lopburny_blog_8_pic_4.jpg)

### その他
公式サイトによると、以下のサービスとの統合もサポートされています。
<br>
- [G Suite and Google Drive](https://gsuite.google.com/marketplace/app/drawio_diagrams/671128082532)
- [Office 365 and One Drive](https://appsource.microsoft.com/ja-JP/product/office/wa200000113)

他にも、draw.io で作成した図をWordPress や Redmine 上に埋め込むためのプラグインなどもありますので、各現場の環境に合わせた使い方を工夫してみてはいかがでしょうか。

最後まで読んでいただきありがとうございました。
