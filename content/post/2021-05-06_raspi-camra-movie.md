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

01 の続きです


# 実装

+ MJPG-streamer をインストールする
  + 一旦、 データ置き場を作る ---> `mjpg-streamer`

```
mkdir ~/mjpg-streamer
cd ~/mjpg-streamer
```

+ 以下を使う

https://github.com/jacksonliam/mjpg-streamer

上記の README にインストール方法が書いてある


```
This is a fork of http://sourceforge.net/projects/mjpg-streamer/ with added support for the Raspberry Pi camera via the input_raspicam plugin.
```

+ 準備

```
sudo apt update
sudo apt install -y cmake libjpeg8-dev gcc g++
```

+ インストール

```
git clone https://github.com/jacksonliam/mjpg-streamer
cd mjpg-streamer/mjpg-streamer-experimental
make
sudo make install
```

+ run.sh

```
sudo ./mjpg_streamer -i "./input_uvc.so -f 10 -r 640x480 -d /dev/video0 -y -n" -o "./output_http.so -w ./www -p 8080 -c admin:password"
```
```
sudo bash ./run.sh
```
```
$ sudo bash ./run.sh
MJPG Streamer Version: git rev: 310b29f4a94c46652b20c4b7b6e5cf24e532af39
 i: Using V4L2 device.: /dev/video0
 i: Desired Resolution: 640 x 480
 i: Frames Per Second.: 10
 i: Format............: YUYV
 i: JPEG Quality......: 80
 i: TV-Norm...........: DEFAULT
 o: www-folder-path......: ./www/
 o: HTTP TCP port........: 8080
 o: HTTP Listen Address..: (null)
 o: username:password....: admin:password
 o: commands.............: enabled
```


この状態で Web ブラウザでみることが出来る

http://192.168.202.101:8080/index.html

Static -> 静止画 -> ブラウザを再読込すると最新の静止画になる
Stream -> 動画を見れる


動画だけを見たい場合は

```
http://192.168.202.101:8080/?action=stream
```

たぶん、これを HTML に入れ込めばいい


チューンニング

```
sudo ./mjpg_streamer -i "./input_uvc.so -f 15 -r 1280x720 -d /dev/video0 -y -n" -o "./output_http.so -w ./www -p 8080 -c admin:password"
```

root で動くようにする

sudo cp -r ~/mjpg-streamer/mjpg-streamer/mjpg-streamer-experimental /usr/local/bin/



sudo su -

cd /usr/local/bin/mjpg-streamer-experimental
./mjpg_streamer -i "./input_uvc.so -f 15 -r 1280x720 -d /dev/video0 -y -n" -o "./output_http.so -w ./www -p 8080 -c admin:password"










## デーモン化

以下のファイルを作成する


```
# cat /etc/systemd/system/multi-user.target.wants/mjpg-streamer.service

[Unit]
Description=mjpg streamer
# After=network.target nss-lookup.target

[Service]
Type = simple

ExecStart = cd /usr/local/bin/mjpg-streamer-experimental && ./mjpg_streamer -i "./input_uvc.so -f 15 -r 1280x720 -d /dev/video0 -y -n" -o "./output_http.so -w ./www -p 8080 -c admin:password"
Restart = always

[Install]
WantedBy=multi-user.target
```




vim /etc/systemd/system/mjpg-streamer.service







```
systemctl daemon-reload
```

```
systemctl list-unit-files --type=service | grep mjpg-streamer
```
```
# systemctl list-unit-files --type=service | grep mjpg-streamer
mjpg-streamer.service                              disabled
```

```
systemctl enable mjpg-streamer
systemctl start mjpg-streamer
systemctl stop mjpg-streamer
```

journalctl -u mjpg-streamer




systemctl status mjpg-streamer


