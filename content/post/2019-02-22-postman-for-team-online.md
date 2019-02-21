+++
thumbnail = "images/postman/title.png"
tags = ["API","チーム開発"]
categories = ["技術メモ"]
date = "2019-02-22T00:00:00+09:00"
title = "PostmanでAPIのテストをチーム内で共有する（アカウントを作る編）"
description = "APIの実行をcurlコマンドからPostmanに切り替えてチームに共有する話（アカウントを作る編）"
+++

前回、アカウントを作らない共有方法の[記事](https://gyoza.beer/post/2019-02-11-postman-for-team/)を書きましたが。今回はアカウントを作る編になります。

## PostmanアカウントでログインしていないとWorkspaceを切り替えられない

まず、Postmanアカウントでログインしていない場合、Workspaceを切り替えることが出来ません。
ログインするとWorkspaceを共有できるようになるのでやっていきます。

{{% img src="images/postman-for-team/1.png" w="400" h="405" %}}

## ログイン

Postmanアカウントでログインしていきます。

{{% img src="images/postman-for-team/2.png" w="400" h="508" %}}

Googleアカウントでログインできるので今回はこちらで試します。

{{% img src="images/postman-for-team/3.png" w="400" h="443" %}}

ログインしました。Collectionsなど、ログインせずに作成した設定が引き継がれています。

{{% img src="images/postman-for-team/4.png" w="800" h="524" %}}

ログインすると、Workspaceが切り替えられるようになりました。Create Newから新しいWorkspaceを作ることも可能ですが、今回はデフォルトで作成されている Team Workspaceを使ってみます。

{{% img src="images/postman-for-team/5.png" w="400" h="402" %}}

切り替えるとまっさらな設定が開くので、ここを設定してチームで共有することができます。自分個人の設定はPersonalのWorkspaceに設定できるので個人開発とチーム開発で設定を分けることができます。

{{% img src="images/postman-for-team/6.png" w="800" h="524" %}}

自分のデータだけエクスポートするのか、チーム全体のデータをエクスポートするのかExportの選択肢が増えています。

{{% img src="images/postman-for-team/7.png" w="800" h="535" %}}

ちなみに、再度Postmanアカウントでログインしないで開くと、先ほどあった設定が無くなっていました。事前にバックアップとしてエクスポートしておいた方が良いでしょう。

{{% img src="images/postman-for-team/9.png" w="800" h="524" %}}

## チームメンバーを招待

inviteからチームメンバーを招待していきます。

{{% img src="images/postman-for-team/10.png" w="400" h="467" %}}

招待した別のユーザでログインしてみます。Team Wrokspaceに切り替えるとちゃんと設定が共有できています（当たり前ですが）これで設定がシェアできました。

{{% img src="images/postman-for-team/11.png" w="800" h="524" %}}

## おわりに

Postmanアカウントでログインすることで、個人とチームの設定を分離しつつ共有することができました。
自分のチームではログインしないで使用しても十分な気もしますが、ログインすると色々便利な機能が使えるようになるようですが、それはまた別の機会に。

