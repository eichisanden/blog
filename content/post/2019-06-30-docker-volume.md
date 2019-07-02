
+++
categories = ["技術メモ"]
date = "2019-06-30T19:00:00+09:00"
description = "Dockerコンテナ内で作成したファイルをホストにマウントする"
tags = ["Docker"]
thumbnail = "images/eye-catch/docker.png"
title = "Dockerコンテナ内で作成したファイルをホストにマウントする"
+++

### はじめに

下記のようなことを実現しようとして少し苦労しました。

- Dockerイメージに最初にから入っていて欲しいファイルをDockerfile内で作成
- それをbindマウントでホストと共有したい

すごくシンプルな例として`/usr/local/work/hello.txt`を共有してみます。

### 失敗例

当初はDockerfileの中に必要な処理をすベて書いていました。

```
$ cat Dockerfile
FROM centos:7.6.1810
RUN mkdir /usr/local/work
RUN echo "hello" > /usr/local/work/hello.txt
```

Dockerイメージをビルドします。

```bash
$ docker build -t test:1.0 .
```

`/usr/local/work`をbindマウントでdocker runしてみます。

```
$ docker run --rm -v $PWD/data:/usr/local/work test:1.0
```

dataフォルダが存在しなければ勝手に作られます。ただし、フォルダは作成されますが中身は空っぽです。

```
$ ls data
```

コンテナに入ってdfコマンドを叩いてみると、単純にホスト側のディレクトリがマウントされていました。  
この方法でコンテナのファイルを共有するのは無理だということが分りました。何事も経験です。

```
# df -h
Filesystem      Size  Used Avail Use% Mounted on
overlay          59G   12G   44G  22% /
tmpfs            64M     0   64M   0% /dev
tmpfs          1000M     0 1000M   0% /sys/fs/cgroup
/dev/sda1        59G   12G   44G  22% /etc/hosts
shm              64M  8.0K   64M   1% /dev/shm
osxfs           466G  136G  326G  30% /usr/local/work
tmpfs          1000M  8.3M  992M   1% /run
```

### 解決方法

今度はフォルダやファイルの作成をDockerfile内では直接やらず`ENTRYPOINT`から起動したシェルの中でやるようにします。

```
$ cat Dockerfile
FROM centos:7.6.1810
COPY entrypoint.sh /tmp/
ENTRYPOINT sh -x /tmp/entrypoint.sh
```

entrypoint.shではファイルを作成します。ちなみに`/usr/local/work`フォルダは`docker run -v`で勝手に作成されるので明示的に作成しませんでした。この動きも最初はよく分かっていなくて大分混乱しました。

```sh
#!/bin/sh
echo "hello" > /usr/local/work/hello.txt
```

docker runしてみるとdataフォルダが作成され中身もちゃんと共有され、やりたいことが出来ました。

```
$ docker run --rm -v $PWD/data:/usr/local/work test:1.0
$ ls data
data/hello.txt

$ cat data/hello.txt
hello
```

Entrypointでファイルを書き込む手法はPostgreSQLやMySQLのコンテナでも行われてましたので一般的なやり方なんでしょう。

### --mountオプション

だいぶ前からですが、`-v`より`--mount`の方が推奨されているそうです。どっちがsourceでどっちがdestinationなのかも分りやすいかな。ちなみにdataフォルダは事前に存在しないとエラーになるので注意が必要ですがホストのファイルをマウントすることを案に示しているようで良いと思いました。

```
$ mkdir data
$ docker run --rm --mount type=bind,src=$PWD/data,dst=/usr/local/work test:1.0
```

### おわりに

Dockerのバインドマウントは他にもパーミッションの問題があったり色々難しいですね。通常はVOLUMEマウントして必要なファイルをdocker cpで取得するのが良いかなというのが現時点での私の結論です。
