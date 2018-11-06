+++
draft = true
thumbnail = ""
tags = ["Chrome"]
categories = ["技術メモ"]
date = "2018-11-05T13:17:11+09:00"
title = "Chrome Devtoolsでスクレイピングする"
description = "Chrome Devtoolsを使ったスクレイピング方法"
+++

サイトからちょっとデータをコピーしたいんだけど単純にコピーできない時に、Chrome Devtoolsを使うと便利という話。  
今まで知らなかったですが、Command Line APIというもので、ConsoleからjQueryライクな書き方ができてDom要素が取得できるのでそれを使います。

## Command Line API

下記の書き方で、必要なNodeが取得できるので、あとはmapなりforEachなりで回して必要な形でデータを取得します。

- $(selector)  
指定された CSS セレクターに一致する最初の要素を返します。  
document.querySelector() のショートカットです。  
ただし、jQueryを使っているサイトではjQueryの$が優先されてしまうので、その場合は`$$(selector)[0]`で代用可能です。

- $$(selector)  
指定された CSS セレクターに一致するすべての要素の配列を返します。 document.querySelectorAll() のエイリアスですがNodeListではなく、Arrayを返してくれるので扱いやすいです。

<参考>  
https://developer.mozilla.org/ja/docs/Web/API/Document/querySelectorAll

- $x(xpath)
指定された XPath に一致する要素の配列を返します。

## 試しに使ってみる

試しに、[ここ](https://baseball.yahoo.co.jp/npb/stats/batter?series=2
)からパリーグの打率トップ29の選手名を抽出してみたいと思います。
人様のサイトを題材にしてますが、当然、注意してください。

```
$$('td.ltnb a').map(a => {return a.innerText})
```

こんな感じに書くことで選手名だけ取得できます。

{{% img src="images/scraping-with-chrome/scraping.png" w="1016" h="358" %}}

`copy()`でクリップボードにコピーすることもできます

```
copy($$('td.ltnb a').map(a => {return a.innerText}))
```

今度は打率とチーム名も取得してみたいと思います。  
class属性がついているので、それをMapのキーに利用して取得してみました。

```
$$('table.NpbPlSt tr').map(tr => {
        var m = {};
        return $$('td', tr).reduce((a,td,index) => {
            a[td.className] = td.innerText;
            return a
        }, {});
    }
)
```

すごく雑な書き方ですが、目的は果たしているので良しとします。

{{% img src="images/scraping-with-chrome/avegate.png" w="1944" h="358" %}}

## おわりに

ちょっとサイトのデータをコピペしたい場合に覚えておくと便利そうです。
