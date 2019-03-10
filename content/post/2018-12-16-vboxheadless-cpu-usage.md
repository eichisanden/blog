+++
thumbnail = "images/vboxheadless-cpu-usage/top.png"
tags = ["Vagrant", "VirtualBox", "MacOS", "CentOS7"]
categories = ["技術メモ"]
date = "2018-12-16T15:50:27+09:00"
title = "VirtualBoxでCentOS7を動かすとCPU使用率が100%になって困った話"
description = "VirtualBoxでCentOS7を動かすとCPUが荒ぶる問題と格闘した話"
+++

VirtualBoxのCentOS7を動かすとCPUが荒ぶって困っていたのですが、根本原因は依然分かっていないものの発生タイミングと対症療法が分かったのでメモを残しておきます。

## 環境

- Macbook Air 2018
- VirtualBox 5.2.22
- Vagrant 2.2.0
- ホストOS: MacOS 10.14.2
- ゲストOS: CentOS 7.6

## 発生した問題

VirtualBox(Vagrant)でCentOS7を立ち上げてしばらくすると、ホストOS上のVBoxHeadlessというプロセスのCPU使用率が100%になるというのが問題。
1コア専有する程度なのでPC自体がフリーズする訳じゃないけど、CPUのファンが唸ってうるさくて堪らない状態になります。

## 試したこと

`VBoxHeadless CPU`でググって色々やってみたけど、結論を先に書くとどれもハズレだった。

[Stack Overflowのポスト](https://stackoverflow.com/questions/28293238/why-does-virtual-box-vboxheadless-process-using-vagrant-use-100-of-my-cpu
)によると、VirtualBox Guest Additionのバージョンが不一致で発生した人がいたので、vagrant-vbguestプラグインを入れてみた。  
どのみち、vagrant-vbguestは入れておいた方が良いので無駄ではないのだが、本件とは関係なかった。

{{% img src="images/vboxheadless-cpu-usage/remote-display.png" w="800" h="590" %}}

次に、VirtualBoxのリモートディスプレイを無効化して改善したという情報を見て試したけど、これも無関係。
もともと有効である必要がなかったし警告も出てたのそのまま無効にしておく。

今度は[このサイト](http://omulettekobo.hatenablog.com/entry/2013/10/04/112836
)を見て、割り当てCPU数はもともと1つだったので、「(1)IO APICを有効化のチェックを外す」、「(3) ネステッドページングを有効化のチェックを外す」を試してみたけど、これも関係なかった。

{{% img src="images/vboxheadless-cpu-usage/ioapic.png" w="800" h="590" %}}
{{% img src="images/vboxheadless-cpu-usage/nestedpaging.png" w="800" h="590" %}}


## 結局

UbuntuとCentOS6のゲストOSを試したところ、CentOS7でしか発生しない問題であることが分かった。  
また、発生するタイミングもPCのスリープからの復帰直後に発生するという法則も分かってきた。  
最初は全く意味が分からなかったので、これが分かっただけでも大きな進歩。  
だがしかし、原因がさっぱり分からないので対症療法でしかないがスリープさせる前にvagrant suspendか vagrant haltでVMを明示的に止めておくことで回避している。  
Macbook Airの旧モデルでCentOS7をVirtualBoxで動かした時はこんな問題起きなかったので、Macbook自体かMojaveに関連する問題なのかな。  
ちゃんとした対応方法が分かったら追記します。
