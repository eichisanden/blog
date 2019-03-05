+++
categories = ["技術メモ"]
date = "2017-07-09T23:50:13+09:00"
draft = false
tags = ["Hugo", "Amazon"]
thumbnail = "images/eye-catch/hugo.png"
title = "HugoのShortcodesを自作してみる"

+++

Hugoでは、Markdownで書けないことはHTMLを直接書くわけですが、Shortcodesというテンプレート化する仕組みがあります。

https://gohugo.io/extras/shortcodes/

TwitterやYoutubeの埋め込みはビルドインのもので出来るのですが、Amazonにリンク貼るものがなかったので自作してみました。  

## 完成イメージ

こんな感じにAmazonへのリンクを表示するシンプルなものを作っていきます。  

{{% img src="images/hugo-shortcodes/shortcode1.png" caption="完成イメージ" w="759px" h="201px" %}}

## 呼び出し側のコード  

amazonという名前でShortCodeを作成して、すべてパラメータで渡すスタイルにしました。  
商品コードだけ指定して動的に検索する仕組みにするのも可能ですが、JSONを取得するAPIを自前で作ったり結構大変です。  
今回は個人使用だし、頻繁に使うわけじゃないのでこれで十分です。  

    {{%/* amazon 
      itemId="4873118026"
      title="エラスティックリーダーシップ ―自己組織化チームの育て方"
      author="Roy Osherove"
      publisher="オライリージャパン"
      imageUrl="https://images-fe.ssl-images-amazon.com/images/I/51hwSe%2BgVeL._SL160_.jpg"
    */%}}

## ShortCodeの作成

`layouts/shortcodes/amazon.html`ファイルを作成してコードを書いていきます。  
基本、引数で受け取ったものを`{{ .Get "xxx" }}`で埋め込んでいくだけです。  
AmazonのアソシエイトIDはconfig.tomlに追加したので`{{ .Site.Params.AmazonId }}`で取得しています。  
このコード内容を記事に直接書かないで済むので、記事がかなりスッキリしました。

```html
<div class="amazon-box">
  <div class="amazon-image">
    <a href="http://www.amazon.co.jp/exec/obidos/ASIN/{{ .Get "itemId" }}/{{ .Site.Params.AmazonId }}/ref=nosim/" target="_blank">
      <img src="{{ .Get "imageUrl" }}" alt="{{ .Get "title" }}" />
    </a>
  </div>
  <div class="amazon-info">
    <div class="amazon-name">
      <a href="http://www.amazon.co.jp/exec/obidos/ASIN/{{ .Get "itemId" }}/{{ .Site.Params.AmazonId }}/ref=nosim/" target="_blank">{{ .Get "title" }}</a>
    </div>
    <div>{{ .Get "author" }} <br />{{ .Get "publisher" }} <br />
    </div>
  </div>
  <div class="clear-left"></div>
</div>
```

## ShortCodeの作成（AMP対応）

私が使用しているRobustテーマはampに対応しているので、AMP用の`layouts/shortcodes/amazon.amp.html`も作成します。  
世の中のAmazonリンク生成ツールはAMPに対応していないようなので、今回ShortCodeを作ろうと思った次第です。  
AMPでは`img`タグは使えないので、代わりに`amp-image`タグを使用します。  

```html
<div class="amazon-box">
  <div class="amazon-image">
    <a href="http://www.amazon.co.jp/exec/obidos/ASIN/{{ .Get "itemId" }}/{{ .Site.Params.AmazonId }}/ref=nosim/" target="_blank">
      <amp-img src="{{ .Get "imageUrl" }}" alt="{{ .Get "title" }}" width=113 height=160></amp-img>
    </a>
  </div>
  <div class="amazon-info">
    <div class="amazon-name">
      <a href="http://www.amazon.co.jp/exec/obidos/ASIN/{{ .Get "itemId" }}/{{ .Site.Params.AmazonId }}/ref=nosim/" target="_blank">{{ .Get "title" }}</a>
    </div>
    <div>{{ .Get "author" }} <br />{{ .Get "publisher" }} <br />
    </div>
  </div>
  <div class="clear-left"></div>
</div>
```
AMPでCSSの指定させるのが結構面倒でしたが、それはまた別の機会に。
