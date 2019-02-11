+++
thumbnail = "images/postman/title.png"
tags = ["API","チーム開発"]
categories = ["技術メモ"]
date = "2019-02-11T23:00:00+09:00"
title = "PostmanでAPIのテストをチーム内で共有する（アカウントを作らない方法）"
description = "APIの実行をcurlコマンドからPostmanに切り替えてチームに共有する話（アカウントを作らない方法）"
+++

APIのテストなんて好きな方法でやれば良いと思っているのですが、
メンバーが急激に増えたのと、multipart/form-dataでファイルをアップロードするAPIの叩き方はみんな意外と悩むようなので実行方法をチームに共有することになりました。  
curlコマンドの実行サンプルを共有しても良いのですが、うちのチームではGUIのツールの方が都合が良さそうだなと思って、この週末にPostmanの設定を共有する方法を調べていました。  
とりあえずPostmanのアカウントを作らない方法で検討してみました。  

### Postman  
https://www.getpostman.com/  
https://learning.getpostman.com/

## curlコマンドをインポートしてRequest設定を作る

Import->Paste Raw Textに、curlコマンドをコピペすることでRequest設定が作成出来るようです。  
試しに先週紹介したjsonplaceholderのAPIを叩くcurlコマンドを貼り付けてみます。  
（ちなみに、レスポンスヘッダーを出力する-iオプションが付いているとエラーになるので注意してください）

{{% img src="images/postman/import_curl1.png" w="400" h="451" %}}

インポートするとcurlを元にいい感じにRequest設定が作られました。  
これはHeader部ですが、-Hオプションに設定した値がちゃんとセットされています

{{% img src="images/postman/import_curl2.png" w="800" h="255" %}}

Body部です。こちらも-dオプションの値が設定されています。

{{% img src="images/postman/import_curl3.png" w="800" h="255" %}}

既存のcurlコマンドは無駄にならずに活用できて大分楽に設定が作れそうです。

## Environmentでサーバーごとの設定を変数にする

例えば接続先のサーバーごとにRequest設定を作成すると管理が大変になるので、ホスト名やAPIトークンなどは変数にして無闇にRequest設定を増やさない方が良いと思います。  
New -> Environment で環境（変数を束ねる単位だと思っています）を作成できますので作っていきます。
ここでは、試しにtestという環境を作り、変数としてservrerを追加しました。

{{% img src="images/postman/environment.png" w="600" h="453" %}}

変数は `{{変数名}}` の形式で参照できます。右上のプルダウンで環境を切り替えることで変数に設定される値を変えることが出来ます。

{{% img src="images/postman/environment2.png" w="800" h="263" %}}

少なくともテスト環境とプロダクションでは変数を分けておこうかと思います。

## 設定の共有方法

共有方法を2つの紹介します。

### 設定を丸ごと共有する

Settings -> Export data からCollectionsやEnvironmentの設定を丸ごとエクスポートして、
Settings -> Import data で他のPCで環境が再現できます。  
丸ごとと言ってもCollection, Environment, Global、 Header presetsがExport対象なようです。  
ExportしたJSONファイルをリポジトリで管理することで設定をシェア出来そうです。  

{{% img src="images/postman/export_setting.png" w="600" h="419" %}}

2回目以降のImportはCollectionをReplaceを選択すれば上書き更新されます。

{{% img src="images/postman/import_setting.png" w="600" h="419" %}}

ただし、Environmentは上書きしてくれず増殖してしまいます。不要なものは削除すれば良いのですが数が多いと削除するのが大変かもしれません。あと`My Workspace - globals`というのも突然現れますがひとまず気にしないことします。

{{% img src="images/postman/import_setting2.png" w="600" h="454" %}}

### CollectionとEnvironmentを個別にExport、Importする

今度はCollectionとEnvironnmentを個別に選択してExportする方法です。  
まず、CollectionをExportしてみます。

{{% img src="images/postman/export_collection.png" w="300" h="572" %}}

出力したファイルは、Import -> Import Fileから取り込むことが出来ます。

{{% img src="images/postman/import_collection.png" w="500" h="508" %}}

Replaceを選べば上書き更新できます。

{{% img src="images/postman/import_collection2.png" w="500" h="205" %}}

また、Environmentは`↓`アイコンからExport、ImportからImportすることが出来ます。

{{% img src="images/postman/import_environment.png" w="600" h="454" %}}

## おわりに

Environmentが上書き更新できないのが辛いですが、初回は設定を丸ごとインポート、更新はCollectionsやEnvironmentを個別にインポートしてもらうのが良さそうな気がしています。  
また、管理が煩雑になるのでCollectionやEnvironmentは必要以上に増やさない方が良さそうです。  
ひとまず今回のやり方でも設定は共有出来そうですが、次はアカウントを作る方法でも検討してみようかと思っています。  
時間切れなのでまた次回以降に。
