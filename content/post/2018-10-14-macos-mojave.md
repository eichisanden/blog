+++
thumbnail = "images/macos-mojave/title.png"
tags = ["Mac"]
categories = ["技術メモ"]
date = "2018-10-14T09:45:35+09:00"
title = "ちょっと古めのMacBook Air に macOS Mojaveをインストールしてみた"
description = "MacBook Air 2013 に macOS Mojaveをインストールした記録と、新機能についての所感"
+++

詳しく調べてないけど、そんな悪い評判も聞かないので High Sierraから、パワーに満ちたシンプル macOS Mojaveにアップデートしてみました。  

## 動作環境

MacBook Air(mid 2013)

[互換性のあるMac](https://support.apple.com/ja-jp/HT201475)によるとMacBook Air、MacBook Proは 「2012年中期以降に発売されたモデルが対象」と結構厳しいです。  
今回はなんとかセーフでしたが、うちのMacBook Airの現役引退の日はそう遠くなさそうです。

## インストール

[High Sierraへのアップデートではハマった](https://gyoza.beer/post/2018-01-10-install-macos-sierra/)
けど今回は何事もなくスムーズに完了しました。

## トラブルシューティング

### iTerm2 のフォントが汚い

画像だとよく分からないかもしれないけど、ぱっと見で分かるぐらいWindowsのフォントのようにギザギザして汚くなってしまいました。  

{{% img src="images/macos-mojave/iterm2-before.png" w="653" h="443" %}}

> Fix support for subpixel antialiasing on Mojave.
  Previously, if you had enabled this hidden OS
  feature, text would be very hard to read.

元々、iTerm2 は3.2.2が入っていましたが、  
3.2.3のリリースノートに上記の記述があり、単純にバージョンアップしただけで綺麗になりました

{{% img src="images/macos-mojave/iterm2-after.png" w="653" h="443" %}}

### OSレベルでフォントが汚くなった

iTerm2は綺麗になりましたが、根本的にOSレベルで
フォントのサブピクセル・アンチエイリアス処理が変更されカラーフリンジがなくなったことで、非Retinaディスプレイではフォントが汚く表示されるようになったそうです。  
うちの場合はMac本体が非Retinaですが、外部ディスプレイに繋ぐケースもあるので多くの人に影響ある修正ではと思います。  
ただ、コマンドラインで下記を実行して一度ログアウトすることで廃止されたカラーフリンジ・エフェクトを復活させることができるそうです。

```
$ defaults write -g CGFontRenderingFontSmoothingDisabled -bool NO
```

またアンチエイリアシングの強度を変えれるようで、「AppleFontSmoothing」を「3」と最強に変更しました。これもターミナルで実行し、ログアウトしログインすると反映されます。

```
$ defaults -currentHost write -globalDomain AppleFontSmoothing -int 3
```

ただ、微妙な差はよく分かってないかも。

[参考]
https://gori.me/mac/mac-tips/110780

## パフォーマンス

そもそも速いPCじゃないのですが、今のところ特別遅くなった印象はありません。

----
切り替えたばかりなので、まだ何か起きそう。何か起きたら追記します。

## vimの起動でエラー

(2018/10/16 追記) vimの起動時にpowerlineでエラーが出ることに気づいたので対応しました

```
$ vi
Traceback (most recent call last):
  File "<string>", line 9, in <module>
  File "/Users/eiichi/.vim/bundle/powerline/powerline/__init__.py", line 6, in <module>
    import logging
  File "/usr/local/Cellar/python/2.7.13/Frameworks/Python.framework/Versions/2.7/lib/python2.7/logging/__init__.py", line 26, in <module>
    import sys, os, time, cStringIO, traceback, warnings, weakref, collections
ImportError: dlopen(/usr/local/Cellar/python/2.7.13/Frameworks/Python.framework/Versions/2.7/lib/python2.7/lib-dynload/time.so, 2): no suitable image found.  Did find:
^I/usr/local/Cellar/python/2.7.13/Frameworks/Python.framework/Versions/2.7/lib/python2.7/lib-dynload/time.so: code signature in (/usr/local/Cellar/python/2.7.13/Frameworks/Python.framework/Versions/2.7/lib/python2.7/lib-dynload/time.so) not valid for use in process using Library Validation: mapped file has no cdhash, completely unsigned? Code has to be at least ad-hoc signed.
An error occurred while importing powerline module.
This could be caused by invalid sys.path setting,
or by an incompatible Python version (powerline requires
Python 2.6, 2.7 or 3.2 and later to work). Please consult
the troubleshooting section in the documentation for
possible solutions.
Unable to import powerline, is it installed?
Press ENTER or type command to continue
```

まず、自分はappleがビルドしたOS標準のvimを使ってましたが、Mojaveにしたことで更新されたようです。root@apple.com強そう

```
$ vim --version
VIM - Vi IMproved 8.0 (2016 Sep 12, compiled Aug 17 2018 15:22:29)
Included patches: 1-503, 505-680, 682-1283
Compiled by root@apple.com
```

python2でエラーになっていますが、そんなエラーよく分からんので既存の環境はサクッと捨てて
Homebrewでpython3と、vimをインストールします  
vimは `--with-override-system-vi` オプションで付きでインストールして、brewで入れた方を使うようにします。これでエラーが出なくなりました

```
$ brew install python
$ brew install vim --with-override-system-vi
# Homebrewで入れた方が起動している
$ vim --version
VIM - Vi IMproved 8.1 (2018 May 18, compiled Oct 16 2018 15:07:10)
macOS version
Included patches: 1-450
Compiled by Homebrew
（省略）
# python2が無効になりpython3が有効になっている
+comments          +libcall           -python            +viminfo
+conceal           +linebreak         +python3           +vreplace
```

[参考]  
https://github.com/powerline/powerline/issues/1947　　
https://qiita.com/owlbeck/items/2df2ab50eedd32011ffd

## 新機能

続いて、使える新機能がないか確認していきます。

- [ダークモード](https://help.apple.com/macOS/mojave/whats-new/?lang=ja&cases=kIFAGHvXTAepRptQXiXe5w,N0bfKOvERSiAp06mHOcgOQ#dark-mode)  
背景が暗くなるだけっちゃだけ。とりあえず設定してみたけど、Apple純正のアプリ以外でダークになるものが少ない印象。とにかく黒い画面が好きな人向け

- [スタック](https://help.apple.com/macOS/mojave/whats-new/?lang=ja&cases=kIFAGHvXTAepRptQXiXe5w,N0bfKOvERSiAp06mHOcgOQ#stacks)  
ファイルの種類やファイル更新日やタグなどでデスクトップのファイルをまとめてくれる機能。  
種類はともかく、日付でまとめて便利なイメージがあまり湧かないしタグでまとめるぐらいならフォルダ切るかな

- [クイックルック](https://help.apple.com/macOS/mojave/whats-new/?lang=ja&cases=kIFAGHvXTAepRptQXiXe5w,N0bfKOvERSiAp06mHOcgOQ#quick-look)  
特別なソフトなしにファイルを編集できるようになりました。  
使い方は、Finderで画像を選択してスペースキーを長押しすると、プレビューが開くのアイコンを押します

{{% img src="images/macos-mojave/quick-look1.png" w="715" h="643" %}}

そうすると、画像編集するツールバーが表示されます。Skitchでやってたような画像編集はこれで出来てしまいます。ブログに貼る画像の編集ぐらいには十分で、これは使えそうです。

{{% img src="images/macos-mojave/quick-look2.png" w="715" h="643" %}}

- [スクリーンショット](https://help.apple.com/macOS/mojave/whats-new/?lang=ja&cases=kIFAGHvXTAepRptQXiXe5w,N0bfKOvERSiAp06mHOcgOQ#screenshots)  
スクリーンショットの機能が強化されていて、新たに `⌘` + `Shift` + `5` ショートカットが追加になりました。  

{{% img src="images/macos-mojave/ss.png" w="937" h="387" %}}

アイコンの意味は以下の通り。スクリーンキャストも取れるようなっています。

- 静止画
  - スクリーン全体
  - ウィンドウを選択
  - 範囲を選択
- 動画
  - スクリーン全体
  - 範囲を選択

今まで操作によってショートカットを使い分けが大変でしたが、今後はこれだけ覚えておけば大丈夫そうです。

## おわりに

今回、私個人の環境は割とスムーズに移行できました。
クイックルックとスクリーンショットは便利そうなので会社のMacも早く移行したい
