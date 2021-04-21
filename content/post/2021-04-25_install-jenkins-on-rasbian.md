+++
title = "Raspberry Pi に Jenkins をインストールする"
date = 2021-03-31T00:00:00Z
tags = [
    "Raspberry Pi",
    "Jenkins",
]
draft = false # `true` or `false`
toc = true
backtotop = true
disable_comment = true
+++

Raspberry Pi に Jenkins をインストールし起動するやり方です  :)

<!--more-->
---

# 注意点

この記事は下記の記事のアーカイブです ;)

https://iganari.hatenablog.com/entry/2021/04/22/025532

今記事の情報は執筆当時のものになります <(. . )>

# 概要

宅内に簡単なジョブ基盤が欲しいと思っていたところ、使用していない Raspberry Pi があったので Jenkins をインストールしてみようと思いました

結果から書くと、公式ドキュメントの通りにやると出来ます :)

https://www.jenkins.io/doc/book/installing/linux/

# 実際にやってみる

### 使用する Raspberry Pi のスペック

```
$ cat /etc/os-release
PRETTY_NAME="Raspbian GNU/Linux 10 (buster)"
NAME="Raspbian GNU/Linux"
VERSION_ID="10"
VERSION="10 (buster)"
VERSION_CODENAME=buster
ID=raspbian
ID_LIKE=debian
HOME_URL="http://www.raspbian.org/"
SUPPORT_URL="http://www.raspbian.org/RaspbianForums"
BUG_REPORT_URL="http://www.raspbian.org/RaspbianBugs"
```
```
$ uname -a
Linux raspi 5.10.17-v7+ #1403 SMP Mon Feb 22 11:29:51 GMT 2021 armv7l GNU/Linux
```
```
$ cat /proc/version
Linux version 5.10.17-v7+ (dom@buildbot) (arm-linux-gnueabihf-gcc-8 (Ubuntu/Linaro 8.4.0-3ubuntu1) 8.4.0, GNU ld (GNU Binutils for Ubuntu) 2.34) #1403 SMP Mon Feb 22 11:29:51 GMT 2021
```

### Java のインストール

+ apt を更新する

```
sudo apt update
```

+ インストール出来る最新の OpenJDK を探します

```
$ sudo apt search openjdk | grep jre

WARNING: apt does not have a stable CLI interface. Use with caution in scripts.

default-jre/stable 2:1.11-71+b1 armhf
default-jre-headless/stable 2:1.11-71+b1 armhf
openjdk-10-jre/stable 10.0.2+13-2 armhf
openjdk-10-jre-headless/stable 10.0.2+13-2 armhf
openjdk-10-jre-zero/stable 10.0.2+13-2 armhf
openjdk-11-jre/stable 11.0.9.1+1-1~deb10u2 armhf
openjdk-11-jre-headless/stable 11.0.9.1+1-1~deb10u2 armhf
openjdk-8-jre/stable 8u212-b01-1+rpi1 armhf
openjdk-8-jre-headless/stable 8u212-b01-1+rpi1 armhf
openjdk-8-jre-zero/stable 8u212-b01-1+rpi1 armhf
openjdk-9-jre/stable 9.0.4+12-4 armhf
openjdk-9-jre-headless/stable 9.0.4+12-4 armhf
openjdk-9-jre-zero/stable 9.0.4+12-4 armhf
```

