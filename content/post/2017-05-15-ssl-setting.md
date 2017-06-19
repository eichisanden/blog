+++
categories = ["技術メモ"]
date = "2017-05-18T13:21:14+09:00"
draft = false
tags = ["Github", "Github Pages", "SSL"]
thumbnail = "images/cloud_flare_top.png"
title = "Cloud Flareで独自ドメインのGihub Pagesをhttps化する"

+++

独自ドメインで公開しているGihub Pagesをhttps化してみました。  

https://github.com/isaacs/github/issues/156

こちらで意見交換されてますが、Githubは独自に証明書を設定する機能を実装していません。  

仕方なく別の方法を調べてみるとCDNを使用したリバースプロキシをhttps化する方法が一般的なようです。  
ただし、証明書をサーバに設置する普通のやり方と比較すると、欠点もあります。

- CDNまでのルートがhttps化されるというだけで、CDN -> Github間は平文になってしまう  
- 1つのIPアドレスに複数の証明書を登録するための技術、SNI(Server Name Indication)が使われているが対応していないブラウザがある。

今回の使い方では、そこまでセキュアさを求めないないし、よっぽど古くなければブラウザも対応しているので問題なしと判断しました。

今回はCloud Flareとしうサービスを使ってみました。  
採用した理由は、無料でSSLが使えるし、Github Pagesでの利用実績が一番多そうだからです。

https://www.cloudflare.com/

# Coud Flareのセットアップ

設定は思いのほか簡単で、アカウントを作成後、ウィザードに従ってセットアップするだけで最低限のことはできてしまいます。

まずは自分のドメインを検索します。（※画像はサブドメインを検索していますが本来はApexドメインを検索します）

{{% img src="images/cloud_flare1.png" w="260" h="118" %}}

そうすると、現状のDNSの設定を持って来てくれます。

{{% img src="images/cloud_flare2.png" w="260" h="153" %}}

プランを選択します。今回はFree版を選択します。

{{% img src="images/cloud_flare3.png" w="260" h="176" %}}

ネームサーバーが表示されるのでメモっておきます

{{% img src="images/cloud_flare4.png" w="260" h="151" %}}

ここまででウィザードでの設定は完了です。  
特に何も聞かれませんが、SSLの設定は「Flexible」という設定になっています。  
CDNまでSSLにする、というやりたかった設定がデフォルトなので変更する必要はありません。

# ネームサーバーの変更

今度は、お名前.comの設定画面から、ネームサーバーの設定を先ほどメモしたものに変更します。  

{{% img src="images/cloud_flare5.png" w="260" h="133" %}}

しばらく待てば設定が反映されます。
これだけで自分のサイトが、SSL化されています。

# Page Ruleの設定

今度はhttpsを強制するため、Page Rulesの追加を行います。  
これにより、httpでアクセスされたら301を返してhttpにリダイレクトされるようになります。

{{% img src="images/cloud_flare6.png" w="260" h="148" %}}

# 証明書を確認する

Alaternavite namesがえらいことになってます(笑)  
証明書はCOMODOみたいです。

{{% img src="images/cloud_flare8.png" w="260" h="142.781" %}}

セキュリティのレベルは https://www.ssllabs.com で見るとAランクでした。

{{% img src="images/cloud_flare7.png" w="260" h="110.375" %}}

# 最後に

厳密にはSSL化したとは言えないと思いますが、ひとまず見た目上はhttpsになりました。
