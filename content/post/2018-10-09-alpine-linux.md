+++
thumbnail = "images/alpine-linux/alpine-linux.png"
tags = ["Alpine Linux","Docker"]
categories = ["技術メモ"]
date = "2018-10-08T09:36:49+09:00"
title = "Alpine Linuxを試してみる"
description = ""
+++

Docker Hubで公開されているイメージは、容量を減らすため軽量なDockerイメージをベースに作られていることが多いです。  
必要なコマンドが入ってなかったりして（特にファイル編集系）困ると思いつつ、Volumeをホストにマウントして、ホスト側のコマンドで誤魔化して使ってました。 
良い加減面倒なので、一番使われてそうな[Alpine Linux](https://alpinelinux.org/)をちょっと調べて見ました。

## 環境構築

まず、最新3.8をpullしてみます。

```
$ docker image pull alpine:3.8
```

容量はたったの4.41MBでした！この容量でLinuxが動くんだからすごいです。

```
$ docker image ls|grep alpine
alpine                                     3.8                 196d12cf6ab1        3 weeks ago         4.41MB
```

動かしてみます。Apline LinuxのDockerfileの`CMD`は`/bin/sh`になっているので、特に指定せずともシェルが立ち上がります。

```
$ docker container run -it alpine:3.8
/ #
```

デフォルトではbashは入ってなくて、ashが動いているようです。  
仕事でもbashに依存するようなシェルは書いてない気がするので問題ない気がします。

```
/ # cat /etc/shells
# valid login shells
/bin/sh
/bin/ash
```

色々試す環境ができました。

## BusyBox

Alpine Linuxはscratchという、空っぽのDockerイメージをベースにBusyBoxでコマンドを実現する構成になっています。  
/bin下をlsすると分かるのですが、コマンドは`/bin/busybox`にシンボリックリンクが貼られています。  
実行したファイル名と引数を受け取って、内部的に処理を振り分けているようです。  
それが軽さの秘密ですが、`busybox`自体も777KBしかありません。

```
/ # ls -lh /bin
total 780
lrwxrwxrwx    1 root     root          12 Sep 11 20:23 arch -> /bin/busybox
lrwxrwxrwx    1 root     root          12 Sep 11 20:23 ash -> /bin/busybox
lrwxrwxrwx    1 root     root          12 Sep 11 20:23 base64 -> /bin/busybox
lrwxrwxrwx    1 root     root          12 Sep 11 20:23 bbconfig -> /bin/busybox
-rwxr-xr-x    1 root     root      777.6K Jul 17 15:22 busybox
lrwxrwxrwx    1 root     root          12 Sep 11 20:23 cat -> /bin/busybox
（以下省略）
```

busyboxに引数としてコマンドを実行してもOK。やんないけど。

```
/ # /bin/busybox ps
PID   USER     TIME  COMMAND
    1 root      0:00 /bin/sh
    7 root      0:00 /bin/busybox ps
```

ヘルプを見ると使えるコマンドが見れます。基本的なコマンドは揃っている感じ...  
あれvi入っている...  
vi使えないコンテナ多いと思ったんだけど...他の何かと勘違いしていたようだ、まあ良いか。

```
/ # /bin/busybox --help
BusyBox v1.28.4 (2018-07-17 15:21:40 UTC) multi-call binary.
BusyBox is copyrighted by many authors between 1998-2015.
Licensed under GPLv2. See source distribution for detailed
copyright notices.

Usage: busybox [function [arguments]...]
   or: busybox --list[-full]
   or: busybox --install [-s] [DIR]
   or: function [arguments]...

	BusyBox is a multi-call binary that combines many common Unix
	utilities into a single executable.  Most people will create a
	link to busybox for each function they wish to use and BusyBox
	will act like whatever it was invoked as.

Currently defined functions:
	[, [[, acpid, add-shell, addgroup, adduser, adjtimex, arch, arp, arping, ash, awk, base64, basename, bbconfig, beep, blkdiscard, blkid,
	blockdev, brctl, bunzip2, bzcat, bzip2, cal, cat, chgrp, chmod, chown, chpasswd, chroot, chvt, cksum, clear, cmp, comm, conspy, cp, cpio,
	crond, crontab, cryptpw, cut, date, dc, dd, deallocvt, delgroup, deluser, depmod, df, diff, dirname, dmesg, dnsdomainname, dos2unix, du,
	dumpkmap, dumpleases, echo, ed, egrep, eject, env, ether-wake, expand, expr, factor, fallocate, false, fatattr, fbset, fbsplash, fdflush,
	fdformat, fdisk, fgrep, find, findfs, flock, fold, free, fsck, fstrim, fsync, fuser, getopt, getty, grep, groups, gunzip, gzip, halt, hd,
	hdparm, head, hexdump, hostid, hostname, hwclock, id, ifconfig, ifdown, ifenslave, ifup, init, inotifyd, insmod, install, ionice, iostat,
	ip, ipaddr, ipcalc, ipcrm, ipcs, iplink, ipneigh, iproute, iprule, iptunnel, kbd_mode, kill, killall, killall5, klogd, less, link, linux32,
	linux64, ln, loadfont, loadkmap, logger, login, logread, losetup, ls, lsmod, lsof, lspci, lsusb, lzcat, lzma, lzop, lzopcat, makemime,
	md5sum, mdev, mesg, microcom, mkdir, mkdosfs, mkfifo, mkfs.vfat, mknod, mkpasswd, mkswap, mktemp, modinfo, modprobe, more, mount,
	mountpoint, mpstat, mv, nameif, nanddump, nandwrite, nbd-client, nc, netstat, nice, nl, nmeter, nohup, nologin, nproc, nsenter, nslookup,
	ntpd, od, openvt, partprobe, passwd, paste, patch, pgrep, pidof, ping, ping6, pipe_progress, pkill, pmap, poweroff, powertop, printenv,
	printf, ps, pscan, pstree, pwd, pwdx, raidautorun, rdate, rdev, readahead, readlink, readprofile, realpath, reboot, reformime, remove-shell,
	renice, reset, resize, rev, rfkill, rm, rmdir, rmmod, route, run-parts, sed, sendmail, seq, setconsole, setfont, setkeycodes, setlogcons,
	setpriv, setserial, setsid, sh, sha1sum, sha256sum, sha3sum, sha512sum, showkey, shred, shuf, slattach, sleep, smemcap, sort, split, stat,
	strings, stty, su, sum, swapoff, swapon, switch_root, sync, sysctl, syslogd, tac, tail, tar, tee, test, time, timeout, top, touch, tr,
	traceroute, traceroute6, true, truncate, tty, ttysize, tunctl, udhcpc, udhcpc6, umount, uname, unexpand, uniq, unix2dos, unlink, unlzma,
	unlzop, unshare, unxz, unzip, uptime, usleep, uudecode, uuencode, vconfig, vi, vlock, volname, watch, watchdog, wc, wget, which, whoami,
	whois, xargs, xxd, xzcat, yes, zcat
```

## apkでパッケージ管理

apkコマンドで足りないパッケージはダウンロードできます。まず`apk update`で最新化。

```
/ # apk update
fetch http://dl-cdn.alpinelinux.org/alpine/v3.8/main/x86_64/APKINDEX.tar.gz
fetch http://dl-cdn.alpinelinux.org/alpine/v3.8/community/x86_64/APKINDEX.tar.gz
v3.8.1-26-ga9d9f445b7 [http://dl-cdn.alpinelinux.org/alpine/v3.8/main]
v3.8.1-23-ga2d8d72222 [http://dl-cdn.alpinelinux.org/alpine/v3.8/community]
OK: 9539 distinct packages available
```

`apk add`でインストールします。

```
/ # apk add tree
(1/1) Installing tree (1.7.0-r1)
Executing busybox-1.28.4-r1.trigger
OK: 5 MiB in 14 packages

/ # ls -l /usr/bin/tree
-rwxr-xr-x    1 root     root         85808 May  1 15:28 /usr/bin/tree
```

`apk del` でアンインストール。

```
/ # apk del tree
(1/1) Purging tree (1.7.0-r1)
Executing busybox-1.28.4-r1.trigger
OK: 4 MiB in 13 packages
```

`--virtual` で適当な名前をつけておくと、その名前でまとめてアンインストールできる。便利そう。

```
/ # apk add --virtual=hoge git git-svn

/ # apk del hoge
```

## おわりに

Docker Hubにいくと、色んなイメージがありますが
今度仕事でDockerイメージ作る時はAlpine Linuxベースで作るかな。


