+++
draft = true
thumbnail = ""
tags = ["Docker"]
categories = ["作業ログ"]
date = "2019-03-24T14:12:15+09:00"
title = "Dockerで/etc/localtimeがマウントできない"
description = "Dockerで/etc/localtimeがマウントできない"
+++

- docker-compose.yml

```
version: "3"

services:
  hello:
    image: hello-world
    volumes:
      - /etc/localtime:/etc/localtime:ro
```

-エラーになる

```
docker-compose up                                                                                                                        6:24:53
Recreating work_hello_1 ... error

ERROR: for work_hello_1  Cannot start service hello: b'Mounts denied: \r\nThe path /etc/localtime\r\nis not shared from OS X and is not known to Docker.\r\nYou can configure shared paths from Docker -> Preferences... -> File Sharing.\r\nSee https://docs.docker.com/docker-for-mac/osxfs/#namespaces for more info.\r\n.'

ERROR: for hello  Cannot start service hello: b'Mounts denied: \r\nThe path /etc/localtime\r\nis not shared from OS X and is not known to Docker.\r\nYou can configure shared paths from Docker -> Preferences... -> File Sharing.\r\nSee https://docs.docker.com/docker-for-mac/osxfs/#namespaces for more info.\r\n.'
ERROR: Encountered errors while bringing up the project.
```

```
$ readlink /etc/localtime
/var/db/timezone/zoneinfo/Asia/Tokyo
```

直接指定した場合のエラー

```
$ docker-compose up                                                                                                                       22:59:18
Starting work_hello_1 ... error

ERROR: for work_hello_1  Cannot start service hello: b'Mounts denied: \r\nThe path /var/db/timezone/zoneinfo/Asia/Tokyo\r\nis not shared from OS X and is not known to Docker.\r\nYou can configure shared paths from Docker -> Preferences... -> File Sharing.\r\nSee https://docs.docker.com/docker-for-mac/osxfs/#namespaces for more info.\r\n.'

ERROR: for hello  Cannot start service hello: b'Mounts denied: \r\nThe path /var/db/timezone/zoneinfo/Asia/Tokyo\r\nis not shared from OS X and is not known to Docker.\r\nYou can configure shared paths from Docker -> Preferences... -> File Sharing.\r\nSee https://docs.docker.com/docker-for-mac/osxfs/#namespaces for more info.\r\n.'
ERROR: Encountered errors while bringing up the project.
```


Mac OSの/etcは/private/etcにリンクされています（private/etcなのが気になるが）

```
$readlink /etc                                                                                                                            6:31:02
private/etc
```
指定してみます。

```
    volumes:
      - /private/etc/localtime:/etc/localtime:ro
```

- エラー内容が変わった。
```
[work] docker-compose up                                                                                                                        6:28:14
Removing work_hello_1
Recreating 298b57e487ff_work_hello_1 ... error

ERROR: for 298b57e487ff_work_hello_1  Cannot start service hello: error while creating mount source path '/private/etc/localtime': mkdir /private/etc/localtime: file exists

ERROR: for hello  Cannot start service hello: error while creating mount source path '/private/etc/localtime': mkdir /private/etc/localtime: file exists
ERROR: Encountered errors while bringing up the project.
```

/etc -> /private/etc
/var -> /private/var
/private/etc/localtime -> /var/db/timezone/zoneinfo/Asia/Tokyo
/var/db/timezone/zoneinfo/Asia/Tokyo -> /private/var/db/timezone/zoneinfo/Asia/Tokyo

```
    volumes:
      - /private/var/db/timezone/zoneinfo/Asia/Tokyo:/etc/localtime:ro
```

```
docker-compose up                                                                                                                        7:58:12
Removing work_hello_1
Recreating 298b57e487ff_work_hello_1 ... error

ERROR: for 298b57e487ff_work_hello_1  Cannot start service hello: error while creating mount source path '/private/var/db/timezone/zoneinfo/Asia/Tokyo': mkdir /private/var/db/timezone/zoneinfo: file exists

ERROR: for hello  Cannot start service hello: error while creating mount source path '/private/var/db/timezone/zoneinfo/Asia/Tokyo': mkdir /private/var/db/timezone/zoneinfo: file exists
ERROR: Encountered errors while bringing up the project.
```

## 番外編

拡張属性の@の意味

```
ls -l /etc                                                                                                                               6:30:25
lrwxr-xr-x@ 1 root  wheel  11 10 16 18:48 /etc -> private/etc
```