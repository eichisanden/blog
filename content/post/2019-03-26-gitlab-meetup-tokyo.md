+++
thumbnail = "images/gitlab-meetup-tokyo/title.jpg"
tags = ["GitLab", "DevOps", "ChatOps"]
categories = ["イベント"]
date = "2019-03-26T19:07:22+09:00"
title = "GitLab Meetup Tokyo #13: ChatOpsに行ってきた"
description = ""
+++

GitLabを仕事で使っているし、社内でChatBotを運用しているので興味があったので[GitLab Meetup Tokyo #13: ChatOps](https://gitlab-jp.connpass.com/event/124425/)に参加してきました。

ブログ枠で申し込んでませんが勝手にブログを書きます！  
（ブログ枠で申し込もうかなと思ったのですが一般枠に空きがあったのでついそちらを申し込んでしまいました）

## GitLabのトレンド @tnir

GitLabはDevOps Tool CahinからToolへと進化している。
いろんなツールを組み合わせる必要はなくGitLabだけで完結できるようになってきている。  
→専用のツールの方が単品で見ると優れていることが多いのではと思っていますが、導入の手軽さと1つにパッケージングされているところで優位性はあるかもなと聞いていて思いました。

- 2019年のDevOpsを支えるCI/CD動向

こちらの記事をベースにお話をされていました。
https://gihyo.jp/dev/column/newyear/2019/devops

GAになったのが去年だそうですがAutoDevOpsという機能は知りませんでした。
ソースをプッシュしてマージリクエストを取り込むと、ビルド、テスト、デプロイなどを自動でやってくれるものだそうです。静的なセキュリティテストやコンテナイメージセキュリティ診断なんかも出来るそうでちょっと使ってみたいと思いました。

- GitLab Hackathon Q1 2019

Issueを消化するハッカソンが開催され、ソースがマージされるとグッズがもらえたそうです。

- GitLab Women

過去2回開催された女性限定のイベント。ピンクの狐がカワイイ。

https://gitlab-jp.connpass.com/event/116951/  
https://gitlab-jp.connpass.com/event/105178/

## GitLabではじめる一人DevOps @jumpyoshim	

- 資料  
https://speakerdeck.com/jumpyoshim/one-person-devops-beginning-with-gitlab

[個人開発](https://gitlab.com/jumpyoshim/django-polls
)で一人DevOpsしている話。

GitLabにはタスク管理機能も備わっており、Trelloに近いこともできている。

- カンバン
- 見積もり・稼働管理
- バーンダウンチャート
- マイルストーン
- エピック

CI/CDもGitLabでやっている。

- コンテナレジストリにイメージをプッシュ
- ユニットテスト
- 静的解析
- コードメトリクスの計測
- デプロイ
  - Dplと言うツールでHerokuにデプロイしている

パイプラインをGitLab Badgesで可視化している。  
GitLab Pagesでpytestのテスト結果を可視化。

Dependencies.ioとRenovateを使ってプログラム言語、フレームワーク、サードパーティライブラリのバージョンを自動でアップデートしている。  
→Dependencies.ioとRenovate知りませんでしたが調べてみたらすごい便利そうでね。とりあえず個人開発で使ってみようかなと思いました。

開発フローはGitLab Flowを使っている。  
→さすがGitLab使い

タグを切ったら自動でパイプラインが走るようにしている。

## GitLab ChatOpsセッション @tnir

GitLab10/11から新たに導入されたアイコンが可愛いフレームワーク。  
CEでも今月から使えるようになった。  
SlackのSlashコマンドからGitLab CIのビルドパイプラインが叩けるらしい。  
.gilab-ci.ymlにJobを追加すると使えるけど、チャットから動かしたくないものは`except:[chat]`すると良いとのこと。  
もともとHubotなどからGitLab CIを呼んでいた人はすんなり移行出来そう。

## Gitlabでのproject import/exportのversion差異で困ったことありませんか？

importとexportのバージョン差異で苦しんだ話。  
資料なしで発表するという斬新なスタイルと思ったら、スライドは作ったけどPCごと持ってくるの忘れたとのことでした（笑）  
でも資料なしで発表するのすごいです。

## マーケターがGitlabを使って見た話

会社全体の業務がGitLabのイシューで回っている話。会社全体エンジニア力高い。

## Mattermost Plugin Bounty Programについて

- 資料  
https://www.slideshare.net/nemotoyusuke16/mattermost-plugin-bounty-program

登壇者は今月のSoftware DesignでMattermostの連載を開始した方。  
ちなみに会社ではMattermostを使っているのもあって、速攻で連載読みました。  
Mattermostプラグインを使うとチャットbotを立てるサーバーが必要なくなったり、UIすらカスタマイズ可能になるとのこと。  
プラグインの話はきっと連載に書かれるでしょうと期待してお待ちしてます。

https://github.com/mattermost/mattermost-server/issues/10457

mattermost-plugin-gitlabを実装すると$1,000もらえるらしいです。  
GitLabの他にも、WebEx、Skype、Jenkisも募集しているようですので賞金が欲しい方はお早めに。

## おわりに

{{% img src="images/gitlab-meetup-tokyo/title.jpg" w="600" h="450" %}}

じゃんけん大会でGitLabのトランプをいただきました。  
こども達とトランプします。ありがとうございました！
