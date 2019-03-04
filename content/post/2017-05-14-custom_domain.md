+++
categories = ["技術メモ"]
date = "2017-05-14T23:40:05+09:00"
tags = ["GitHub", "GitHub Pages", "独自ドメイン", "DNS"]
thumbnail = ""
title = "GitHub Pagesを独自ドメインで公開する"
+++

## モチベーション

長いことエンジニアをしていますが、独自ドメインの取得やDNSの設定など実際にやったことがなかったので、そこらへんの知識を得るため GitHub Pagesで公開したブログに独自ドメインを設定してみました。

GitHubのHelpを見ながら設定していきます。

- https://help.github.com/articles/using-a-custom-domain-with-github-pages/

## ドメインを取得する

まず、お名前.comで `gyoza.beer` ドメインを取得しました。  
beerドメインに一目惚れしてしまい、beerに合うものと考えたところgyoza.beerに決定w  
初年も更新も確か2,980円と、決して安くはないけどバカ高くもないでまあ良しとします。

他の用途でも使いたくなるかもしれないので、 `blog.gyoza.beer` というサブドメインをGitHub Pagesに割り当てることにします。  
なので、この先はCustom Subdomainの設定手順に従って進めます。

https://help.github.com/articles/setting-up-a-custom-subdomain/

## GitHub にドメインを設定する

GitHub Pagesで公開しているリポジトリのSettingsからCustom Domainに `blog.gyoza.beer` を設定します。  
自分の場合は `ユーザ名.github.io` リポジトリに設定します。

{{% img src="images/custom_domain/github_setting_custom_domain.png" w="740" h="594" %}}

ネットで色々調べてると、CNAMEファイルを手動で追加する手順が出てきますが、Helpによるとこの設定をした時点で自動的にCNAMEファイルが生成されるようでした。

https://help.github.com/articles/troubleshooting-custom-domains/#cname-already-taken

確かに、私はファイルを置いてませんが問題なくアクセスできていて不思議に思っていましたが、後からcurlを叩いてみたところCNAMEファイルが置かれていました。

```
$ curl http://blog.gyoza.beer/CNAME
blog.gyoza.beer
```

後からコミットログを見たら、勝手にCNAMEファイルがコミットされてました。  
自分のアカウントでコミットされているし、もう少し自動生成と分かるコメント入れてコミットして欲しいですね。

{{% img src="images/custom_domain/github_commit_cname.png" w="800" h="240" %}}

なお、この設定の影響は `github.io/ユーザ名/リポジトリ名` 形式のリポジトリにも及ぶようで、例えばabcリポジトリ自体には何も設定しなくても `http://blog.gyoza.beer/abc` でアクセスできるようになってました。

## DNSの設定

お名前.comの管理コンソールからDNSにCNAMEレコードを追加します。

{{% img src="images/custom_domain/onamae-com-dns-setting.png" w="679" h="151" %}}

10分ぐらい待つとDNSに反映されました。

```
$ dig blog.gyoza.beer +nostats +nocomments +nocmd                                                                        

; <<>> DiG 9.8.3-P1 <<>> blog.gyoza.beer +nostats +nocomments +nocmd
;; global options: +cmd
;blog.gyoza.beer.		IN	A
blog.gyoza.beer.	3600	IN	CNAME	eichisande.github.io.
eichisande.github.io.	1562	IN	CNAME	github.map.fastly.net.
github.map.fastly.net.	7	IN	A	151.101.72.133
```

実は最初は良くわかってなくて、サブドメインなのにAレコードを設定していました。  
普通に動くので気づいていなかったけど、GitHubから警告のメールが来てCNAMEでの設定に変更しています。

```
Titile: [eichisanden/eichisanden.github.io] Page build warning
---
The page build completed successfully, but returned the following warning for the `master` branch:

Your site's DNS settings are using a custom subdomain, blog.gyoza.beer, that's set up as an A record. We recommend you change this to a CNAME record pointing at eichisanden.github.io. For more information, see https://help.github.com/pages/.

For information on troubleshooting Jekyll see:

  https://help.github.com/articles/troubleshooting-jekyll-builds

If you have any questions you can contact us by replying to this email.
```

## さいごに

仕事ではDNSを触ることはまず無いので、プライベートで触れておくことは重要だなと思いました。次はhttps化していきたいと思います。
