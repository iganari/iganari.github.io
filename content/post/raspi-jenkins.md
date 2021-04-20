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

Raspi + Camera + Node-RED => 栽培日記 v1

<!--more-->
---

# 注意点

Raspberry Pi に Jenkins を載せるやり方です

# 概要

Raspberry Pi に Jenkins を載せるやり方です

# スペック

+ Raspberry Pi

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

# やってみる

# Java のインストール

```
sudo apt update
```

+ 入れられそうな最新の OpenJDK を探す
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

+ `openjdk-11-jre` をインストールする

```
sudo apt install openjdk-11-jre
```

+ OpenJDK の確認

```
$ java --version
openjdk 11.0.9.1 2020-11-04
OpenJDK Runtime Environment (build 11.0.9.1+1-post-Raspbian-1deb10u2)
OpenJDK Server VM (build 11.0.9.1+1-post-Raspbian-1deb10u2, mixed mode)
```

+ Jenkins を apt 経由でインストールする
  + 参考 公式: https://www.jenkins.io/doc/book/installing/linux/

```
wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
sudo sh -c 'echo deb https://pkg.jenkins.io/debian-stable binary/ > \
    /etc/apt/sources.list.d/jenkins.list'
sudo apt-get update
sudo apt-get install jenkins

```
