+++
thumbnail = "images/dockerfile/container.jpg"
tags = ["Docker"]
categories = ["技術メモ"]
date = "2018-10-21T19:48:36+09:00"
title = "Dockerfileを自分で作ってみて、Dockerの理解が足りないことが分かった"
description = "Dockerfileを作ってみてDockerの理解が足りてなくてハマった話"
+++

Dockerを使うのは慣れてきたのですが、自分でイメージを作ってみたらDockerを理解出来てないことが分かったというお話です  
今回、仕事で使っているCentOSのイメージをベースにPostgreSQLが動くコンテナを自前で作ってみました

## コンテナがすぐ停止してしまう

Dockerfileの中で、`ENTRYPOINT`か`CMD`でフォアグランドのジョブが動くようにしていないとコンテナがすぐ終了してしまいます。  
それを知らなくて、PostgreSQLをバックグラウンドで起動してて何で起動しないんだろう？って悩んでしまいました。

```
CMD ["/usr/pgsql-9.4/bin/postgres", "-D", "/usr/local/pgsql/data/"]
```

postgresコマンドでフォアグラウンドで起動すれば良いので、`CMD`に上記に書き換えたらうまく行きました。  
普段、PostgreSQLをフォアグランドを動かす機会がないので、この起動の仕方知らなかった。

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

これは、コンテナのディレクトリをホストにマウントすると思い込んでいたのですが、
実際は逆で、ホストのディレクトリ（この場合は存在しなので空のフォルダが作られる）がコンテナにマウントされたため、空のフォルダを参照してファイルが存在しないエラーになっていました。  
今回は、Dockerfileの中でinitdbしてフォルダが出来上がっているのでホストにマウントするのは無理でしたので、VOLUMEを使用しました。

```
VOLUME [ "/usr/local/pgsql/data" ]
```

https://docs.docker.com/engine/reference/commandline/run/#mount-volume--v---read-only

## おわりに

今回、分かってみればダサくて恥ずかしいミスですが、実際に手を動かしてみないと（少なくとも自分には）覚えにくかったと思います。  
dockerコマンドのオプションを間違ってハマったり、新しいオプションを覚えたり出来たので、手を動かすのって重要だな。
