+++
categories = ["技術メモ"]
date = "2017-06-14T13:44:07+09:00"
draft = false
tags = ["GitLab", "GitLab Pages", "GitHub", "GitHub Pages", "Let' Encrypt"]
thumbnail = "images/gitlab_pages2.png"
title = "GitHub PagesからGitLab Pagesに移行してみた"

+++

# GitHub PagesからGitLab Pagesに移行してみた

なぜだかCloud Flareで発行した証明書から自分のドメインが消えてしまうという謎の事象を解決できなかったのもあり、自前のSSL/TLS証明書を設定できるGitLab Pagesにブログを移行した。  
基本的な設定は置いておいて、独自ドメインの設定とSSL/TLS証明書の設定方法のメモ。

## DNSの設定

お名前.comの管理画面でDNSのAレコードをGit LabページのIPアドレス（52.167.214.135）に向けておく。

{{% img src="images/gitlab_pages1.png" w="260" h="127" %}}


## Let's EncryptでSSL/TLS証明書を設定する

Let's Encryptをダウンロードして証明書を発行していきます。  

```
$ git clone https://github.com/letsencrypt/letsencrypt
$ cd letsencrypt
$ ./letsencrypt-auto certonly --agree-tos -a manual -d gyoza.beer
FATAL: macOS support is very experimental at present...
if you would like to work on improving it, please ensure you have backups
and then run this script again with the --debug flag!
Alternatively, you can install OS dependencies yourself and run this script
again with --no-bootstrap.
```

MacはExperimentalらしく、初回コマンド実行時はエラーになるので、`--debug`オプションをつけて実行する。  
（3回目からはオプションなくても良いみたい）

```
$ ./letsencrypt-auto certonly --agree-tos -a manual -d gyoza.beer
```

コマンドの実行結果の中に以下のようなのが出力される。後で指定されたURLで値を返すように設定しないといけないのでメモっておく。

```
Make sure your web server displays the following content at
http://gyoza.beer/.well-known/acme-challenge/MVcYbRe68R9_hMc8hGGl1bGh3dOXP0qjYf9vO8D8M40 before continuing:

MVcYbRe68R9_hMc8hGGl1bGh3dOXP0qjYf9vO8D8M40.6w_jWE2mP9CxdvJca7dEsHUCF_JEu2f5uP3ZLVuW3hg
```
# GitLabにSSL/TLS証明書を設定

発行された証明書をGitLab PagesのSettings->Pagesから設定します。  

```
$ sudo cat /etc/letsencrypt/live/gyoza.beer/fullchain.pem | pbcopy
$ sudo cat /etc/letsencrypt/live/gyoza.beer/privkey.pem | pbcopy
```

それぞれCertificate(PEM), Key(PEM)に貼り付けます。

# .gitlab-ci.ymlの設定

[Gitlab PagesのHugoのサンプルプロジェクト](https://gitlab.com/pages/hugo)の.gitlab-ci.ymlを参考に作成します。  
先ほど証明書生成時にのログに出てきたacme-challengeのファイルを作成して、public下の所定のフォルダにコピーするようにしてみました。  

```yml
image: alpine

before_script:
  - bin/hugo version

test:
  script:
  - bin/hugo
  except:
  - master

pages:
  script:
  - bin/hugo
  - mkdir -p public/.well-known/acme-challenge
  - cp content/.well-known/acme-challenge/MVcYbRe68R9_hMc8hGGl1bGh3dOXP0qjYf9vO8D8M40 public/.well-known/acme-challenge
  artifacts:
    paths:
    - public
  only:
  - master
```

これで移行できました。  
ただ、GitLab Pageは独自にHTTPヘッダーを記述できていないので、以前に設定したHSTSが効かなくなってしまいました。  
GitLabにIssueが上がってたので、+1しときました。そのうち対応してくれないかな。
