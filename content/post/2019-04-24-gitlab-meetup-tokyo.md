+++
thumbnail = "images/gitlab-meetup-tokyo-no16/title.png"
tags = ["GitLab", "DevOps", "gitlabjp"]
categories = ["イベント"]
date = "2019-04-27T14:30:00+09:00"
title = "GitLab Meetup Tokyo #16: 新年度応援に行ってきた"
description = "2019/4/24に行われたGitLab Meetup Tokyo #16の参加レポートです"
+++

ブログ枠で参加させてもらいました。  
今回は内容盛りだくさんで時間が掛かっちゃいましたが参加レポートです。ご査収ください。

{{% ogp "https://gitlab-jp.connpass.com/event/126533/" %}}

場所は銀座のプレイドさん。
執務室とイベントスペースに区切りがない斬新なオフィス、素敵。

## GitLab Tokyo/GitLabのご紹介

昨年度の活動の振り返りと今年度の活動について。  
直近だと5/8-14にGitLab Contributeがあるそうです。  
https://about.gitlab.com/events/gitlab-contribute/

## GitLabでできること＆GitLab 12.0の改善点

{{% speakersdeck id="19209d0942a14dc0a749f97c474f8b7a" ratio="1.77777777777778" %}}

by @tnirさん

### GitLabの概要

- 機能一覧  

https://about.gitlab.com/features/

機能が多すぎてスクロールするだけで大変。

- GitLabのアドバンテージ＝DevOps

{{% img src="images/gitlab-meetup-tokyo-no16/devops-loop-and-spans-small.png" w="540" h="250" %}}

https://about.gitlab.com/devops-tools/

何でも揃っていて逆にできないこと何って感じですね。  
新しいツールを導入する調査や説得コストなんかを考えると標準で揃っているの良いですよね。
使い始めてしまえば課題がはっきりして、足りないところは別のツールに置き換えたりしやすいですし。

### 12.0

- 2019/6/22リリース予定
- 新機能はさほど多くない。
- 古いGitLab Runnerなどdeprecationもいくつかあるので注意が必要。
- APIアプデートはない。
- 不定期だったメジャーリリースは12ヶ月サイクルになる。

リリースサイクルが固定されるのは、今の最新バージョンが分かりやすくて良いですね。

## 共有、自動化、計測 -DevOpsツール考察-

by New Relicの@cskさん

{{% slideshare a1AMHuz1Kz3ZtA %}}

DevOpsとは何なのかを振り返りながら、ツールをどう活用していくかというお話でした。  
アプリのリリースとインフラのリリースが同時に行うという考え方は参考になりました。

>アジャイル開発のような動的な環境変化に対してうまく対処できるよう、組織が協力しあうための文化運動

DevOpsって人によって定義がずれている場合が多いと思っていて、原点となる考え方を揃えるのは大事だなと思いました。

ちなみにNew Relicってここまで機能が揃っていて日本法人もあったんですね知りませんでした。

## スペックを上げてクラウドで殴るCI & 後日談

by @sue445さん

{{% speakersdeck id="1d56e1f3c0d84ea3b1722b219b9f5696" retio="1.77777777777778" %}}

{{% ogp "https://esa-pages.io/p/sharing/8985/posts/109/88cafdb00f98f7c02b0a-slides.html#/" %}}

- 全ての手作業を自動化するのがミッション。働かないために全力で働く

ものすごい実践的な話でとても参考になりました。

- EC2のスポットインスタンスを使うノウハウ
- AWSからオンプレのGitLabを使うノウハウ
- AMIにDockerを入れておくことで30秒早くなった
- git clone --depth 1しても巨大なリポジトリをClone済みのDocker Imageを作ることで速度アップ

## ふんわりReview Appsでデプロイプレビュー

by Rena_mooさん

{{% ogp "https://docs.google.com/presentation/d/1pz3uCjdbYazYeq1ArFUHCUxleH_WHoYFsFuO0p7bDno/edit#slide=id.g35f391192_00" %}}

私も普段Netlifyで使ってますが、デプロイプレビューって便利ですよね。  
ちょっと環境づくりが大変みたいだけど自分も仕事で取り入れたいと思いました。  
初心者向けのRunnerのセッションをやって欲しいとのことでしたので誰かお願いします（私も聞きたい）

## GitLab CI/CDとECS Fargateでリリース作業が楽になった話

by @8rfxさん

{{% speakersdeck id="7b8e77edd6de4864b00bb7d2cfbf500b" ratio="1.33333333333333" %}}

- 辛い運用をGitLab CIに置き換えた話。
- Artifactが便利だった。
- Environment機能を使うと環境の一覧が勝手に作られる。
- ECRじゃなくてもFargateが使える。

Beforeの状況のわかりみが深かったです。Fargateちょっと調べ見よう。

## iOSとGitLab-CI

by @417_72kiさん

{{% speakersdeck id="6680fd287c364aabb912fe203063279d" ratio="1.33333333333333" %}}

https://speakerdeck.com/417_72ki/ios-and-gitlab-ci

- クラウドサービスでCIするとiOSアプリのビルドに必要な証明書をサービス上に登録する必要がある
- 客先だと顧客のAppleIDを使わないといけないので扱いがシビア
- GitLabだったらオンプレに建てられるのが良い。

受託開発のように色々制約が多い状況では、GitLabのようにクラウド/オンプレの選択肢があるのは良いですね。

## GitLabサーバーのモニタリング

by @yteraokaさん

{{% speakersdeck id="37fb43a457334aca84c8c6ea236ad970" ratio="1.77777777777778" %}}

- GitLab CIの待ち時間を知りたかったが Prometheusにはなかったので作った。
- GrafanaのグラフをSlackに通知
- 入門Prometheusが出るらいしい(https://www.amazon.co.jp/dp/4873118778)
- gitlab.rbを眺めているとRelease Noteにない新機能が見つかるらしい。
- 現在はデフォルト無効だけど、インターネットに公開する場合はRack Attackを有効にした方が良い。

エムスリーさんでは2012年からGitLabを使っていて現在プロジェクト数は1200越えだそうですごい（語彙）

## 新人がGitを使えるようになるために育成レポートをGitLabで管理してみた話

by @takami228さん

{{% speakersdeck id="e0806351575d473e847e1cb8e30eee4d" ratio="1.77777777777778" %}}

https://speakerdeck.com/takamii228/gitlab-review-practice

Gitの操作は実践して覚えるのが1番だし、最初はコマンドを覚えるのが良いと思っているのですごく共感しました。  
あとエンジニアなら、WordやExcelじゃなくてテキストが良いですよね。「達人プログラマー」の「14.プレインテキストの威力」でも言ってますしね。

# おわりに

GitLabを中心にDevOps周辺の様々な話が聞けて非常に楽しかった。  
かなり内容盛りだくさんで知らないことも一杯でしたので、GW中に自分なりに消化したいと思いました。
