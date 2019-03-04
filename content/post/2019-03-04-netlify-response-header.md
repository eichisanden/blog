+++
thumbnail = "images/netlify-response-header/title.png"
tags = ["Netlify", "HSTS", "CDN"]
categories = ["技術メモ"]
date = "2019-03-04T13:35:42+09:00"
title = "Netlifyで任意のレスポンスヘッダーを返却する"
description = "Netlifyで任意のレスポンスヘッダーを返却する"
+++

## はじめに

[Netlifyにブログを引っ越した](https://gyoza.beer/post/2017-06-17-netlify/)時点では独自のレスポンスヘッダーが設定できなかったと思うのですが、久々に見てみたらできるようになっていたので設定してみました。

昔のビルド結果はこんな感じでした。

{{% img src="images/netlify-response-header/build-log-old.png" w="800" h="294" %}}

いつからかビルド結果画面に `No header rules proceeded` と表示されヘッダーが設定できることを示してくれていましたが全然気づいてませんでした。

{{% img src="images/netlify-response-header/build-log-new.png" w="800" h="424" %}}

## 設定してみる

https://www.netlify.com/docs/headers-and-basic-auth/


上記公式サイトを見て設定してみました。
_headersファイルか、netlify.tomlに記述すれば良いようですが、今回はnetlify.tomlファイルを使用しました。

## 設定ツール

{{% img src="images/netlify-response-header/test.png" w="800" h="659" %}}

[Netlify’s Playground](https://play.netlify.com/headers)というツールを使用しました。入力した内容のチェックとnetlify.tomlの形式に変換してくれます。

## 実行結果を確認

作成したnetlify.tomlをプロジェクトの直下に配置してデプロイするとレスポンスヘッダーが書き換わりました。

{{% img src="images/netlify-response-header/response-haeder.png" w="800" h="507" %}}

## HSTSプリロードリストヘの登録

以前も[hstsのプリロードリストに登録](https://gyoza.beer/post/2017-05-20-hsts/)していたのですが、Netlifyに引っ越した時にHSTS preloadヘッダーを吐き出せなくなったので強制的にリストから除外されてしまっていました。今回吐き出せるようになったので再度申請しました。

ちなみに、max-ageを半年に設定していたら短すぎと怒られてしまいました。

{{% img src="images/netlify-response-header/hsts-preload-list-error.png" w="600" h="249" %}}

これで申請したので、しばらくしたらリストに追加されるはずです。

{{% img src="images/netlify-response-header/hsts-preload-list.png" w="600" h="452" %}}

## おわりに

Netlifyに引っ越して唯一不満だったレスポンスヘッダーを設定できない問題が解消されました。