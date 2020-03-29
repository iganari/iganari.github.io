+++
author = "iganari"
categories = ["macOS", "Raspberry Pi", "VNC Server"]
date = "2020-02-22"
description = ""
featuredalt = "vnc-raspberrypi-from-macos ja"
featuredpath = "date"
linktitle = ""
title = "macOS から Raspberry Pi に画面共有したい"
type = "post"
+++

# 注意点

この記事は下記の記事のアーカイブです ;)

+ https://iganari.hatenablog.com/entry/2020/02/22/110953

# macOS から Raspberry Pi に画面共有したい

# 結論

+ Raspberry Pi 側
  + 初期状態から組み込まれている vncserver を使いましょう。
+ macOS 側
  + サードパーティ製の VNC クライアントを使いましょう。
    + https://www.realvnc.com/en/connect/download/viewer/
  + なお、公式のサポートも手軽に使えるよとコメントしています。
    + https://www.raspberrypi.org/forums/viewtopic.php?t=229348

# Raspberry Pi 側 (LAN 内の IP アドレス = 192.168.202.118)

+ VNC について確認しましょう。

```
$ sudo systemctl list-unit-files | grep vnc
vncserver-virtuald.service             disabled
vncserver-x11-serviced.service         disabled


---> vncserver-x11-serviced があることが確認出来ました。
```

+ vncserver-x11-serviced を起動し、自動起動を設定しましょう。
  + vncserver-x11-serviced の詳細は以下を参照してください。
  + https://help.realvnc.com/hc/en-us/articles/360002310857-vncserver-x11-serviced-Man-Page

```
sudo systemctl start vncserver-x11-serviced.service
sudo systemctl enable vncserver-x11-serviced.service
```

+ 確認をします。

```
$ sudo systemctl status vncserver-x11-serviced.service
● vncserver-x11-serviced.service - VNC Server in Service Mode daemon
   Loaded: loaded (/usr/lib/systemd/system/vncserver-x11-serviced.service; enabled; vendor preset: enabled)
   Active: active (running) since Tue 2020-02-18 19:30:26 JST; 3 days ago
 Main PID: 2493 (vncserver-x11-s)
    Tasks: 6 (limit: 2200)
   Memory: 21.2M
   CGroup: /system.slice/vncserver-x11-serviced.service
           ├─2493 /usr/bin/vncserver-x11-serviced -fg
           ├─2497 /usr/bin/vncserver-x11-core -service
           ├─2506 /usr/bin/vncagent service 13
           ├─2510 /usr/bin/vncserverui service 17
           └─2518 /usr/bin/vncserverui -statusicon 5
```

Raspberry Pi 側 の設定は以上です。

# macOS 側

+ RealVNC for macOS をダウンロードし、インストールします。

https://www.realvnc.com/en/connect/download/viewer/

{{< figure src="/img/2020/02/200218-vnc-raspberrypi-from-macos-01.png" >}}

{{< figure src="/img/2020/02/200218-vnc-raspberrypi-from-macos-02.png" >}}

+ Applications にコピーしましょう。

{{< figure src="/img/2020/02/200218-vnc-raspberrypi-from-macos-03.png" >}}

+ その後、Launchpad から VNC Viewer を起動し、Raspberry Pi の IP アドレス (= 192.168.202.118) を入れ、Enter を押します。

{{< figure src="/img/2020/02/200218-vnc-raspberrypi-from-macos-04.png" >}}

+ 初回は下記のような確認画面が出ますので、確認して続けましょう。

{{< figure src="/img/2020/02/200218-vnc-raspberrypi-from-macos-05.png" >}}

+ Raspberry Pi にログイン出来るユーザ名とパスワードをいれます。
  + この時、VNC 用のユーザの新規作成は必要ありません。

{{< figure src="/img/2020/02/200218-vnc-raspberrypi-from-macos-06.png" >}}

+ 無事、VNC クライアントを用いて macOS から Raspberry Pi にアクセスすることが出来るようになりました。

{{< figure src="/img/2020/02/200218-vnc-raspberrypi-from-macos-07.png" >}}

# まとめ

Raspberry Pi 上の OS に対して、VNC する機会はあまり無いかと思いますが、いざ必要という際に設定するのはめんどうなので備忘として残しておきます :)

Have fun! :)
