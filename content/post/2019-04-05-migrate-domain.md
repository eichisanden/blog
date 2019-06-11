+++
thumbnail = "images/migrate-domain/title.png"
tags = ["Google Domains", "Netlify"]
categories = ["作業ログ"]
date = "2019-04-05T00:00:00+09:00"
title = "NetlifyでホストしているHugoブログをdevドメインに移行しました"
description = "NetlifyでホストしているHugoブログをdevドメインに移行した話"
toc = true
+++

お金を積めばdevドメインを優先的に取得できるEarly Access Programが終了して、2/28から年額12ドルの正規料金で取得できるようになったので3月の初めにdevドメインを購入しました。  
もともと独自ドメインで運用していたこのブログをdevドメインに移行してみたので手順をまとめておきます。

## Google Domainsでドメイン購入

本当はa1.devを取得したかったのですが2文字のプレミアムドメイン扱いのため¥81,780/年と高額すぎるため妥協してa-1.devを取得しました。今使っているドメインが¥3,000/年ぐらいなので¥1,400/年はお財布にやさしいです。

{{% img src="images/migrate-domain/google-domain2.png" w="600" h="246" %}}

ちなみに、devドメインはhttpsでしか使えないと説明が書いてあるので購入前にちゃんと理解しておきましょう。

{{% img src="images/migrate-domain/google-domain.png" w="600" h="482" %}}

## Netlifyに新しいプロジェクト追加してカスタムドメインを設定する

旧ドメイン名で稼働させているブログは残しつつ、新しいドメインでブログを立ち上げるため
新しいプロジェクトを作成しました。独自ドメインを設定するためにCustom domainsからAdd custom domainします。

{{% img src="images/migrate-domain/netlify2.png" w="600" h="249" %}}

カスタムドメインには`blog.a-1.dev`というサブドメインを指定しました。

{{% img src="images/migrate-domain/netlify3.png" w="600" h="292" %}}

## なぜサブドメインを指定するのか
もともと`gyoza.beer`というApexドメインでブログを運用していたのですが、ドキュメントをよく読んでみるとCNAMEで運用した方が良いみたいなのでサブドメインを指定しました。

https://www.netlify.com/blog/2017/02/28/to-www-or-not-www/

Apexドメインを指定する場合、NetlifyのロードバランサーのIPアドレスを指定する必要があります。
かたやCNAMEの向き先にNetlifyのドメインを指定しておけば、Netlify側でアクセスをうまくさばいてくれるのでCDNのメリットを最大限活かすことができます。

## DNSを設定する

カスタムドメインを設定しましたが、`Check DNS configuration`と表示されてDNSの設定が終わらないと使えませんので設定していきます。

{{% img src="images/migrate-domain/netlify4.png" w="600" h="336" %}}

どう設定するかはドキュメントに書いてあって、CNAMEの向き先をnetlify.comに向けるだけです。

{{% img src="images/migrate-domain/netlify5.png" w="600" h="529" %}}

DNSはGoogle Domainを使用しました。CNAMEの設定を追加してちょっと待つと反映されます。

{{% img src="images/migrate-domain/dns2.png" w="800" h="266" %}}

## SSL証明書を設定する

DNSの反映が済んだらデフォルトで使えるLet's Encryptの証明書を設定します。少し時間がかかりますが証明書が発行されます。

{{% img src="images/migrate-domain/ssl.png" w="800" h="318" %}}

## config.tomlのbaseURLを書き換え

HugoのbaseURLを新しいドメインに書き換えておきます。

```
baseURL = "https://blog.a-1.dev/"
```

ここまで設定すると新しいドメインでアクセス出来るようになりました。

## 旧ドメインから新ドメインにリダイレクトさせる

下記のようにnetlify.tomlを設定して旧ドメインへのアクセスを新ドメインに301でリダイレクトするようにします。

```toml
[[redirects]]
from = "https://gyoza.beer/*"
to = "https://blog.a-1.dev/:splat"
status = 301
force = true
```

ついでにnetlify.comへアクセスがきたら独自ドメインにリダイレクトするよう設定しました。まあ、こっちにアクセスされることはないのですが。

```toml
[[redirects]]
from = "https://angry-euclid-67959a.netlify.com/*"
to = "https://blog.a-1.dev/:splat"
status = 301
force = true
```

## HSTSプリロードリストへの登録

https://hstspreload.org/

HSTSに登録しようとしたらすでに登録されているとのことでした。

{{% img src="images/migrate-domain/hsts.png" w="800" h="204" %}}

Google Chromeから`chrome://net-internals/#hsts`で確認しても確かに有効なようです。

{{% img src="images/migrate-domain/hsts2.png" w="600" h="294" %}}

下記の[プリロードリスト](https://cs.chromium.org/chromium/src/net/http/transport_security_state_static.json?g=0&maxsize=15335808
)を見るとドメインレベルで`force-https`有効になっているようで個別に登録は不要なようです。devドメインをhttps縛りなのはこの設定で実現しているようです。他にもGoogleっぽいドメインが並んでいます。


```json
// gTLDs and eTLDs are welcome to preload if they are interested.
    { "name": "android", "policy": "public-suffix", "mode": "force-https", "include_subdomains": true },
    { "name": "app", "policy": "public-suffix", "mode": "force-https", "include_subdomains": true },
    { "name": "bank", "policy": "public-suffix", "mode": "force-https", "include_subdomains": true },
    { "name": "chrome", "policy": "public-suffix", "mode": "force-https", "include_subdomains": true },
    { "name": "dev", "policy": "public-suffix", "mode": "force-https", "include_subdomains": true },
    { "name": "foo", "policy": "public-suffix", "mode": "force-https", "include_subdomains": true },
    { "name": "gle", "policy": "public-suffix", "mode": "force-https", "include_subdomains": true },
    { "name": "google", "policy": "public-suffix", "mode": "force-https", "include_subdomains": true, "pins": "google" },
    { "name": "insurance", "policy": "public-suffix", "mode": "force-https", "include_subdomains": true },
    { "name": "new", "policy": "public-suffix", "mode": "force-https", "include_subdomains": true },
    { "name": "page", "policy": "public-suffix", "mode": "force-https", "include_subdomains": true },
    { "name": "play", "policy": "public-suffix", "mode": "force-https", "include_subdomains": true },
    { "name": "youtube", "policy": "public-suffix", "mode": "force-https", "include_subdomains": true },
```

## おわりに

しばらくの間、Google検索すると旧ドメインがヒットしていましたが、現在では新ドメインに入れ替わっているので旧ドメインの方は閉鎖しようかと思っています。割とスムーズに移行できました。
