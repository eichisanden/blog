+++
draft = true
thumbnail = ""
tags = []
categories = []
date = "2018-11-04T15:27:21+09:00"
title = "2018 11 04 Docker Gvisor"
description = ""
+++

Ubuntu 18.04.1 LTS (Bionic Beaver)

wget https://storage.googleapis.com/gvisor/releases/nightly/latest/runsc
wget https://storage.googleapis.com/gvisor/releases/nightly/latest/runsc.sha512
sha512sum -c runsc.sha512
chmod a+x runsc
sudo mv runsc /usr/local/bin

/var/snap/docker/321/config/daemon.json

$ ps -fe|grep docker
root      1985     1  0 12:17 ?        00:00:01 dockerd -G docker --exec-root=/var/snap/docker/321/run/docker --data-root=/var/snap/docker/common/var-lib-doc{
ker --pidfile=/var/snap/docker/321/run/docker.pid --config-file=/var/snap/docker/321/config/daemon.json --debug
root      2047  1985  0 12:17 ?        00:00:03 docker-containerd --config /var/snap/docker/321/run/docker/containerd/containerd.toml
vagrant   2569  1876  0 12:23 pts/0    00:00:00 grep --color=auto docker




sudo docker run --runtime=runsc --name pgsql -e POSTGRES_PASSWORD=postgres -d postgres
908c5d1b2a88ba159d687207bf38185feebda4e369b17033c951ffa52b77e679
docker: Error response from daemon: OCI runtime create failed: /usr/local/bin/runsc did not terminate sucessfully: unknown.



https://github.com/google/gvisor/issues/55

We require a Linux 3.17+ kernel. It looks like you are on 3.10.

$ cat /proc/version
Linux version 3.10.0-514.10.2.el7.x86_64 (builder@kbuilder.dev.centos.org) (gcc version 4.8.5 20150623 (Red Hat 4.8.5-11) (GCC) ) #1 SMP Fri Mar 3 00:04:05 UTC 2017



