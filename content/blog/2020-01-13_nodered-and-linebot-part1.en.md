+++
author = "iganari"
categories = ["Raspberry Pi", "Node-RED", "tech", "English"]
date = "2020-01-13"
description = ""
# featured = "20200101_hikkoshi_title_wide.png"
featuredalt = "node-red_to_line_bot_part_1 en"
featuredpath = "date"
linktitle = ""
title = "Node-RED to LINE Bot Part 1 [英語版]"
type = "post"
+++

# Attention

This is an archive of the articles below ;)

+ https://dev.to/iganari/try-push-line-bot-from-node-red-part-1-52f6

# Try push LINE Bot From Node-RED Part.1 

{{< figure src="/img/2020/01/20200113_computer_jisaku_bunkai.png" >}}

# Goal

Refer to the following article, I try push LINE Bot From Node-RED.

+ Raspberry Pi やローカルPC で動かす Node-RED で Line Bot の Push メッセージを送るメモ
  + https://www.1ft-seabass.jp/memo/2020/01/03/local-node-red-linebot-push-message/

Split the article into several pieces, because the contents is long.

# Set up Raspberry Pi

I use raspberry Pi 3 Model B what was used in the past.

{{< tweet 1211203592666804224 >}}

Refer to the following to initialize the microSD, Write Rasbian on microSD.

+ Raspberry Piに Raspbian をインストールする for Mac OSX
  + https://qiita.com/ttyokoyama/items/7afe6404fd8d3e910d09

Choise Raspbian.

+ Raspbian
  + https://www.raspberrypi.org/downloads/raspbian/

Download on Hostmachine, Write Rasbian on microSD.

Then My Work is running from macOS.

After pyhical mount micoSD on hostmachine, Check from your terminal.

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

`/dev/disk2s1` is microSD, therefore unmount from your terminal

```
sudo diskutil umount /dev/disk2s1
sudo diskutil unmountDisk /dev/disk2
```
```
### Example

$ sudo diskutil umount /dev/disk2s1
Password:
Volume (null) on disk2s1 unmounted
$ sudo diskutil unmountDisk /dev/disk2
Unmount of all volumes on disk2 was successful
```

Check image of Raspbian Buster with desktop

```
$ ls ~/Desktop/2019-09-26-raspbian-buster.img
/Users/iganari/Desktop/2019-09-26-raspbian-buster.img
```

Write Rasbian on microSD using dd command.

```
sudo dd bs=1m if=~/Desktop/2019-09-26-raspbian-buster.img of=/dev/disk2
```

```
### Example

$ sudo dd bs=1m if=~/Desktop/2019-09-26-raspbian-buster.img of=/dev/disk2
3652+0 records in
3652+0 records out
3829399552 bytes transferred in 17100.346498 secs (223937 bytes/sec)
$
```

It took about 5 hours to complete...

Physically remove and insert microSD for remount.

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

You can see that `Linux` has been added to `/ dev / disk2`.

Next, you insert this microSD in Raspberry Pi, and Power on.

{{< tweet 1213207593591554048 >}}

Success!!

# Set up Raspbian Buster with desktop

First, let's set up Wifi.

This time, Set up IEEE802.11b only.

Because Raspberry Pi 3 Model B can only set IEEE802.11b/g/n.

If Raspberry Pi 3 Model B+, I can set IEEE802.11a.

+ Raspberry Pi 3 Model B
  + https://www.iodata.jp/product/pc/raspberrypi/ud-rp3/spec.htm
+ Raspberry Pi 3 Model B+
  + https://www.iodata.jp/product/pc/raspberrypi/ud-rp3bp/spec.htm

I shoud have checked spec...	

I pulled myself together and Setting Raspbian.

## Updating packages

+ Updating packages using apt command.

```
sudo apt-get update
sudo apt-get upgrade
```

## Set up SSH login

+ Install openssh-server

```
sudo apt-get install openssh-server
```

+ Set the automatic startup of the ssh daemon.

```
sudo systemctl start ssh
sudo systemctl enable ssh
```

+ reboot for checking.

```
sudo shutdown -r now
```

+ After restarting, if you can SSH login to this Raspberry Pi from another PC, the setting is completed.

## Changing the standard editor

Raspbian's standard editor is nano. However, it may be confusing for users who do not normally use nano.

In such a case, change the standard editor. Here is how to change the standard editor to Vim.

+ install Vim package using apt command.

```
sudo apt install vim
```

+ Check that the installation has been completed.

```
sudo apt list --installed | grep vim
```
```
### Example

$ sudo apt list --installed | grep vim

WARNING: apt does not have a stable CLI interface. Use with caution in scripts.

vim-common/stable,now 2:8.1.0875-5 all [installed]
vim-runtime/stable,now 2:8.1.0875-5 all [installed,automatic]
vim-tiny/stable,now 2:8.1.0875-5 armhf [installed]
vim/stable,now 2:8.1.0875-5 armhf [installed]
```

+ Check the existing standard editor.

```
$ ls -la /etc/alternatives/editor
lrwxrwxrwx 1 root root 9 Sep 26 09:08 /etc/alternatives/editor -> /bin/nano
```

+ Replace the existing standard editor.

```
sudo unlink /etc/alternatives/editor
sudo ln -s /usr/bin/vim.basic /etc/alternatives/editor
```

+ Recheck the standard editor.

```
$ ls -la /etc/alternatives/editor
lrwxrwxrwx 1 root root 18 Jan  4 14:00 /etc/alternatives/editor -> /usr/bin/vim.basic
```

+ Uninstall nano.

```
sudo apt remove --purge nano
```

## References

+ https://qiita.com/iganari/items/612a4b6f59a528c2f15a
+ https://qiita.com/iganari/items/587c330a62e52023679d

# Conclusion

For now, I was able to replace the Raspberry Pi. That's it for today's work.

I will install Node-RED in the next article.

Thanks for reading this article :)
