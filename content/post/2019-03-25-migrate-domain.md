+++
draft = true
thumbnail = ""
tags = ["Google Domains", "Netlify"]
categories = ["作業ログ"]
date = "2019-03-24T14:12:15+09:00"
title = "devドメインにブログを移行しました"
description = "devドメインにブログを移行した話"
+++

お金を積めばdevドメインを優先的に取得できるEarly Access Programが終了して、2/28から年額12ドルの正規料金で取得できるようになったので早速devドメインを購入してみました。
使い道は考えていなかったのですが、このブログをdevドメインに移行してみました。

## Google Domainsでドメイン購入

本当はa1.devが取得したかったのですが2文字のためプレミアムドメイン扱いのため、81,780円と高額すぎるため断念してa-1.devを取得しました。
Google Domainsを初めて利用しましたがシンプルで分かりやすかったです。
某日本の某レジストらのごちゃごちゃしたUI

## Netlifyにプロジェクト追加

新しいブログ用に新しいプロジェクトを作成します。ソースはGitHubから取得しているので

## Domain

もともとgyoza.beerと言うApexドメインでブログを運用していたのですが、ホスト先のNetlifyのドキュメントをよく読んでみるとCNAMEでの運用を強く勧めているようです。

https://www.netlify.com/blog/2017/02/28/to-www-or-not-www/

CNAMEで指定しておけばNeflifyがDDOSを食らっている時にCDNをうまく参照するようにDNSを調整してくれるが、AレコードでNetlifyのロードバランサーのIPアドレスを直指定してしまうとそれが出来ないと言う理解。このタイミングでサブドメインを指定するようにします。

chrome://net-internals/#hsts

https://cs.chromium.org/chromium/src/net/http/transport_security_state_static.json?g=0&maxsize=15335808

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
