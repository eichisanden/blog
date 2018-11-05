+++
draft = true
thumbnail = ""
tags = ["Chrome"]
categories = ["技術メモ"]
date = "2018-11-05T13:17:11+09:00"
title = "Chrome Devtoolsでスクレイピングする"
description = "Chrome Devtoolsを使ったスクレイピング方法"
+++

サイトからちょっとデータを抜きたい時に、Chrome Devtoolsを使うと便利でした。
まず、Consoleで下記のように実行するとDom要素が抜けます。

- $(selector)  
指定された CSS セレクターに一致する最初の要素を返します。
document.querySelector() のショートカットです。
ただし、jQueryを使っているサイトではそちらが優先されるので使えないケースが多いと思いますので、その場合は`$$(selector)[0]`で代用可能です。

- $$(selector)  
指定された CSS セレクターに一致するすべての要素の配列を返します。 document.querySelectorAll() のエイリアスです。
返される配列はArrayなので扱いやすい。

- $x(xpath)
指定された XPath に一致する要素の配列を返します。
あんまり使わない。

ここから選手名だけを抽出したいと思います。

```
$$('td.ltnb a').map(a => {return a.innerText}).join("\n")
```

