+++
date = "2017-05-14T12:56:15+09:00"
draft = false
title = "GitHub Pages/Hugoでブログを作成してみた"
categories = ["技術メモ"]
tags = ["Hugo", "Github", "Github Pages"]
thumbnail = "images/start-blog-with-hugo/hugo.png"
+++

## はじめに

本の感想やポエムなど、少し長めの文章を書く場所が欲しくて、ブログを設置してみました。  
はてなブログを使うのが楽だし、SEO的にも有利そうですが、前々から使ってみたかったGitHub Pagesにブログを作成してみました。  
ブログを書くたびGitHubに草が生えるし、モチベーションのキープに効果がありそう。  
そこでまず、どの静的サイトジェネレータを使うかを検討しました。

- StaticGen https://www.staticgen.com/
- Static Site Generators https://staticsitegenerators.net/

上記サイトで調べてみると、世の中にはものすごい沢山の静的サイトジェネレータがあると分かります。  
自分が知っているのは、Jekyll、Middleman、Hubpress、Sphinxぐらいでした。  
最初はGitHubのスターの数が他を圧倒しているしGitHubが公式に対応しているJekyllにしようと思いましたが、ページ生成が遅いとのことなので速いと評判のHugoを採用しました。

- Hugo https://gohugo.io/

HugoはGoで書かれているため公開されたバイナリを実行するだけで良くて、RubyやNodeの環境準備が不要なのもアドバンテージです。  
サイトをざっと見た感じ、とりあえず一通りのことは出来そうです。

- Themes http://themes.gohugo.io/

使いたいテーマがないと困るので、先にテーマを探しておきます。  
テーマの数はそれほど多くない印象です。  
探したところ、はてブにも対応したRobustというクールなテーマがあったので使用させて頂きました。

Robust http://themes.gohugo.io/robust/

## セットアップ

公式サイトに従ってセットアップしていきます。

### Hugoのインストール

HomebrewでHugoをインストールします。

```
$ brew install hugo
```

### サイトのテンプレート生成

```
$ hugo new site eichisanden.github.io
```

空のフォルダ体系とconfig.tomlファイルが生成されます。

```
.
|-- archetypes
|-- config.toml
|-- content
|-- data
|-- layouts
|-- static
`-- themes

6 directories, 1 file
```

## テーマの設定

先ほど調べておいたテーマをGitHubから取得します。  

```
$ cd themes
$ git clone https://github.com/dim0627/hugo_theme_robust.git
```

テーマのリポジトリを参考に`config.toml`を編集しておきます。  
また、 `hasCJKLanguage = true` を追加しておきます。
これを設定しておかないと、下記イメージのように記事のサマリーが無駄に長く表示されてしまいます。

{{% img src="images/start-blog-with-hugo/hugo_hasCJKLanguage1.png" w="439" h="397" %}}

設定後は適当な長さでぶった切ってくれます。

{{% img src="images/start-blog-with-hugo/hugo_hasCJKLanguage2.png" w="439" h="397" %}}

## 最初のコンテンツ生成

```
$ hugo new post/first_content.md
```

上記コマンドを実行すると、下記のような内容で `content/post/first_content.md` ファイルが生成されます。

```
+++
author = ""
categories = []
date = "2017-05-14T10:57:22+09:00"
description = ""
featured = ""
featuredalt = ""
featuredpath = ""
linktitle = ""
title = "first_content"

+++
```

`+++`で囲まれている部分は `front matter`と呼ばれる設定部分ですので、本文は直後に書いていきます。  
Robustテーマは、`thumbnail = "images/hugo.png"` でサムネイル画像が指定出来ます。  
また、`toc = true` を追加すると目次が表示されます。  

## サーバー起動してプレビュー

内容が書けたらローカルでプレビューしていきます。  

```
$ hugo server --theme=hugo_theme_robust --buildDrafts
```

テーマとドラフトの記事も生成するオプションを指定してHugo Serverを起動すれば http://localhost:1313/ にアクセスしてプレビューが確認できます。  
また、ファイルを編集すると自動的にブラウザがリロードされプレビューに即反映されるのが便利です。  

## ドラフトを解除して公開用にpublicフォルダを生成

プレビューがOKだったらドラフトを解除してpublicフォルダを生成します。

```
$ hugo undraft post/first_content.md
$ hugo --theme=hugo_theme_robust
```

これでpublicフォルダが生成されます。

## Github Pagesで公開

あとはGithubにpushして公開します。  
publicフォルダの中身をGithubにpushして公開します。  

```
$ cd public
$ git init
$ git add --all
$ git commit -m "Initial commit"
$ git remote add origin git@github.com:eichisanden/eichisanden.github.io.git 
$ git push -u origin master
```

publicフォルダ以外のフォルダも別途Gitにコミットしておきます。  
publicとthemesフォルダは除外しておきます。  
この手順が少し面倒に感じました。

```
$ cd ..
$ git init
$ echo "/public/" >> .gitignore
$ echo "/themes/" >> .gitignore
$ git add --all
$ git commit -m "Initial commit"
```

## 最後に

Word Pressのように凝ったことはできませんが、静的ファイルをアップロードしているだけなので高パフォーマンスなブログを作成できて良いと思いました。
