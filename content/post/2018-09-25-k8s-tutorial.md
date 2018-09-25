+++
thumbnail = "images/k8s-tutorial/dashboard.png"
categories = ["技術メモ"]
tags = ["Kubenetes", "Docker"]
title = "知識ゼロで Kubenates Tutorialをやってみた"
date = "2018-09-25T20:37:07+09:00"
+++

最近めちゃくちゃ良く聞くようになり、知らないと話についてけないので調べてみました。  
Webを見てるだけだと良く分からなかったので公式のTutorialをやってみることにしました。

- Tutorials - Hello Minikube  
https://kubernetes.io/docs/tutorials/hello-minikube/


Tutorialの内容は、Node.jsのHello worldアプリをDockerイメージに落としてK8sで動かすものです。
k8sといってもMinikubeという簡易版を使ってローカルPCで動かすようですが。  

## セットアップ

このTutrialにはDocker for Macも必要ですが、すでに入っているので割愛。

まず、Home brewでMiniCubeをインストールします。

```
$ brew cask install minikube
```

続いて[手順](https://github.com/kubernetes/minikube/blob/master/docs/drivers.md#hyperkit-driver)に従い、HyperKit driverをインストールします。


最後にkubectlをインストールします。

```
$ brew install kubernetes-cli
```

## Minikube クラスタの起動

`minikube start` でMinKubeを起動します。`--vm-driver`オプションでhyperkitを指定します。

```
$ minikube start --vm-driver=hyperkit
Starting local Kubernetes v1.10.0 cluster...
Starting VM...
Downloading Minikube ISO
 160.27 MB / 160.27 MB [============================================] 100.00% 0s
Getting VM IP address...
Moving files into cluster...
Downloading kubeadm v1.10.0
Downloading kubelet v1.10.0
Finished Downloading kubelet v1.10.0
Finished Downloading kubeadm v1.10.0
Setting up certs...
Connecting to cluster...
Setting up kubeconfig...
Starting cluster components...
Kubectl is now configured to use the cluster.
Loading cached images from config file.
```

Turorialに従い、Contextを切り替えます。
`~/.kube/config`を見るとどうやら`minikube`というcontextが勝手に登録されてるようなのでそれを使います。

実行すると問題が発生。
```
$ kubectl config use-context minikube                                                        
failed MSpanList_Insert 0xc0b000 0x148027ce3801 0x0
fatal error: MSpanList_Insert
```

ググると、私の環境に入っているGoogle Cloud SDKが古いせいみたいなので、`gcloud components update` した後でやったらうまく行きました。

```
$ kubectl config use-context minikube                                                                               
Switched to context "minikube".
```

`kubectl cluster-info`を叩くと、Kubenetes masterとKubeDNSというのが動いているようです。

```
$ kubectl cluster-info
Kubernetes master is running at https://192.168.64.2:8443
KubeDNS is running at https://192.168.64.2:8443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.
```

まだ全く使いこなせませんが、`minikube dashboard` でダッシュボードが開けるようです。メモリは1.5GBぐらい食っていて貧弱な私のPCでは辛いです

{{% img src="images/k8s-tutorial/dashboard.png" w="1242" h="748" %}}


## サンプルアプリケーションの作成

NodeでHello Worldするだけのアプリを作成します。server.jsというファイル名で保存します

```server.js
var http = require('http');

var handleRequest = function(request, response) {
  console.log('Received request for URL: ' + request.url);
  response.writeHead(200);
  response.end('Hello World!');
};
var www = http.createServer(handleRequest);
www.listen(8080);
```

TutorialにしたがってDockerfileも作成します。

```Dockerfile
FROM node:6.9.2
EXPOSE 8080
COPY server.js .
CMD node server.js
```

次に、docker buildしてイメージを作成します。

```
$ docker build -t hello-node:v1 .
```

出来ました。`docker images`の一番上が今ビルドしたものですが、関連して沢山のimageが取得されていることが分かります。

```
$ docker images                                                                                                                            REPOSITORY                                 TAG                 IMAGE ID            CREATED             SIZE
hello-node                                 v1                  479cfa661a95        8 hours ago         655MB
k8s.gcr.io/kube-proxy-amd64                v1.10.0             bfc21aadc7d3        6 months ago        97MB
k8s.gcr.io/kube-controller-manager-amd64   v1.10.0             ad86dbed1555        6 months ago        148MB
k8s.gcr.io/kube-apiserver-amd64            v1.10.0             af20925d51a3        6 months ago        225MB
k8s.gcr.io/kube-scheduler-amd64            v1.10.0             704ba848e69a        6 months ago        50.4MB
k8s.gcr.io/etcd-amd64                      3.1.12              52920ad46f5b        6 months ago        193MB
k8s.gcr.io/kube-addon-manager              v8.6                9c16409588eb        7 months ago        78.4MB
k8s.gcr.io/k8s-dns-dnsmasq-nanny-amd64     1.14.8              c2ce1ffb51ed        8 months ago        41MB
k8s.gcr.io/k8s-dns-sidecar-amd64           1.14.8              6f7f2dc7fab5        8 months ago        42.2MB
k8s.gcr.io/k8s-dns-kube-dns-amd64          1.14.8              80cc5ea4b547        8 months ago        50.5MB
k8s.gcr.io/pause-amd64                     3.1                 da86e6ba6ca1        9 months ago        742kB
k8s.gcr.io/kubernetes-dashboard-amd64      v1.8.1              e94d2f21bc0c        9 months ago        121MB
gcr.io/k8s-minikube/storage-provisioner    v1.8.1              4689081edb10        10 months ago       80.8MB
node                                       6.9.2               faaadb4aaf9b        21 months ago       655MB
```

## サンプルアプリケーションの起動

`kubectl run`でコンテナを起動します。`--image-pull-policy=Never`とすることでローカルのイメージだけが使用されます。ただ、なぜかエラーになりました。

```
$ kubectl run hello-node --image=hello-node:v1 --port=8080 --image-pull-policy=Never
error: failed to discover supported resources: Get https://192.168.64.2:8443/apis/apps/v1beta1?timeout=32s: http2: no cached connection was available
```

単純にもう一度実行したら正常に起動しました。何でだろう。

```
$ kubectl run hello-node --image=hello-node:v1 --port=8080 --image-pull-policy=Never
deployment.apps/hello-node created
```

## 確認コマンド色々

Podはコンテナを束ねる単位で、DeploymentはPodを管理するもののようですが、確認しろというのでそれぞれコマンドを叩いて状態を確認してみます

```
$ kubectl get pods                                                                                                                         NAME                          READY     STATUS    RESTARTS   AGE
hello-node-57c6b66f9c-lz4ll   1/1       Running   0          1m
```

```
$ kubectl get deployments                                                                                                                  NAME         DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
hello-node   1         1         1            1           53s
```

`kubectl get events` で内部のイベントを確認できるそうです。

```
$ kubectl get events
LAST SEEN   FIRST SEEN   COUNT     NAME                                           KIND         SUBOBJECT                     TYPE      REASON                  SOURCE                  MESSAGE
3m          3m           1         hello-node-57c6b66f9c-lz4ll.15572ff0e4dd83b8   Pod                                        Normal    Scheduled               default-scheduler       Successfully assigned hello-node-57c6b66f9c-lz4ll to minikube
3m          3m           1         hello-node-57c6b66f9c.15572ff0d9febff2         ReplicaSet                                 Normal    SuccessfulCreate        replicaset-controller   Created pod: hello-node-57c6b66f9c-lz4ll
3m          3m           1         hello-node.15572ff0cfc21ad7                    Deployment                                 Normal    ScalingReplicaSet       deployment-controller   Scaled up replica set hello-node-57c6b66f9c to 1
3m          3m           1         hello-node-57c6b66f9c-lz4ll.15572ff114eaea55   Pod                                        Normal    SuccessfulMountVolume   kubelet, minikube       MountVolume.SetUp succeeded for volume "default-token-jd4sd" 
2m          2m           1         hello-node-57c6b66f9c-lz4ll.15572ff2b28df380   Pod          spec.containers{hello-node}   Normal    Pulled                  kubelet, minikube       Container image "hello-node:v1" already present on machine
2m          2m           1         hello-node-57c6b66f9c-lz4ll.15572ff2dc1480b1   Pod          spec.containers{hello-node}   Normal    Created                 kubelet, minikube       Created container
2m          2m           1         hello-node-57c6b66f9c-lz4ll.15572ff32abf3df6   Pod          spec.containers{hello-node}   Normal    Started                 kubelet, minikube       Started container
```

最初の方で、`~/.kube/config`を参照しましたが、コマンドでも見れるようです

```
$ kubectl config view
apiVersion: v1
clusters:
- cluster:
    certificate-authority: /Users/foo/.minikube/ca.crt
    server: https://192.168.64.2:8443
  name: minikube
contexts:
- context:
    cluster: minikube
    user: minikube
  name: minikube
current-context: minikube
kind: Config
preferences: {}
users:
- name: minikube
  user:
    client-certificate: /Users/foo/.minikube/client.crt
    client-key: /Users/foo/.minikube/client.key

```

## サービスの有効化
`kubectl get services`するとサンプルアプリが出てこない。

```
$ kubectl get services                                                                                                                                                                          9:59:28
NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   12h
```

`kubectl expose`することで初めて使えるようになるようです。

```
$ kubectl expose deployment hello-node --type=LoadBalancer
service/hello-node exposed

$ kubectl get services                                                                                                                                                                         10:01:54
NAME         TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
hello-node   LoadBalancer   10.100.26.53   <pending>     8080:31578/TCP   48s
kubernetes   ClusterIP      10.96.0.1      <none>        443/TCP          12h
```

## アプリを動かしてみる

`minikube service hello-node`とすることで、ブラウザでアプリを開けます。

```
$ minikube service hello-node
```

{{% img src="images/k8s-tutorial/hello-node.png" w="764" h="427" %}}

## ログの確認

`kubectl logs <Pod名>`でログを確認できます。`console.log`したものが見れるようです

```
$ kubectl logs hello-node-57c6b66f9c-lz4ll
Received request for URL: /
Received request for URL: /favicon.ico
```

## サンプルアプリケーションのバージョンアップ

今度はアプリのバージョンアップについて試して行きます。
server.jsで表示しているメッセージを書き換えます。

```server.js

<    response.end('Hello World!');
---
>    response.end('Hello World Again!');
```

v2としてDockerビルドします

```
$ docker build -t hello-node:v2 .                                                              
```

`kubectl set image` でイメージを更新します。

```
$ kubectl set image deployment/hello-node hello-node=hello-node:v2
deployment.extensions/hello-node image updated
```

`minikube service hello-node` でブラウザを開くと変更が反映されています。  まあ簡単。

{{% img src="images/k8s-tutorial/hello-node2.png" w="752" h="551" %}}

## 後始末

Serviceを削除します

```
$ kubectl delete service hello-node
 service "hello-node" deleted

$ kubectl get services                                                                                      NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   13h
```

Deploymentを削除します

```
$ kubectl delete deployment hello-node                                                                                                                                                         deployment.extensions "hello-node" deleted
```

Dockerイメージも不要なので削除

```
$ docker rmi hello-node:v1 hello-node:v2 -f
```

Minikubeを停止

```
$ minikube stop
Stopping local Kubernetes cluster...
Machine stopped.
```

ただ初回はしばらく待った挙句エラーになり停止できませんでした。単純にリトライしたら何事もなく停止しましたが、何だろう。

```
$ minikube stop                                                                                                                                                                                Stopping local Kubernetes cluster...
Error stopping machine:  Error stopping host: minikube: Maximum number of retries (60) exceeded
```

環境変数も消しておきます
```
$ eval $(minikube docker-env -u)
```

Minikube VMも削除しておきます

```
$ minikube delete                                                                                                                                                                              
Deleting local Kubernetes cluster...
Machine deleted.
```
## おわりに

- hyperkitが1.6GB、com.docker.hyperkitが1.5GBと、メモリ消費が激しいので私のMacBook Air 2013（メモリ4GB）では辛かった。
  - MacBook Pro タッチバーなしメモリ沢山モデル、頼む出してくれ
- クラウドで動くイメージだったけど開発環境はローカルで作れるみたい
  - ローカルと本番の環境差異が少なそうなので、それは良さげ
  - ただ、よく分からないエラーも発生したので実際に使う時つらいかも
- 大まかな構成は理解できたものの、複数コンテナ扱う場合の起動順とか、複雑なことはこれから
  - 会社に転がってた本を借りてきたので、足りない知識は補っていく

{{% amazon
  itemId="4297100339"
  title="Docker/Kubernetes 実践コンテナ開発入門"
  author="山田 明憲 (著)"
  publisher="技術評論社"
  imageUrl="https://images-fe.ssl-images-amazon.com/images/I/51d69eJQMyL._SL160_.jpg"
%}}

とりあえず雰囲気は少し掴んだので、話に少しはついていけそう。
