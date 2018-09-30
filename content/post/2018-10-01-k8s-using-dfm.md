+++
draft = true
thumbnail = "images/docker4mac-k8s/setting-top.png"
categories = ["技術メモ"]
tags = ["Kubernetes", "Docker"]
date = "2018-09-28T18:06:53+09:00"
title = "Docker for MacでKubernetesを使う"
description = ""
+++

先週、Minikubeを使ってローカル開発環境を作ったが、Docker for Macにはk8が同梱されているようなので試してみました。  

## 実行環境
MacOS High Sierra(10.13.6)  
Docker: 18.06.1  
Kubernetes: 1.10.3

## 設定

Preferencesを開くとKubernetsタブがあります。  
`Enable Kubernetes`にチェックしてApplyを押します
{{% img src="images/docker4mac-k8s/setting1.png" w="612" h="499" %}}

インストールするようなので、しばらく待ちます...結構待ちました
{{% img src="images/docker4mac-k8s/setting2.png" w="612" h="499" %}}

無事起動しました。右下に`Kubernetes is running`と表示されてます
{{% img src="images/docker4mac-k8s/setting3.png" w="612" h="499" %}}

## kubernetes-cliは消しておこう

後から気づいたのですが、`kubectl`コマンドがインストールされるはずなんですが、Homebrewでインストールされるとそれが残ってしまうようです  
```
$ ll  $(which kubectl)
lrwxr-xr-x  1 user  admin    43B  9 23 21:22 /usr/local/bin/kubectl -> ../Cellar/kubernetes-cli/1.11.3/bin/kubectl
```

バージョンが1.11が入ってました
```
$ /usr/local/bin/kubectl version
Client Version: version.Info{Major:"1", Minor:"11", GitVersion:"v1.11.3", GitCommit:"a4529464e4629c21224b3d52edfe0ea91b072862", GitTreeState:"clean", BuildDate:"2018-09-10T11:44:36Z", GoVersion:"go1.11", Compiler:"gc", Platform:"darwin/amd64"}
Server Version: version.Info{Major:"1", Minor:"10", GitVersion:"v1.10.3", GitCommit:"2bba0127d85d5a46ab4b778548be28623b32d0b0", GitTreeState:"clean", BuildDate:"2018-05-21T09:05:37Z", GoVersion:"go1.9.3", Compiler:"gc", Platform:"linux/amd64"}
```

こっちがDocker同梱のコマンド。1.10でした
```
/Applications/Docker.app/Contents/Resources/bin/kubectl version
Client Version: version.Info{Major:"1", Minor:"10", GitVersion:"v1.10.3", GitCommit:"2bba0127d85d5a46ab4b778548be28623b32d0b0", GitTreeState:"clean", BuildDate:"2018-05-21T09:17:39Z", GoVersion:"go1.9.3", Compiler:"gc", Platform:"darwin/amd64"}
Server Version: version.Info{Major:"1", Minor:"10", GitVersion:"v1.10.3", GitCommit:"2bba0127d85d5a46ab4b778548be28623b32d0b0", GitTreeState:"clean", BuildDate:"2018-05-21T09:05:37Z", GoVersion:"go1.9.3", Compiler:"gc", Platform:"linux/amd64"}
```

シンボリックリンクを貼り直せば済むのかもしれませんが、`Enable Kubernetes`を OFF -> ON でKubernetsを入れ直したところ事なきを得ました
```
$ ll $(which kubectl)
lrwxr-xr-x  1 user  staff    55B  9 30 22:36 /usr/local/bin/kubectl -> /Applications/Docker.app/Contents/Resources/bin/kubectl
```

### Minikube Tutorialのアプリを動かしてみる

docker-for-desktopというcontextが増えているので切り替えます
```
$ kubectl config get-contexts
CURRENT   NAME                 CLUSTER                      AUTHINFO             NAMESPACE
          docker-for-desktop   docker-for-desktop-cluster   docker-for-desktop
*         minikube             minikube                     minikube

$ kubectl config use-context docker-for-desktop
Switched to context "docker-for-desktop".
```

cluster-infoを見てみると、localhostでmasterとKubeDNSで動いているようで、Minikubeの時は192.168.64.2だったので微妙な違いがありました
```
$ kubectl cluster-info
Kubernetes master is running at https://localhost:6443
KubeDNS is running at https://localhost:6443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.
```

前回作ったサンプルアプリを起動し、exposeします
```
$ kubectl run hello-node --image=hello-node:v1 --port=8080 --image-pull-policy=Never
deployment.apps/hello-node created

$ kubectl expose deployment hello-node --type=LoadBalancer
service/hello-node exposed
```

Minikubeのときは `minikube service hello-node` でブラウザを開きましたが、
どうするんだろう？と一瞬悩みましたが、直接`http://localhost:8080/` で何事もなく開けました

{{% img src="images/docker4mac-k8s/application.png" w="656" h="384" %}}

## 所感
- Minikubeとの使い分けが分かってないが、MacかWindowsで開発することが多いので気軽に使えて良さそう
  - 使いたいaddonが出てきたらMinkube使いたくなるのかな...
- 使っているDockerに対応したバージョンのk8sがダウンロードされるので、バージョン違いによる変なハマり方をしなくて済みそう
- docker stack でKubernetesと連携できるみたいなので今度試してみよう

＜参考＞  
https://docs.docker.com/docker-for-mac/#kubernetes  
https://blog.alexellis.io/docker-for-mac-with-kubernetes/