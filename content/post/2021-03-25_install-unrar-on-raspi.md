+++
title = "Raspbian で unrar コマンドが古かったので最新にした"
date = 2021-03-25T00:00:00Z
tags = [
    "Raspbian",
    "Raspberry Pi",
    "unrar",
    "Linux",
]
draft = false # `true` or `false`
toc = true
backtotop = true
disable_comment = true
+++

unrar コマンドをアップデートする話です :)

<!--more-->
---

# 注意点

この記事は下記の記事のアーカイブです ;)

https://iganari.hatenablog.com/entry/2021/03/25/212336

# 概要

Raspberry PI 専用の OS である、 Raspbian にて unrar コマンドが古かったので最新にしました

インターネット上で転がっている情報だとすこし情報が足りなかったので、今回の記事にまとめました

# 実際にやってみる

### 確認

+ OS のバージョンの確認

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

+ アーキテクトの確認

```
$ uname -a
Linux raspberrypi 5.10.17-v7+ #1403 SMP Mon Feb 22 11:29:51 GMT 2021 armv7l GNU/Linux
```

+ 現状の unrar の確認

```
$ unrar --version
unrar 0.0.1

---> これが古すぎる
```

+ 実行ファイルの確認

```
$ which unrar
/usr/bin/unrar
```
```
$ ls -la /usr/bin/ | grep unrar
lrwxrwxrwx  1 root root         23 Mar 24 06:26 unrar -> /etc/alternatives/unrar
-rwxr-xr-x  1 root root      31004 Oct 16  2017 unrar-free
```
```
$ ls -la /etc/alternatives/unrar
lrwxrwxrwx 1 root root 19 Mar 24 06:26 /etc/alternatives/unrar -> /usr/bin/unrar-free
```

### unrar のアンインストール

+ 古い unrar のアンインストール

```
sudo apt remove unrar unrar-free
```

+ 確認
  + 何も出なければOK

```
which unrar
ls -la /usr/bin/ | grep unrar
```

### 新しい unrar のインストール

+ リポジトリの参照先を追加

```
echo "deb-src http://mirrordirector.raspbian.org/raspbian/ $(cat /etc/os-release | grep VERSION_CODENAME | awk -F\= '{print $2}') main contrib non-free rpi" | sudo tee -a /etc/apt/sources.list
```

+ 確認
  + 既存の deb-src をコメントアウトを取ればよいだけの可能性もある…

```
$ cat /etc/apt/sources.list
deb http://raspbian.raspberrypi.org/raspbian/ buster main contrib non-free rpi
# Uncomment line below then 'apt-get update' to enable 'apt-get source'
#deb-src http://raspbian.raspberrypi.org/raspbian/ buster main contrib non-free rpi
deb-src http://mirrordirector.raspbian.org/raspbian/ buster main contrib non-free rpi
```

+ apt のアップデート

```
sudo apt update
```

+ 作業ディレクトリを作成

```
mkdir -p /tmp/unrar-nonfree && chmod 0777 /tmp/unrar-nonfree && cd /tmp/unrar-nonfree
```

+ unrar-nonfree に必要な依存関係をインストール

```
sudo apt build-dep unrar-nonfree
```

+ unrar-nonfree ソースをダウンロードし、 .deb パッケージをビルド

```
sudo apt source -b unrar-nonfree
```

+ 生成された .deb パッケージをインストール
  + unrar-nonfree のバージョンによって異なるため * を使用

```
sudo dpkg -i unrar*.deb
```

---> これでインストールが出来ました :)

### 新しい unrar の確認

+ Version の確認
  + オプションが `version` なので注意
  + help みたいにオプションの説明も入ってくるので `head` で適宜情報をカットする

```
unrar version
```
```
### 例


$ unrar version | head -n 6

UNRAR 5.61 beta 1 freeware      Copyright (c) 1993-2018 Alexander Roshal

Usage:     unrar <command> -<switch 1> -<switch N> <archive> <files...>
               <@listfiles...> <path_to_extract\>
```

+ 実行ファイルの確認

```
$ which unrar
/usr/bin/unrar
```
```
$ ls -la /usr/bin/ | grep unrar
lrwxrwxrwx  1 root root         23 Mar 24 06:40 unrar -> /etc/alternatives/unrar
-rwxr-xr-x  1 root root     389048 Oct 24  2018 unrar-nonfree
```
```
$ ls -la /etc/alternatives/unrar
lrwxrwxrwx 1 root root 22 Mar 24 06:40 /etc/alternatives/unrar -> /usr/bin/unrar-nonfree
```

---> 今度は unrar-nofree が実体となっていることが分かります

+ .deb パッケージを削除

```
cd && rm -rv /tmp/unrar-nonfree
```

### 注意点

```
sudo apt source -b unrar-nonfree
```

このコマンド実行中に `_apt` ユーザで一部作業をするので、 上記のコマンドは `_apt` ユーザがアクセスし作業出来るディレクトリでやる必要があります

```
### 権限エラーの例

W: Download is performed unsandboxed as root as file 'unrar-nonfree_5.6.6-1.dsc' couldn't be accessed by user '_apt'. - pkgAcquire::Run (13: Permission denied)
```

### 参考

```
VadimBrodsky/install-unrar-nonfree-raspbian.sh
https://gist.github.com/VadimBrodsky/1f567067e2cd438312bb9fd57095a806
```

# まとめ

これで unrar コマンドのアップデート出来ました

Have fan !! :)
