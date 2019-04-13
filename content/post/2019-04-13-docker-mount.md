+++
draft = true
thumbnail = "images/docker-mount-error/title.png"
tags = ["Docker"]
categories = ["作業ログ"]
date = "2019-04-13T16:00:00+09:00"
title = "Mac OSのDockerで/etc/localtimeがマウントできなくなって困った話"
description = "Mac OSのDockerで/etc/localtimeがマウントできなくなって困った話"
+++

ホストの`/etc/localtime`をコンテナの`/etc/localtime`にマウントすることでDockerイメージを使用する人のローカルの時刻に合わせる手法はよく行われますが、ある日Mac OSでそれができなくなって困ってしまいました。

## 環境

- Mac OS Mojave 10.14.4
- Docker Desktop Community 2.0.0.3
- Docker Engine 18.09.2

## 再現方法

実際は某OSSを動かそうとして発生しましたが、最小構成の`docker-compose.yml`を書いて再現させてみます。

```yml
version: "3"

services:
  hello:
    image: hello-world
    volumes:
      - /etc/localtime:/etc/localtime:ro
```

`docker-compose up`すると`/etc/localtime`がOS XからDockerに共有されていないと言ってマウントがエラーになリます。

```bash
$ docker-compose up
Starting work_hello_1 ... error

ERROR: for work_hello_1  Cannot start service hello: b'Mounts denied: \r\nThe path /etc/localtime\r\nis not shared from OS X and is not known to Docker.\r\nYou can configure shared paths from Docker -> Preferences... -> File Sharing.\r\nSee https://docs.docker.com/docker-for-mac/osxfs/#namespaces for more info.\r\n.'

ERROR: for hello  Cannot start service hello: b'Mounts denied: \r\nThe path /etc/localtime\r\nis not shared from OS X and is not known to Docker.\r\nYou can configure shared paths from Docker -> Preferences... -> File Sharing.\r\nSee https://docs.docker.com/docker-for-mac/osxfs/#namespaces for more info.\r\n.'
ERROR: Encountered errors while bringing up the project.
```

ログに書かれたURLを見ると、デフォルトだと`/Users`、`/Volumes`、`/private`、`/tmp`が共有されているがそれ以外のディレクトリはDockerに共有されていないようです。  
現在の設定は`Docker -> Preferces -> File Sharing`で確認できます。

{{% img src="images/docker-mount-error/file-sharing.png" w="800" h="693" %}}

`/etc`が共有されていないように思えますが、`/etc`は`/private/etc`にリンクが張られており`/private`が共有されているので問題ないはずです。  
また、`/etc/localtime`の実態は`/var/db/timezone/zoneinfo/Asia/Tokyo`で、`/var`の実態は`/private/var` なのでこれも問題ないはずです。  

```sh
$ readlink /etc
private/etc

$ readlink /etc/localtime
/var/db/timezone/zoneinfo/Asia/Tokyo

$ readlink /var
private/var
```

と言うか、もともとは使えてたので問題ないはずなんですよね。


## 対処法

https://github.com/docker/for-mac/issues/2396

本家のリポジトリでもIssueが上がっていますが現在のところ対症療法しかないようです。

### 対処法1.

`/etc/localtime`の代わりにファイルの実態である`/var/db/timezone/zoneinfo/Asia/Tokyo`を指定します。

```yml
version: "3"

services:
  hello:
    image: hello-world
    volumes:
      - /var/db/timezone/zoneinfo/Asia/Tokyo:/etc/localtime:ro
```

さらに共有フォルダに`/var/db`を追加します。

{{% img src="images/docker-mount-error/file-sharing2.png" w="800" h="693" %}}

これで`docker-compose up`すると無事起動できました。  
このことから、`/var`と`/etc`のサブフォルダを明示的に共有することでマウント出来ることが分かりました。

`/etc`を共有できれば`/etc/localtime`がマウントできそうですが、それは出来ないようです。  
これが出来れば設定ファイルを書き変えず実行できるのに残念。

{{% img src="images/docker-mount-error/etc-cant-shre.png" w="600" h="244" %}}

### 対処法2.

参照できるディレクトリにtimezoneファイルを作成する方法です。`/etc/localtime`はバイナリのファイルですが、テキストファイルにタイムゾーンを指定する方法もあるようです。この例では`/Users`下にファイルを作成しました。

```
$ mkdir ~/etc
$ echo Asia/Tokyo > ~/etc/timezone
```

`docker-compose.yml`を書き換えて`/etc/timezone`をマウントさせます。

```yml
version: "3"

services:
  hello:
    image: hello-world
    volumes:
      - ~/etc/timezone:/etc/localtime:ro
```

### 対処法3.

最後はTZ環境変数を使用する方法です。  
docker-compose.ymlのenvironmentに`TZ=Asia/Tokyo` を追加してタイムゾーンを指定します。  
設定ファイルを書き換えるだけで済むのが楽なので、自分はこの方法で回避しています。

```yml
version: "3"

services:
  hello:
    image: hello-world
    environment:
      - TZ=Asia/Tokyo
```

## おわりに

どれも設定ファイルを書き換える方法なのがイマイチですが回避はできました。  
早いところ根本的に対応してくれないかな。