+ LTS である `Java 11` に対応する `openjdk-11-jre` がインストール出来そうなので、このバージョンをインストールしましょう
  + 参考: [AdoptOpenJDK | Support](https://adoptopenjdk.net/support.html#roadmap)

```
sudo apt install openjdk-11-jre
```

+ OpenJDK の確認します

```
$ java --version
openjdk 11.0.9.1 2020-11-04
OpenJDK Runtime Environment (build 11.0.9.1+1-post-Raspbian-1deb10u2)
OpenJDK Server VM (build 11.0.9.1+1-post-Raspbian-1deb10u2, mixed mode)
```

### Jenkins のインストール

+ Jenkins を apt 経由でインストールします
  + 参考 [公式ドキュメント](https://www.jenkins.io/doc/book/installing/linux/)

```
wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
sudo sh -c 'echo deb https://pkg.jenkins.io/debian-stable binary/ > \
    /etc/apt/sources.list.d/jenkins.list'
sudo apt-get update
sudo apt-get install jenkins
```

+ Jenkins のデーモンを確認します

```
$ sudo systemctl status jenkins
● jenkins.service - LSB: Start Jenkins at boot time
   Loaded: loaded (/etc/init.d/jenkins; generated)
   Active: active (exited) since Tue 2021-04-20 22:50:21 JST; 1min 6s ago
     Docs: man:systemd-sysv-generator(8)
    Tasks: 0 (limit: 2062)
   CGroup: /system.slice/jenkins.service

Apr 20 22:50:19 raspi systemd[1]: Starting LSB: Start Jenkins at boot time...
Apr 20 22:50:19 raspi jenkins[3495]: Correct java version found
Apr 20 22:50:19 raspi su[3554]: (to jenkins) root on none
Apr 20 22:50:19 raspi su[3554]: pam_unix(su-l:session): session opened for user jenkins by (uid=0)
Apr 20 22:50:20 raspi su[3554]: pam_unix(su-l:session): session closed for user jenkins
Apr 20 22:50:21 raspi jenkins[3495]: Starting Jenkins Automation Server: jenkins.
Apr 20 22:50:21 raspi systemd[1]: Started LSB: Start Jenkins at boot time.
```

+ ブラウザから初期設定を行います

```
http://IP_ADDRESS:8080
```

+ 準備中…

{{< figure src="/images/2021/04/25/01.png" >}}

+ まずは admin としてログイン & 初期設定を行うために表記されている PATH にあるファイルから admin のパスワードをコピペします

{{< figure src="/images/2021/04/25/02.png" >}}

+ Jenkins の Plugin のインストール画面では `suggested` を選びます

{{< figure src="/images/2021/04/25/03.png" >}}

+ admin 以外のユーザの作成をします
  + ここでは作成せずに、admin のまま進めます

{{< figure src="/images/2021/04/25/04.png" >}}

+ Jenkins の URL の設定を入れます
  + ここも後でゆっくりと設定したほうがいいでしょう

{{< figure src="/images/2021/04/25/05.png" >}}

+ これで初期設定は完了です!!
  + 設定しなかった項目は後ほど設定しましょう

{{< figure src="/images/2021/04/25/06.png" >}}

+ Jenkins のようこそページが見れるようになりました :)

{{< figure src="/images/2021/04/25/07.png" >}}

---> これで Jenkins on Raspberry Pi の基礎構築が完了しました! :)

# その他、最低限設定しておいたほうが良いこと

### Jenkins の Version

+ Jenkins の Version はコンフィグに書いてあるので確認出来ます

```
$ sudo cat /var/lib/jenkins/config.xml | grep "version" | grep -v "xml"
  <version>2.277.2</version>
```

### Jenkins CLI

Jenkins には便利な CLI があるので実行出来るようにしておきましょう

+ 以下の URL にアクセスし、 `jenkins-cli.jar` をダウンロードしておきます
  + ダウンロード後にサーバにコピーして下さい

```
http://IP_ADDRESS:8080/cli/
```

+ 直接サーバ上でダウンロードする場合は以下のようにします

```
cd /usr/local/bin
sudo wget http://localhost:8080/jnlpJars/jenkins-cli.jar
sudo chmod 0755 jenkins-cli.jar
```

### admin のパスワード 

+ admin の初期パスワードは `${JENKINS_HOME}/secrets/initialAdminPassword` にあります

```
export JENKINS_HOME='/var/lib/jenkins'

cat ${JENKINS_HOME}/secrets/initialAdminPassword
```

記事中にも書きましたが、admin ユーザ以外の設定を必ずしましょう

### ログローテートの設定

Jenkins のログに対してのローテートの設定( `/etc/logrotate.d/jenkins` )を変更しておきます

+ 修正前

```
/var/log/jenkins/jenkins.log /var/log/jenkins/access_log {
        weekly
        copytruncate
        missingok
        rotate 52
        compress
        delaycompress
        notifempty
}
```

+ 修正後

```
/var/log/jenkins/*.log
{
    daily
    missingok
    dateext
    rotate 52
    compress
    delaycompress
    notifempty
    create 644 jenkins jenkins
    sharedscripts
    postrotate
        if [ -f /var/run/jenkins ]; then
            kill -USR1 `cat /var/run/jenkins`
        fi
    endscript
}
```

+ ドライランをします

```
sudo /usr/sbin/logrotate -dv /etc/logrotate.d/jenkins
```

+ 強制実行をします

```
sudo /usr/sbin/logrotate -f /etc/logrotate.d/jenkins
```

### Systemd について

+ 固有の状況かもしれませんが、 Jenkins の Systemd の設定が以下の PATH にありました

```
/run/systemd/generator.late/jenkins.service
```

+ 内容は以下のようです
  + `/run/systemd/generator.late/jenkins.service`

```
# Automatically generated by systemd-sysv-generator

[Unit]
Documentation=man:systemd-sysv-generator(8)
SourcePath=/etc/init.d/jenkins
Description=LSB: Start Jenkins at boot time
Before=multi-user.target
Before=multi-user.target
Before=multi-user.target
Before=graphical.target
After=remote-fs.target
After=network-online.target
Wants=network-online.target

[Service]
Type=forking
Restart=no
TimeoutSec=5min
IgnoreSIGPIPE=no
KillMode=process
GuessMainPID=no
RemainAfterExit=yes
SuccessExitStatus=5 6
ExecStart=/etc/init.d/jenkins start
ExecStop=/etc/init.d/jenkins stop
```

# まとめ

Raspberry Pi に Jenkins をインストールし起動するやり方を書きました

宅内の簡単な処理を行うジョブ基盤としてしばらく使ってみようと思います

Have fun !! :)
