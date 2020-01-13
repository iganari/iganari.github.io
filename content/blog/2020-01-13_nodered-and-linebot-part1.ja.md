+++
author = "iganari"
categories = ["Raspberry Pi", "Node-RED", "tech"]
date = "2020-01-13"
description = ""
# featured = "20200101_hikkoshi_title_wide.png"
featuredalt = "node-red_to_line_bot_part_1"
featuredpath = "date"
linktitle = ""
title = "Node-RED to LINE Bot Part 1 [日本語版]"
type = "post"

+++

# 注意点

この記事は下記の記事のアーカイブです ;)

+ https://iganari.hatenablog.com/entry/2020/01/13/212830

# Node-REDから LINE Bot に Push してみる Part.1

image

# やりたいこと

下記の記事を参考にして、自分でも Node-RED から LINE Bot に Push メッセージを送ってみようと思います。

+ Raspberry Pi やローカルPC で動かす Node-RED で Line Bot の Push メッセージを送るメモ
  + https://www.1ft-seabass.jp/memo/2020/01/03/local-node-red-linebot-push-message/

簡潔に纏めるため、数回に分けて記載します。

# Raspberry Pi を準備する

筐体は昔に使っていた Raspberry Pi 3 Model B を使用します。

{{< tweet 1211203592666804224 >}}

下記を参考にして microSD を初期化し、Rasbian を microSD に焼いていきます。

+ Raspberry Piに Raspbian をインストールする for Mac OSX
  + https://qiita.com/ttyokoyama/items/7afe6404fd8d3e910d09

OS は Raspbian を使用します。

+ Raspbian
  + https://www.raspberrypi.org/downloads/raspbian/

上記をダウンロードし、 microSD にイメージを焼きます。
作業は macOS から行っています。

microSD を物理的にマウントした後にターミナルから確認します。

```
$ df -h
Filesystem              Size   Used  Avail Capacity iused      ifree %iused  Mounted on
/dev/disk1              112Gi  108Gi  3.7Gi    97% 1818468 4293148811    0%   /
devfs                   187Ki  187Ki    0Bi   100%     646          0  100%   /dev
/dev/disk2s1            14Gi  2.4Mi   14Gi     1%       0          0  100%   /Volumes/Untitled
```
```
$ diskutil list
/dev/disk0 (internal, physical):
   #:                       TYPE NAME                    SIZE       IDENTIFIER
   0:      GUID_partition_scheme                        *121.3 GB   disk0
   1:                        EFI EFI                     209.7 MB   disk0s1
   2:          Apple_CoreStorage Macintosh HD            120.5 GB   disk0s2
   3:                 Apple_Boot Recovery HD             650.0 MB   disk0s3

/dev/disk1 (internal, virtual):
   #:                       TYPE NAME                    SIZE       IDENTIFIER
   0:                  Apple_HFS Macintosh HD           +120.1 GB   disk1
                                 Logical Volume on disk0s2

/dev/disk2 (internal, physical):
   #:                       TYPE NAME                    SIZE       IDENTIFIER
   0:     FDisk_partition_scheme                        *15.5 GB    disk2
   1:                 DOS_FAT_32                         15.5 GB    disk2s1
```

`/dev/disk2s1` が microSD なので、一度アンマウントします。

```
sudo diskutil umount /dev/disk2s1
sudo diskutil unmountDisk /dev/disk2
```
```
### 例

$ sudo diskutil umount /dev/disk2s1
Password:
Volume (null) on disk2s1 unmounted
$ sudo diskutil unmountDisk /dev/disk2
Unmount of all volumes on disk2 was successful
```

ダウンロードした Raspbian Buster with desktop のイメージの確認をします。

```
$ ls ~/Desktop/2019-09-26-raspbian-buster.img
/Users/iganari/Desktop/2019-09-26-raspbian-buster.img
```

上記のイメージを dd コマンドによって microSD への書き込みます。

```
sudo dd bs=1m if=~/Desktop/2019-09-26-raspbian-buster.img of=/dev/disk2
```

```
### 例

$ sudo dd bs=1m if=~/Desktop/2019-09-26-raspbian-buster.img of=/dev/disk2
3652+0 records in
3652+0 records out
3829399552 bytes transferred in 17100.346498 secs (223937 bytes/sec)
$
```

約5時間かかりました…😰

一度、 microSD を抜き差しします。

