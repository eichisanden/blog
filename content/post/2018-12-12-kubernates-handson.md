+++
thumbnail = "images/k8s-handson/top.png"
tags = ["Kubernetes", "Docker", "Rancher", "Cloud Native", "さくらのクラウド"]
categories = ["技術メモ", "イベント"]
date = "2018-12-12T00:15:05+09:00"
title = "Cloud Native時代のコンテナ基盤構築ハンズオン18.12 ～ Kubernetes・Rancher on さくらのクラウド～に行ってきた"
description = "【2018年12月】Cloud Native時代のコンテナ基盤構築ハンズオン18.12 ～ Kubernetes・Rancher on さくらのクラウド～に参加した話"
+++

先日、[【2018年12月】Cloud Native時代のコンテナ基盤構築ハンズオン18.12 ～ Kubernetes・Rancher on さくらのクラウド～](https://sakura-kanto.doorkeeper.jp/events/82838)に行ってきました

## 概要

さくらインターネットさん主催の、Kubernatesを動かして覚えるハンズオン。  
以前、さくらインターネットのVPSを使ったことがあるのでアカウントは持っているし馴染みもあるのですが、今回はさくらのクラウドを使ったハンズオンでした。  
アカウントを貸し出してもらえるので自分のアカウントを使うことはありませんでした。

## 前半：座学

前半はイベントのタイトルにもなっているクラウドネイティブについての座学。

### クラウドネイティブとは

https://www.cncf.io/

主要なクラウドベンダーが参加する団体「CNCF」（Cloud Native Computing Foundation）が提唱している概念。
従来のオンプレからクラウドへ移行していたシステムに対して、最初からクラウド前提でデザインされたシステムのことをそう呼ぶそうです。

### Cloud Native Trail Map
https://www.cncf.io/blog/2018/03/08/introducing-the-cloud-native-landscape-2-0-interactive-edition/

CNCFが推奨するプロセスとして下記10ステップをあげています。
殆どのステップをCNCFでカバーしている。

1. コンテナ化  
最初の段階として、スケールしやするためコンテナ化が必要。
まずサーバーとホスト名(IPアドレス)が一意に対応づくという概念を捨てるところから始まる。

2. CI/CD  
コードの変更をテスト、デプロイ、コンテナへ反映を自動化するCI/CDをセットアップする

3. オーケストレーション  
Kubernetes、HELM

4. 監視と分析  
Prometheum、fluentd、Open Tracing、JAEGER

5. サービスプロキシ、ディスカバリ、サービスメッシュ  
envoy、CoreDNS、LINKERD

6. ネットワーク  
CNI

7. 分散データベースとストレージ  
Vitess、ROOK

8. ストリーミングとメッセージング  
GRPC、NATS

9. コンテナレジストリ  
HARBOR、ContainerD、rkt

10. ソフトウェア配布  
Notary、TUF

[ここ](https://landscape.cncf.io/)に列挙されていますが、ものすごい数です。
Kubernates上で動くFWとして、IstioとSpinnakerがよく使われているとのこと。




## 後半：ハンズオン

後半は、いよいよハンズオン。Qiitaの記事を見ながら説明してもらいました。

[Kubernetes・Rancherハンズオン on さくらのクラウドv18.12](https://qiita.com/zembutsu/items/41837d953a518c0b7f9e)

## Kubernetes環境構築編

CLIでKubernetes環境を構築していきます  
手順に従ってCoreOSのサーバを３台立ててブリッジに接続、VPCルータ経由でアクセスします

{{% img src="images/k8s-handson/map.png" w="800" h="415" %}}

ハマりどころはないはずですが、VPCルータの設定を変える時に変更→反映と2回ボタンを押す必要があるのですが、反映ボタンを押し忘れたぐらいです。3台セットアップするのは、まあまあ面倒くさいです。

## Rancher編

今度は[Racher](https://www.rancher.co.jp/)を使ったハンズオン

CentOS7.5のサーバーを一台立てて、それ上でRancherを動かします  
Rancherの設定は、予め用意されたスタートアップスクリプトでやってくれるので、特にやることはありません。

{{% img src="images/k8s-handson/rancher2-setup.png" w="800" h="248" %}}

スタートアップスクリプトの説明に使い方が書いてあります

{{% img src="images/k8s-handson/rancher2-setup1.png" w="800" h="186" %}}

スタートアップスクリプトの中を覗いて見るとdockerでrancherを動かしているようです。


```
# Rancherサーバ起動
docker run -d --restart=unless-stopped \
              -p 80:80 \
              -p 443:443 \
              -v /host/rancher:/var/lib/rancher \
              --name rancher-server \
              rancher/rancher
```

セットアップが終わると、`http://サーバーのアドレス/`で管理画面が開けます

{{% img src="images/k8s-handson/top.png" w="800" h="405" %}}

ノード名のプリフィックス、ノード数、テンプレート（sakura-default-template固定）などを指定してクラスタを追加します
（テンプレート本体は[ui-driver-sakuracloud](https://sacloud.github.io/ui-driver-sakuracloud/)なようです）

{{% img src="images/k8s-handson/setup.png" w="800" h="416" %}}

これだけでノード3台構成のクラスタを構築できました

{{% img src="images/k8s-handson/dashboard.png" w="800" h="405" %}}

今度はアプリを追加して行きます。Rancher公式のライブラリとデフォルトでは無効ですがHelmも使えるようです

{{% img src="images/k8s-handson/catalog.png" w="800" h="416" %}}

ハンズオンではWordpressを追加しました。カタログアプリからWordpressを探してセットアップしました。

{{% img src="images/k8s-handson/wordpress.png" w="800" h="427" %}}

これだけでKubernetes上にWordpressを構築できました

{{% img src="images/k8s-handson/blog.png" w="800" h="440" %}}


## おわりに

Rancher便利そうだけど、もうちょっとk8s自体を理解しないと。
クーポンを貰ったのもう少し動かしてみよう。

[【2019年1月】Cloud Native時代のコンテナ基盤構築ハンズオン19.1 ～ Kubernetes・Rancher on さくらのクラウド～](https://sakura-kanto.doorkeeper.jp/events/82839)

来月も開催されるようです