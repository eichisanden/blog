+++
thumbnail = "images/ansible-awx/title.png"
tags = ["Ansible"]
categories = ["技術メモ"]
date = "2018-12-03T11:32:07+09:00"
title = "Ansible AWXのインストールから基本的な実行まで"
description = "Ansible AWXのインストールから基本的な実行まで試したみた"
+++

## Ansible AWXとは？

エンタプライズ向けの位置付けであるAnsible Towerのアップストリーム版という位置付け。  
Web上でプレイブックを管理したり実行したり、APIでアクセスしたりを提供してくれる。

- Ansible AWX  
https://github.com/ansible/awx

- ライセンス  
Apache v2

## 調べてみたモチベーション

管理するサーバが増えてきて何かと作業が面倒なので、Ansibleで一気にやりたいのですがチームで使うに当たってはクリアしたおきたい問題があります。

- 個人環境のセットアップは面倒なので（特に手順化とか）実行環境を共有したい
- いつ誰が何を実行したか分かるようにしたい（可視化）
- 実行されては困るものは権限で制御したい

ここらへんをJenkinsを使う場合よりスマートに解決できるのではと思い試してみました

## 試した環境

- Ansible AWX 2.1.0  
- Ansible 2.7.2  
- CentOS 7.5  
- Docker 1.13.1  
- docker-compose 1.23.1  

## インストール

CentOS7.5にdockerでインストールしてみました

まずAnsibleをインストールします
```
$ sudo yum install -y epel-release
$ sudo yum install -y ansible
```

AWSのリポジトリをcloneします。執筆時点で最新の2.1.0のタグをチェックアウトしました。

```
$ git clone https://github.com/ansible/awx.git
$ cd awx
$ git branch
* devel
$ git checkout -b 2.1.0 tags/2.1.0
```

`installer/inventory`ファイルに設定が書かれているのですが、PostgreSQLのDATAフォルダが何故か/tmp下なので場所を変更しました。あと、docker composeを使用するため、`use_docker_compose=true`を設定します。

```
$ cd installer
$ sed -i s@postgres_data_dir=/tmp/pgdocker@postgres_data_dir=/var/lib/pgsql/data@ inventory
$ sed -i 's/# use_docker_compose=false/use_docker_compose=true/' inventory
```

さすがAnsbileのプロダクトなので、インストールはansible-playbookで実行して行います。
```
$ ansible-playbook -i inventory --become --become-pass install.yml
```

セットアップが終わるまでしばらく待ちましょう。

{{% img src="images/ansible-awx/updating.png" w="500" h="487" %}}

セットアップが完了するとログイン画面が表示されます。デフォルト設定のままならadminユーザはパスワード=passwordでログインできます。

{{% img src="images/ansible-awx/login.png" w="500" h="391" %}}

ログインするとダッシュボード画面に遷移します

{{% img src="images/ansible-awx/dashboard.png" w="800" h="388" %}}

## Organizationの登録

まずOrganizationを登録します。こだわりなければDefaultというのが登録されているのでそれを使っても良いのかもしれません。  
ちなみに、全体的にこんな感じに上下に分割されている独自なUIなんですが私は馴染めずに使いにくいと感じました。

{{% img src="images/ansible-awx/organization.png" w="800" h="388" %}}

## Teamの登録

Organizationの下にTeamを登録可能

{{% img src="images/ansible-awx/team.png" w="800" h="388" %}}

## Projectの登録

Projectを登録します。SCMにはAnsible Towerのサンプルのプロジェクトを登録しました。
https://github.com/ansible/ansible-tower-samples

{{% img src="images/ansible-awx/project.png" w="800" h="388" %}}

## Inventryの登録
Inventryを作成します。テストとしてホストを1つ登録しました。またインスタンスグループはデフォルトで入っているtowerを選択。

{{% img src="images/ansible-awx/inventry.png" w="800" h="388" %}}

## Credentialの登録
認証情報を登録します。認証情報タイプ=マシンを選択してSSHの鍵を登録しました。

{{% img src="images/ansible-awx/credential.png" w="800" h="388" %}}

## Templateの登録

現在のバージョンは日本語処理にバグがあり、Credentialの選択でエラーになります。  
将来的に直ると思いますが、ブラウザの言語設定で英語に変更して英語表示に切り替えることで回避できます。

{{% img src="images/ansible-awx/credential_error.png" w="500" h="377" %}}

https://github.com/ansible/awx/issues/2231

登録したInventry、Project、Playbook、Credentialを指定して登録します。

{{% img src="images/ansible-awx/template.png" w="800" h="388" %}}

Template画面のロケットアイコンで実行できます
{{% img src="images/ansible-awx/job1.png" w="800" h="388" %}}
{{% img src="images/ansible-awx/job2.png" w="800" h="388" %}}

## まとめ
今後、Dynamic Inventory、複数のPlaybookでワークフローを組む機能など応用的な機能も触ってみたい。
ただ、日本語の処理にバグがあったり発展途上な印象なので引き続き評価していこうと思います。


