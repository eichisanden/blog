+++
thumbnail = "images/scraping-with-chrome/title.png"
tags = ["Chrome"]
categories = ["技術メモ"]
date = "2018-11-06T19:55:11+09:00"
title = "Chrome Devtoolsでスクレイピングする"
description = "Chrome Devtoolsを使ったスクレイピング方法"
+++

サイトからデータをコピーしたいんだけど単純にコピーできない時に、Chrome Devtoolsを使うと便利でした。  
Command Line APIというjQueryライクなAPIをConsoleから叩くとNode情報が取得できるのでそれをゴニョゴニョしてみます。

## Command Line API

下記の書き方で、必要なNodeが取得できるので、あとはmapなりforEachなりで回して必要な形でデータを取得します。

- $(selector)  
指定されたCSSセレクターに一致する最初の要素を返します。  
document.querySelector() のショートカットです。  
ただし、jQueryを使っているサイトではjQueryの$が優先されてしまうので、その場合は`$$(selector)[0]`で代用しましょう。

- $$(selector)  
指定されたCSSセレクターに一致するすべての要素の配列を返します。 document.querySelectorAll() に近いですが戻り値がNodeListではなく、Arrayなので扱いやすいです。

<参考>  
https://developer.mozilla.org/ja/docs/Web/API/Document/querySelectorAll

- $x(xpath)  
指定されたXPathに一致する要素の配列を返します。お好みでどうぞ。

## 試しに使ってみる

試しにこのブログの記事一覧を取得してみます。
Elementsで調ベたところ、タイトルは`div.title`で拾えそうです。

{{% img src="images/scraping-with-chrome/1.png" w="1420" h="470" %}}

`$$(div.title)`で10要素拾えたので合っているようです。ちなみにEager evaluationが有効になっていると実行しなくも入力途中から結果が見れるので便利です。

{{% img src="images/scraping-with-chrome/2.png" w="1216" h="466" %}}

map()でinnerTextをreturnしてタイトルの配列ができました。

{{% img src="images/scraping-with-chrome/3.png" w="1626" h="138" %}}

改行コードで区切ったり、カンマやタブで区切ったり、あとはご自由に。

{{% img src="images/scraping-with-chrome/4.png" w="1648" h="198" %}}

`copy()`で囲むとクリップボードにコピーができます。

{{% img src="images/scraping-with-chrome/5.png" w="744" h="94" %}}

## おわりに

ちょっとサイトのデータをコピー&ペーストしたい場合なんかに覚えておくと便利そうです。
