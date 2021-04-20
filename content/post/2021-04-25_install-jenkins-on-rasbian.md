+++
title = "raspi-jenkins"
date = 2021-03-31T00:00:00Z
tags = [
    "Raspberry Pi",
    "Jenkins",
]
draft = true # `true` or `false`
toc = true
backtotop = true
disable_comment = true
+++

Raspberry Pi に Jenkins を載せるやり方です  :)

<!--more-->
---

# 注意点

この記事は下記の記事のアーカイブです ;)

WIP

# 概要

Raspberry Pi に Jenkins を載せるやり方です

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

+ 入れられそうな最新の OpenJDK を探します
  + `v11` が最新のよう

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

+ `openjdk-11-jre` をインストールします

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



# memo

admin の初期パスワードは `/var/lib/jenkins/secrets/initialAdminPassword` にある

これは変えたほうがよい

設定ファイル

+ log ローテートの設定

+ プロセスはこんな感じ

```
$ ps -aufx | grep jenkins
pi        6347  0.0  0.0   7348   572 pts/1    S+   02:25   0:00              \_ grep --color=auto jenkins
jenkins   3556  0.0  0.7  14736  7440 ?        Ss   Apr20   0:00 /lib/systemd/systemd --user
jenkins   3557  0.0  0.2  36564  2544 ?        S    Apr20   0:00  \_ (sd-pam)
jenkins   3577  0.0  0.1   6960  1388 ?        S    Apr20   0:00 /usr/bin/daemon --name=jenkins --inherit --env=JENKINS_HOME=/var/lib/jenkins --output=/var/log/jenkins/jenkins.log --pidfile=/var/run/jenkins/jenkins.pid -- /usr/bin/java -Djava.awt.headless=true -jar /usr/share/jenkins/jenkins.war --webroot=/var/cache/jenkins/war --httpPort=8080
jenkins   3578  5.7 30.3 455088 287468 ?       Sl   Apr20  12:25  \_ /usr/bin/java -Djava.awt.headless=true -jar /usr/share/jenkins/jenkins.war --webroot=/var/cache/jenkins/war --httpPort=8080
```

systemd のファイルは以下



```
$ cat /run/systemd/generator.late/jenkins.service
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

+ Jenkins の Version の確認

```
$ sudo cat /var/lib/jenkins/config.xml | grep "version" | grep -v "xml"
  <version>2.277.2</version>
```


# まとめ

hoeg