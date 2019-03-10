+++
thumbnail = "images/dockerfile/container.jpg"
tags = ["Docker"]
categories = ["技術メモ"]
date = "2018-10-21T19:48:36+09:00"
title = "Dockerfileを自分で作ってみて、Dockerの理解が足りないことが分かった"
description = "Dockerfileを作ってみてDockerの理解が足りてなくてハマった話"
+++

Dockerを使うのは慣れてきたのですが、自分でイメージを作ってみたらDockerを理解出来てないことが分かったというお話です  
今回、仕事で使っているCentOSのイメージをベースにPostgreSQLが動くコンテナを自前で作ってみました。

## コンテナがすぐ停止してしまう

Dockerfileの中で、`ENTRYPOINT`か`CMD`でフォアグラウンドのジョブが動くようにしていないとコンテナがすぐ終了してしまいます。  
それを知らなくて、PostgreSQLをバックグラウンドで起動していて何で起動しないんだろうって悩んでしまいました。

```
CMD ["/usr/pgsql-9.4/bin/postgres", "-D", "/usr/local/pgsql/data/"]
```

postgresコマンドをフォアグラウンドで起動すれば良く、`CMD`を上記に書き換えたらうまく行きました。  
普段、PostgreSQLをフォアグラウンドを動かす機会がないので、この起動の仕方知らなかった。

https://www.postgresql.jp/document/9.4/html/server-start.html

## Volumeを使ったらコンテナが起動しなくなった

Dockerfileが大体出来上がったので、データを外出しするためDATAフォルダをホストにマウントしようと、
`docker run -v $PWD/data:/usr/local/pgsql/data...` したらコンテナが起動しなくなりました。  
ログを見るとファイルが見つからなくてPostgreSQLの起動が失敗してろ、結果として前述の通りフォアグラウンドジョブが動いてないのでコンテナ自体が上がりませんでした。

```
$ docker logs pgsql
postgres cannot access the server configuration file
"/usr/local/pgsql/data/postgresql.conf": No such file or directory
```

これは、コンテナのディレクトリをホストにマウントすると思い込んでいたのですが実際は逆でした。
ホストのディレクトリがコンテナにマウントされたため空のフォルダを参照してファイルが存在しないエラーになっていました。この場合は存在しなので空のフォルダが作られています。
今回は、Dockerfileの中でinitdbしてフォルダが出来上がっているのでホストにマウントするのは無理でしたので、VOLUMEを使用しました。

```
VOLUME [ "/usr/local/pgsql/data" ]
```

https://docs.docker.com/engine/reference/commandline/run/#mount-volume--v---read-only

## Docker ToolBoxで立ち上げたコンテナのPostgreSQLに接続できない

`docker run -p <host-port>:<container-port>` でポートを紐づけてるのに全然繋がらなくてしばらく悩みましたが、  
Docker ToolBoxはVirtualBoxで動いているので、VirtualBoxでもポートフォワーディング設定をしないと駄目という落ちでした。  
VirtualBoxの設定 -> ネットワークのアダプター1のポートフォワーディングルールのホストポート、ゲストポートの両方に`-p`で指定した`<host-port>`を指定して登録することでうまく接続できるようになりました。

|ホストポート|ホストIP  |ゲストポート|
|:----------|:--------|:----------|
|host-port|127.0.0.1|host-port|


## おわりに

今回、分かってみればダサくて恥ずかしいミスですが、実際に手を動かしてみないと（少なくとも自分には）覚えにくかったです。  
dockerコマンドのオプションを間違ってハマったり、新しいオプションを覚えたり出来たので、手を動かすのって重要だな。