```
$ diskutil list
/dev/disk0 (internal, physical):
   #:                       TYPE NAME                    SIZE       IDENTIFIER
   0:      GUID_partition_scheme                        *121.3 GB   disk0
   1:                        EFI EFI                     209.7 MB   disk0s1
   2:          Apple_CoreStorage Macintosh HD            120.5 GB   disk0s2
   3:                 Apple_Boot Recovery HD             650.0 MB   disk0s3

/dev/disk1 (internal, virtual):
   #:                       TYPE NAME                    SIZE       IDENTIFIER
   0:                  Apple_HFS Macintosh HD           +120.1 GB   disk1
                                 Logical Volume on disk0s2

/dev/disk2 (internal, physical):
   #:                       TYPE NAME                    SIZE       IDENTIFIER
   0:     FDisk_partition_scheme                        *15.5 GB    disk2
   1:             Windows_FAT_32 boot                    268.4 MB   disk2s1
   2:                      Linux                         3.6 GB     disk2s2
```

`/dev/disk2` に `Linux` が追加されているのが分かります。

この microSD を Raspberry Pi に挿入して、起動してみます。

{{< tweet 1213207593591554048 >}}

やっと、起動に成功しました!!

# Raspbian Buster with desktop の初期設定をする

兎にも角にも Wifi を設定しましょう

この時、IEEE802.11b しか認識しなかったので調べたところ、Raspberry Pi 3 Model B+ から IEEE 802.11a に対応しており、今回使用した Raspberry Pi 3 Model B は IEEE802.11b/g/n のみ対応していることが分かりました。

+ Raspberry Pi 3 Model B
  + https://www.iodata.jp/product/pc/raspberrypi/ud-rp3/spec.htm
+ Raspberry Pi 3 Model B+
  + https://www.iodata.jp/product/pc/raspberrypi/ud-rp3bp/spec.htm

こういうところも事前に調べられれば良かったかもしれません… 	

気を取り直して、Raspberry Pi の初期設定を行っていきます。

## パッケージの更新

+ apt コマンドを用いて、パッケージをアップデートします。

```
sudo apt-get update
sudo apt-get upgrade
```

## SSH ログイン出来るように設定する

+ openssh-server をインストールします。

```
sudo apt-get install openssh-server
```

+ ssh のデーモンの自動起動を設定します。

```
sudo systemctl start ssh
sudo systemctl enable ssh
```

+ 上記の設定が正常に設定出来たかを確認するために、一度再起動してテストを行います。

```
sudo shutdown -r now
```

+ 再起動後、他の PC からこの Raspberry Pi に対して、SSHログインが出来れば設定は完了です。

## 標準のエディタの変更

Raspbian の標準エディタは nano が採択されています。しかし、 普段 nano を使わないユーザには戸惑うかも知れません。

そのような時は、標準エディタを変更してしまいましょう。下記は、標準エディタを Vim に変更するやり方です。

+ Vim のパッケージをインストールします。

```
sudo apt install vim
```

+ インストール出来ているか確認します。

```
sudo apt list --installed | grep vim
```
```
### 例

$ sudo apt list --installed | grep vim

WARNING: apt does not have a stable CLI interface. Use with caution in scripts.

vim-common/stable,now 2:8.1.0875-5 all [installed]
vim-runtime/stable,now 2:8.1.0875-5 all [installed,automatic]
vim-tiny/stable,now 2:8.1.0875-5 armhf [installed]
vim/stable,now 2:8.1.0875-5 armhf [installed]
```

+ 既存の標準エディタを確認します。

```
$ ls -la /etc/alternatives/editor
lrwxrwxrwx 1 root root 9 Sep 26 09:08 /etc/alternatives/editor -> /bin/nano
```

+ 既存の標準エディタの置き換えをします。

```
sudo unlink /etc/alternatives/editor
sudo ln -s /usr/bin/vim.basic /etc/alternatives/editor
```

+ 標準エディタの再確認します。

```
$ ls -la /etc/alternatives/editor
lrwxrwxrwx 1 root root 18 Jan  4 14:00 /etc/alternatives/editor -> /usr/bin/vim.basic
```

+ nano をアンインストールします。

```
sudo apt remove --purge nano
```

## 参考 URL

+ https://qiita.com/iganari/items/612a4b6f59a528c2f15a
+ https://qiita.com/iganari/items/587c330a62e52023679d

# まとめ

とりあえず、Raspberry Pi を換装するところまで出来たので、ここまで。

次回で Node-RED をインストールしていきます。
