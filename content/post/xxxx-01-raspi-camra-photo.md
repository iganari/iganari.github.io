+++
title = "raspi-camera-nodered"
date = 2021-03-31T00:00:00Z
tags = [
    "GCP",
    "Cloud Build",
    "Cloud Scheduler",
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

この記事は下記の記事のアーカイブです ;)

WIP

# 実装

+ Raspberry PI

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



+ USB 接続を確認

```
lsusb
``

```
### 例

$ lsusb
Bus 001 Device 006: ID 046d:081b Logitech, Inc. Webcam C310
Bus 001 Device 003: ID 0424:ec00 Standard Microsystems Corp. SMSC9512/9514 Fast Ethernet Adapter
Bus 001 Device 002: ID 0424:9514 Standard Microsystems Corp. SMC9514 Hub
Bus 001 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
```

## 写真取る

+ コマンドインストール

```
sudo apt install -y fswebcam
```

+ 写真を取る

```
mkdir camera-test
cd camera-test

```
```
fswebcam image.jpg
```

---> カメラの横のライトが一瞬光る -> 動画を撮りたい

+ イメージができてる

```
$ ls
image.jpg
```

raspi からダウンロードして見てみる

+ IMG01
+ IMG02

---> チューニングの必要がありそう…
