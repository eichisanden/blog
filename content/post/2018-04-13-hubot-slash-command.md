+++
thumbnail = "images/hubot-slash-command/component.png"
tags = ["Slack", "Mattermost", "Hubot"]
categories = ["プログラミング"]
title = "hubot-slash-commandをリリースしました"
date = "2018-04-15T16:06:07+09:00"
+++

SlackやMattermostのSlash comandをOutgoing webhooksのように使えるHubotスクリプトを作ってnpmに公開しました。

https://www.npmjs.com/package/hubot-slash-command

## どんなもの?

Slash Commandsに渡した引数をHobotに横流します。  
例えば`/bot ping`を実行すると`<ボットの名前> ping`のように話かけたのと同じ結果を得られます。

{{% img src="images/hubot-slash-command/example.png" w="516" h="216" %}}

Slash CommandのレスポンスとHubotに送るメッセージはカスタマイズ出来るようにしています。

## 構成

Hubot内臓のexpressでスラッシュコマンドを受け付けています。
受け付けたパラメータをrobot.receiveでHubotに渡しています。

{{% img src="images/hubot-slash-command/component.png" w="723" h="461" %}}

## 何で作ったの？

会社ではMattermostを使っているのですが、Hubotを使ってChatOpsをしようとすると色々制約がありました。

- プライベートチャンネル中心に使っているのでOutgoing Webooksが使えない
- 雰囲気的にパブリックに切り替えにくいし、そもそもプライベートチャンネルをパブリックに切り替える機能もない
- Botユーザを使うタイプのHubotアダプターの [hubot-matteruser](https://github.com/loafoe/hubot-matteruser/)が使えれば色々解決できそうなんですが、GitLabとのSSOに対応していない、BotだけにSSOを介さないログインを許可することが出来ない。

Slash Commandsを受けつけるAPIを実装してIncoming Webhooksで結果を返せば何でも作れるのですが、
色々公開されているHubotスクリプトが使えないので、Slash Commandsの引数をそのままHubotへ連携するスクリプトを作成しました。
（[hubot-schedule](https://github.com/matsukaz/hubot-schedule)とか使いたかった）

## 設定方法

Slackでも使えるので、試しにSlackとHerokuに設定しています。  
（Slack公式のHubot連携があるので実際Slackで使いたい場面はない気もしますが）


### Hubotの設定
(1) [公式サイト](https://hubot.github.com/docs/)に従ってHubotプロジェクトを作成します。
アダプターは[hubot-mattermost](https://www.npmjs.com/package/hubot-mattermost)を指定して下さい。
（Mattermost用のアダプターですがSlackでも動きます）

Herokuへデプロイするのに必要なProcfileは自動的に追加されています。

```
web: bin/hubot -a mattermost
```


(2) hubot-slash-commandをインストールします

```
$ npm i hubot-slash-command --save
```

(3) external-scripts.jsonに追加します。

```
["hubot-slash-command"]
```

(4) git push heroku masterしておきます

### Outgoing Webhooksの設定

URLはHerokuのURL + "/hubot-slash-command"を指定してます。
TokenはあとでHerokuに設定するのでコピーしておいてください。

### Herokuに環境変数を設定

hubot-mattermostとhubot-slash-commandに必要なパラメータを設定しましょう。
hubot-slash-commandの`HUBOT_SLASH_COMMAND_TOKENS`は必須なので先ほどコピーしたToknenを設定して下さい。

{{% img src="images/hubot-slash-command/config_vars.png" w="702" h="459" %}}

以上で設定完了となります。

ChatOpsする基盤が出来たので次は出来ることを充実させていこうと思います。

