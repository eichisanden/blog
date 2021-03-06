+++
thumbnail = "images/gradle-shadow/title.png"
tags = ["Gradle Shadow", "Java", "gradle"]
categories = ["技術メモ"]
date = "2019-05-17T13:23:38+09:00"
title = "Gradle Shadowでmainクラスを実行できる全部入りのjarファイルを作成する"
description = "Gradle Shadowでmainクラスを実行できる全部入りのjarファイルを作成する"
+++

依存するjarファイルの配置などが面倒なので、ちょっとしたツールはGoで作ることが多いのですが、Gradle Shadowを使ってjarファイルを1つに固めたら楽でした。

https://imperceptiblethoughts.com/shadow/

ちなみに全部入りのjarファイルのことをFat Jarと呼ぶそうです。  
User Jarとも言うみたいですが、タクシーのUberとは関係ないようです。へー。

https://www.sakatakoichi.com/entry/2015/11/16/143303


## 設定

こんな感じにbuild.gradleを書きました。基本的にはpluginsにGradle Shadowを指定するだけ。
mainが直接叩けるように、jar -> manifestのエントリーも追加。

```groovy
plugins {
    id 'java'
    id 'com.github.johnrengelman.shadow' version '5.0.0'
}

version '1.0'

sourceCompatibility = 1.8

repositories {
    mavenCentral()
}

jar {
    manifest {
        attributes 'Main-Class': 'com.example.Main'
    }
}

dependencies {
    compile group: 'org.apache.pdfbox', name: 'pdfbox', version: '2.0.15'
    compile 'org.postgresql:postgresql:42.2.5'
    compile 'com.sun.mail:javax.mail:1.6.1'
}
```

現時点ではIntelliJ IDEAでgraldeプロジェクトを作ると4.10.3が使わるのですが、Gradle4系では使えないようなので`gradle/wrapper/gradle-wrapper.properties`を書き換えました。

```
distributionUrl=https\://services.gradle.org/distributions/gradle-5.4.1-bin.zip
```

## ビルド

gradleで`shadowJar`タスクを実行すると`build/libs`にJarファイルが作成されています。

```
$ gradlew shadowJar
```

## 実行

作成したjarファイルを引数に指定してjavaコマンドを実行するだけです。

```
$ java -jar hoge-1.0.jar
```

## おわりに

以上、簡単でしたが自分の備忘のため記事にしました。
