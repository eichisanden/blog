+++
thumbnail = "images/setup-mac-by-ansible/top.png"
tags = ["MacOS", "Ansible", "Homebrew", "mas"]
categories = ["技術メモ"]
date = "2018-11-19T19:39:25+09:00"
title = "Macの環境構築をAnsibleで自動化 2018"
description = "新しいMacbook Airを買ったのでの環境構築をAnsibleで自動化した"
+++

私の記憶が確かなら...2015年ごろMacの開発環境構築のAnsible化が流行ったと記憶しています。
当時、自分もやってみようかなと思ってましたが、Macを買い換えるタイミングもないし何となく今まで来ていたのですが、先日MacBookを買い換えて再セットアップが必要になったので、今更ながらやってみました。
将来、Macを買い換えた時の自分のためが8割ですが、探すと古い記事が多くてAnsibleやHomebrewが結構変わっているので、2018年版として役に立つこともあるかもと思ったので記事にしてみます。

## 環境

- MacOS 10.14.1 Mojave
- Homebrew 1.8.2
- Ansible 2.7.1
- mas 1.4.3

## Homebrew のインストール

まずはHomebrewを入れないことには始まりませんので[公式サイト](https://brew.sh/index_ja)の手順に従ってインストールしましょう。
（そういえば、この時点でXCodeを全く意識してなかったけどすんなり通ってしまった...何か変わったか..）

```
$ /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

ちなみに、以前は Homebrew-cask を使うためには、`brew tap caskroom/cask` で参照するリポジトリを追加する必要がありましたが、今はHomebrewに統合されているので必要ありません。  
パッケージの検索も `brew cask search <package>`ではなく、`brew search <package>`でいけます。

<参考>  
[HomebrewのCaskのレポジトリがcaskroomからhomebrewへ移動](https://rcmdnk.com/blog/2018/06/08/computer-homebrwe/)


## Ansibleのインストール

HomebrewでAnsibleとPyhonをインストールします。ここまでは手動。

```
$ brew install python
$ brew install ansible
```

## AnsibleでPlaybookを作っていく

ここからansible-playbookで自動化するため、適当なフォルダを作って作業していきます。  

### Inventry

とりあえずlocalhostにつなげるInventryファイルを作ります。

```
echo localhost > hosts
```

### Playbookの作成

次にplaybook本体を作っていきます。今回はplaybook.ymlというファイルを作りました。  
冒頭部分から順を追って説明していきます。

よく見る、`sudo:`という書き方は古いみたいなので `become:`としました。

```yml
- hosts: all
  connection: local
  gather_facts: no
  become: no
```

### Homebrew-caskパッケージのインストール

[homebrew_caskモジュール](https://docs.ansible.com/ansible/2.5/modules/homebrew_cask_module.html)で MacOSアプリをインストールしていきます。
コード自体は特筆することは特にありませんが、これ全部サイト回ってインストールしてくのは大変なのですごい楽です。

```yml
    - name: install homebrew cask packages
      homebrew_cask:
        name: "{{ item }}"
      with_items:
        - cheatsheet
        - clipy
        - docker
        - dropbox
        - evernote
        - firefox
        - google-chrome
        - intellij-idea
        - iterm2
        - jetbrains-toolbox
        - vagrant
        - virtualbox
        - visual-studio-code
```

### Hombrewパッケージのインストール

[homebrewパッケージ](https://docs.ansible.com/ansible/2.7/modules/homebrew_module.html)
でインストールしていきます。
以前はハッシュのリストを `with_items` でループさせる手順が多く見られましたが今は非推奨みたいで配列で回しました

```yml
    - name: install homebrew packages
      homebrew:
        name:
          - ansible
          - git
          - go
          - hugo
          - jq
          - mas
          - nkf
          - nodebrew
          - peco
          - python
          - rbenv
          - ruby-build
          - tree
          - wget
          - zsh
```

[以前の記事](https://gyoza.beer/post/2018-10-14-macos-mojave/)にも書いたのですが、OS標準のvimを無効にするため、`with-override-system-vi`オプション付きでインストールするためvimは別タスクにしました。

```yml
    - name: install vim
      homebrew:
        name:
          - vim
        install_options:
          --with-override-system-vi
```

### zsh関連の設定

oh-my-zshはshellでインストールするので、冪等性は自分で確保しないといけません。
statで`~/.oh-my-zsh/`を存在チェックした結果を変数に格納して、存在しない場合だけインストールします。
デフォルトシェルの変更は Ansibleに標準で入っている `user` Moduleで行いました。
また、個人で使うなら、ログインユーザのnameはベタ書きでも良いのですが、公開することを考えて `ansible_ssh_user`変数から取得するようにしています。

```yml
    - name: 'check oh-my-zsh'
      stat: path=~/.oh-my-zsh/
      register: d
    - name: 'install oh-my-zsh'
      shell: sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
      when: d.stat.exists == false
    - name: 'change default shell'
      user:
        name: "{{ ansible_ssh_user }}"
        shell: /usr/local/bin/zsh
```

### MasでCaskでインストールできないアプリをインストールする

XCodeなどHomebrew-caskでインストールできないアプリを[mas(Mac App Store command line interface)](https://github.com/mas-cli/mas)でインストールしていきます。このmasの存在は今回初めて知りました。
この部分はansible-garaxyで公開されている[ansible-role-mas](https://github.com/geerlingguy/ansible-role-mas)というRoleを使わせてもらいました。


### Roleのインストール

あとで使う別のRoleと共にrequirements.ymlというファイルに記述しておきます。

```yml
---
- name: geerlingguy.mas
- name: geerlingguy.dotfiles
```

続いてansible-garalxyコマンドでRoleをインストールするのですが、
よく分からないところにインストールされると嫌なので作業ディレクトリの下のrolesフォルダにインストールしました。

```
$ ansible-galaxy install -p roles -r requirements.yml
```

次に、masでインストールするためにはアプリのIDが必要ですので、古い方のMacで `mas list` してインストールされているアプリのIDを調べます。
今回はXCodeをインストールしたいのでIDを控えておきます。

```
$ mas list
497799835 Xcode (10.1)
409183694 Keynote (8.3)
408981434 iMovie (10.1.10)
409201541 Pages (7.3)
682658836 GarageBand (10.3.1)
409203825 Numbers (5.3)
```

playbook.ymlに戻って、先ほど取得したRoleで必要な`mas_installed_apps`変数にXCodeを定義します。この変数にセットしておくとインストールしてくれる仕組みです。

```yml
  vars:
    # for ansible-role-mas
    mas_installed_apps:
      - { id: 497799835, name: "Xcode (10.1)" }
```

あとはrolesに `geerlingguy.mas` を定義します。Playbookを実行するとRoleが実行されてXCodeがインストールされます。
（tasksとrolesを同時に使ってるのがイマイチな気がするけど）

```yml
  roles:
    - geerlingguy.mas
```

ちなみに OSX 10.13以上はコマンドでApp Storeにログインできなくなってますが、App Storeアプリを一度立ち上げてログインしておけばそれだけで認証してくれるようです。

```
$ mas signin <Apple ID>
Error: The 'signin' command has been disabled on this macOS version. Please sign into the Mac App Store app manually.
For more info see: https://github.com/mas-cli/mas/issues/164
```

### dotfiles

自分のdotileは元々Githubに置いていたのですが、これまたAnsible-garaxyから拝借したRoleを使って設定していきます。
リポジトリのパスとクローンするフォルダ、対象のファイルを変数に指定します。

```yml
  vars:
    # for ansible-role-dotfiles
    dotfiles_repo: "git@github.com:eichisanden/dotfiles.git"
    dotfiles_repo_local_destination: "~/src/github.com/eichisanden/dotfiles"
    dotfiles_files:
      - .bashrc
      - .eslintrc
      - .gitattributes
      - .gitconfig
      - .gitignore
      - .psqlrc
      - .vimrc
      - .zshrc
```

roleを下記のように定義します。実行すると、指定したフォルダにクローン、更にクローンしたファイルをホームディレクトリにシンボリクリンクを貼ってくる仕組みです。

```yml
  roles:
    - geerlingguy.dotfiles
```

### MacOSの環境設定

DockやFinderの設定なども自動化してみました。
標準の`osx_defaults`モジュールでdefaultsコマンドで変更できることが設定可能です。
キー名を調べるのが結構面倒くさいのと、即反映される設定とkillall Dockとかしてプロセスを上げ直さないと反映されないものとあり試行錯誤しながら設定していきました。

```yml
    - name: 'setting macOS'
      osx_defaults: >
        domain={{ item.domain }}
        key={{ item.key }}
        type={{ item.type }}
        value={{ item.value }}
        state={{ item.state|default('present') }}
      with_items:
        # auto hide dock (need "killall Dock")
        - { domain: com.apple.dock, key: autohide, type: bool, value: true }
        - { domain: com.apple.dock, key: autohide-time-modifier, type: int, value: 0 }
        # Move dock to left side
        - { domain: com.apple.dock, key: orientation, type: string, value: "left" }
        # Show Status bar in Finder (need "killall Finder")
        - { domain: com.apple.finder, key: ShowStatusBar, type: bool, value: true }
        # Show Path bar in Finder
        - { domain: com.apple.finder, key: ShowPathbar, type: bool, value: true }
        # Show Tab bar in Finder
        - { domain: com.apple.finder, key: ShowTabView, type: bool, value: true }
        # Show the hidden files
        - { domain: com.apple.finder, key: AppleShowAllFiles, type: bool, value: true }
        # Enable text selection in QuickLook
        - { domain: com.apple.finder, key: QLEnableTextSelection, type: bool, value: true }
        # Show the battery percentage from the menu bar (need "killall SystemUIServer")
        - { domain: com.apple.menuextra.battery, key: ShowPercent, type: string, value: "YES" }
```

＜参考＞  
- [Macの環境構築をAnsibleに任せる](システム環境設定)https://qiita.com/itkr/items/8a3fcab6ed76052d8337)  
- [ターミナルから Mac を設定する（defaults write コマンド等）](https://qiita.com/djmonta/items/17531dde1e82d9786816)  
- [osx_defaults_module](https://docs.ansible.com/ansible/2.5/modules/osx_defaults_module.html)  

## おわりに

なんでも自動化すれば良いものではないのでやりすぎは注意ですが、まだ完成していないのでちまちま直してくつもりです。
あと、あまりAnsibleに詳しくない自分にとって練習の題材には良かったかも。あとは何年か後にMacを買い換えた俺がこのエントリを思い出して喜んでくれたら幸いです。

＜参考＞  
- [Mac の開発環境構築を自動化する (2015 年初旬編)](http://t-wada.hatenablog.jp/entry/mac-provisioning-by-ansible)  
- [Macの環境構築をAnsibleに任せる](https://qiita.com/itkr/items/82ddb1902b0051940526)  
- [geerlingguy/mac-dev-playbook](https://github.com/geerlingguy/mac-dev-playbook)  
- [Macの環境構築をAnsibleでやることにした](https://joe-re.hatenablog.com/entry/2015/01/02/212007)  
