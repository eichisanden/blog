+++
thumbnail = "images/high_sierra/thumbnail.jpg"
tags = ["Mac"]
categories = ["技術メモ"]
date = "2018-01-10T22:45:27+09:00"
title = "High Sierraへのアップデートで「OSInstall.mpkgは存在ないか破損しているようです」エラー"
+++

いい加減にSierraからHigh Sierraにアップデートしようとしたら、
「OSInstall.mpkgは存在ないか破損しているようです」エラーにハマりました。

{{% img src="images/high_sierra/high_sierra_error.jpg" w="800" h="600" %}}

言われた通りに再起動後に試みるも状況変わらず。  
ネットで調べるとTime Machineから復元して、みたいな話ばかり出てきて、
そもそもバックアップも何もしていないので、クリーンインストールを覚悟しましたが、何とか解決できました。

今までこの手のメッセージが出てきても実際に壊れてたことないので疑いもしませんでしたが、  
メッセージの通りで、High Sierraのインストーラのダウンロードに失敗しているケースがよくあるそうです。  

リカバリモードで起動して、インストーラーの起動ディスクを[ディクスユーティリティでFirst Aid](https://support.apple.com/ja-jp/guide/disk-utility/dskutl1040/mac
)したあとで再チャレンジしたところうまく行きました。  
全てのパターンでうまくいく訳じゃないと思うけど試してみる価値はありそうです。

