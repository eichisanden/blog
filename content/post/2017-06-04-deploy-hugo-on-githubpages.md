+++
categories = ["技術メモ"]
date = "2017-06-04T10:18:59+09:00"
draft = false
tags = ["Github", "Github Pages", "Travis.ci"]
thumbnail = "images/docs-desktop.svg"
title = "Github Pages/HugoのブログをTravice.ciでデプロイする"
toc = true
+++

## Hugoのビルド

go getでHugoを毎回取得しても良いのですが、以下の理由でバイナリをコミットすることにしました。

- 取得するHugoのバージョンの制御が難しい
  - （リリース前の変更もmasterにコミットされるようなので、少し枯れたバージョンを使いたい）
- Goのメリット、バイナリファイルを1つ置けば済む
- 毎回取得しないのでビルドが速い

## Github Pagesへのデプロイ

https://docs.travis-ci.com/user/deployment/pages/

上記に従って`.travis.yml`を書いていきました。

```
script:
  - bin/hugo
deploy:
  provider: pages
  skip_cleanup: true
  github_token: $GITHUB_TOKEN
  local_dir: public
  target_branch: master
  fqdn: gyoza.beer
  repo: eichisanden/eichisanden.github.io
  on:
    branch: master
```

hugoのビルド結果がpublicフォルダに出力され、repoで指定したリポジトリにプッシュされます。  
`$GITHUB_TOKEN`は、GithubでPersonal access tokenを発行し、Travis.ciの環境変数に値を登録しました。

{{% img  src="images/deploy-hugo-on-githubpages1.png" w="800" h="524" %}}

注意点としは、毎回 `git push -f` するので、出力先リポジトリの履歴が残らないことです。  
履歴はソースの方で追えば良いので、特に問題はないと思いました。
