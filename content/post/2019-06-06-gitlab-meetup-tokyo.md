+++
categories = ["イベント"]
date = "2019-06-06T21:00:00+09:00"
description = "2019/6/6に行われたGitLab Meetup Tokyo #17の参加レポートです"
tags = ["GitLab"]
thumbnail = "images/gitlab-meetup-tokyo-no17/title.png"
title = "GitLab Meetup Tokyo #17: GitLab Contribute 2019 recapに行ってきた"
+++

{{% ogp "https://gitlab-jp.connpass.com/event/129245/" %}}

前回に続きブログ枠で参加させてもらいました。

会場は大崎のソニーさんの 24F のイベントスペース BRIDGE TERMINAL でした。  
すぐ暗くなっちゃいましたが、羽田空港の飛行機がよく見える見晴らしの良い会場でした。  
フードスポンサーもソニーさん、ありがとうございます！

## オープニング

- こちらオープニングで流れていた動画。お祭りですね。すごく楽しそうです。

{{< youtube xdtPNXtkBhE >}}

- Slack チャンネルのご紹介

http://gitlab-jp.herokuapp.com/

上記 URL から招待 URL を送れるのでどしどし入ってきて欲しいとのことです。  
ランディングページを Heroku の無料プランで動かしているとのことです。

slackin というのを使っているようですね。

{{% ogp "https://github.com/rauchg/slackin" %}}

- Twitter アカウントのご紹介

GitLab-Tokyo の Twitter アカウント(@GitLabTokyo)を作ったのでフォローよろしくお願いします。

## GitLab Contribute の 2019 に参加した話

by @hiroponz さん

https://docs.google.com/presentation/d/1zZkvmuxtM2hDlbbFO4YNvK43KeF2LIVjeaKfK9NZKYo/preview?slide=id.p

- GitLab Contribute とは？
  - 9 ヶ月に１度、世界中から GitLab の社員と Core Team のメンバーが集合する。
  - 去年までは GitLab Summits という名前だった。
  - GitLab に Contribute すると参加できる。
  - Contribute と言ってもドキュメント修正やブログなどでのナレッジ共有も認められるのでお気軽にどうぞ。

Contribute が認められると参加費（宿泊費・食事代込み）の約 28 万円が無料だし、飛行機代の補助も出してくれる。  
英語は聞き取れなかったけど何とかなった。翻訳アプリが便利だった。  
IPO するそうだが、本社がなくて IPO する会社は初らしい。  
コントリビュートする理由は様々。ドキュメントの間違いが許せなくて修正している英語の先生がいた。

pg_trgm を使っているため、2 文字以下での検索ができない問題に誰か Contribute して欲いとのこと。  
→ 英語では 2 文字以下の検索をしたいニーズが少ないでしょうから、日本人がコントリビュートするチャンスですね。

## The Challenge at Contribute & Sessions

by @tnir さん

{{% speakersdeck id="287dc0ac86564aadaa7e55e53af7a124" ratio="1.77777777777778" %}}

### 参加者

GitLab Contribute への参加者は前回の 350 人から 580 人に増加（前回比 66％アップ）。  
日本からの参加は３名。

### Keynote

リモートワークによって家庭へコミットできるようになり社員の離婚率が下がったといった働き方の話が印象的でした。

{{< youtube kDfHy7cv96M >}}

### The Challenge

毎年、何か課題をあげて期間中にクリアするチャレンジ。
今年はインストールの簡単化をやった。

### Compensation Calculator

GitLab の社員は住んでいる地域よって物価などの格差があるので条件を入れてサラリーが見れるツール。

https://about.gitlab.com/handbook/people-operations/global-compensation/calculator/

適当に検索条件を入れてみたらこんな感じでした。

{{< tweet user="eichisanden" id="1136588547345924096" >}}

次回はおそらく 2020/02。アジアのどこか？

---

ここから LT です。

## ダブル SSO で始める Gitlab Mattermost

by @ymasaoka さん

{{% speakersdeck id="f513d9aeb43d4d8380922e5a78f497a6" ratio="1.44428772919605" %}}

- Mattermost ご存知ですか？
  - Mattermost は Slack クローンな OSS チャットツール
- Svn から Git(GiLab)の移行した時に Mattermost も導入
  - Slack が使えない職場という背景も
- 当初は GitLab <- SSO <- Mattermost の構成
- 突然の AD/LDAP 連携が必須のお達し
- だが Mattermost がダブル SSO に対応していないことが分かる
- GitLab に同梱の Mattermost でならできた

話を聞いていて Mattermost を直接 LDAP に繋げば良いのではと思ったんですが、
EE 版じゃないと LDAP 連携をサポートしてないんですね。知らんかったです。
https://docs.mattermost.com/deployment/sso-ldap.html

## GItLab CI で簡単コンテナ開発

by @tempacct032419 さん

https://gitlab.com/mrman/talks/raw/master/dist/2019/06/gitlab-tokyo-meetup/gitlab-ci-and-docker-containers.pdf

- コンテナとは何か？
- パイプラインでコンテナビルド
- GitLab は Docker レジストリーが無料で使えたり、イメージのスキャンができる。
- GItLab CI のサービスで Kubernetes や Mesos のデプロイできたりめちゃくちゃ便利。

## 「Gitlab+Rancher による Devops」

by @cheng さん

{{% speakersdeck id="98991afb4e5a457ea982ca926201b283" ratio="1.77777777777778" %}}

- 画面操作だけで GitLab+Lancher により CI/CD したい。
- GitLab でソースコードと Docker レジストリを管理して、CI/CD は Lancher で行う構成。
- Rancher で Kubernetes のクラスタを簡単に構築できる。
- パイプラインの実行は Rancher の Jenkins を使用している。
- Pipeline の分岐制御はまだ弱いので複雑な分岐は難しいとのこと。
- SonarCube や Nexus も画面からセットアップ可能。

## スポンサー LT

{{% speakersdeck id="ff66112f0fe34cd88c550835e4c439da" ratio="1.77777777777778" %}}

GitLab CE にガントチャートが欲しい。
GanttLab というガントチャートがあるが、まだ触ってない。

{{% ogp "https://www.ganttlab.org/" %}}

Mermaid が GitLab10.3 でサポートされた。
https://docs.gitlab.com/ee/user/markdown.html#mermaid

{{% ogp "https://about.gitlab.com/2017/12/22/gitlab-10-3-released/#flow-charts-sequence-diagrams-and-gantt-diagrams-in-gitlab-flavored-markdown-gfm-with-mermaid" %}}

{{% ogp "https://github.com/knsv/mermaid" %}}

GitLab Issue を API で読み込んでガントチャートを書いてみた。
必要な情報は Description に書いている。GanttLab も同じようなことをやっているようです。

## おわりに

今回も面白い話をたくさん聴けましたし、ブログにまとめることで良い復習となりました。
資料が公開されたら追記するつもりです。  
**2019/06/13** すベての資料が公開されたので記事にリンクを追加しました。
