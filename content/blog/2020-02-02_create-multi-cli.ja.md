+++
author = "iganari"
categories = ["Docker", "Docker Compose", "AWS", "Azure", "GCP"]
date = "2020-02-02"
description = ""
# featured = "20200101_hikkoshi_title_wide.png"
featuredalt = "create multi cli ja"
featuredpath = "date"
linktitle = ""
title = "Multi Cli を作りました [日本語版]"
type = "post"
+++

# 注意点

この記事は下記の記事のアーカイブです ;)

+ https://iganari.hatenablog.com/entry/2020/02/02/193227

# Multi Cli を作りました

# 成果物

https://github.com/iganari/multi-cli

# どんなものか

+ (今の所) AWS、Azure、GCP のCLI をコンテナ上で使えるようにします。
  + これにより、ホストマシンの環境を必要以上に汚すことはありません。
+ Docker Compose を用いることで、常時起動をより簡易にしています。
  + docker コマンドの `-d` でも出来ますが、管理の工数が増えるため、Docker Compose を使用しました。
+ さくっと CLI を使いたい時に重宝します(多分)
  + CLI を使いたいけど、Cloud Shell を起動するのもめんどいし…な時など!!(あるのか??)

# 使用方法

+ Repository を Clone し、`docker-compose` コマンドで実行します。

```
cd {Your work directory}
git clone https://github.com/iganari/multi-cli.git
cd multi-cli
```
```
docker-compose up -d
```
```
### 例

$ docker-compose up -d
Creating multi-cli-azure  ... done
Creating multi-cli-aws    ... done
Creating multi-cli-gcloud ... done
```

+ ちゃんと起動しているか、確認します。

```
docker-compose ps
```
```
$ docker-compose ps
      Name             Command       State   Ports
--------------------------------------------------
multi-cli-aws      /bin/sh           Up
multi-cli-azure    /bin/sh -c bash   Up
multi-cli-gcloud   /bin/sh           Up 
```

+ Docker Compose にて起動しているコンテナにログインします。

```
docker-compose exec aws    /bin/ash
docker-compose exec azure  /bin/ash
docker-compose exec gcloud /bin/ash
```
```
### 例(Azure のコンテナにて)

$ docker-compose exec azure /bin/ash
/ # 
/ # az version
This command is in preview. It may be changed/removed in a future release.
{
  "azure-cli": "2.0.80",
  "azure-cli-command-modules-nspkg": "2.0.3",
  "azure-cli-core": "2.0.80",
  "azure-cli-nspkg": "3.0.4",
  "azure-cli-telemetry": "1.0.4",
  "extensions": {}
}
/ #
```

# 停止する場合

+ 起動しっぱなしで特に問題は無いかと思いますが、停止したい場合は以下のコマンドで可能です。

```
docker-compose stop
```

# エイリアスを貼っておく

この Docker Compose を起動していて、いざ使いたい時にこの Repository のディレクトリに行かないといけないのは面倒ですよね :(

なので、自分の環境にエイリアスを貼っておきます。以下に Bash の場合の例を貼っておきます。

+ `~/.bashrc` に以下を追記します。

```
alias dg='cd ${Your Repository's PATH}/multi-cli && docker-compose exec gcloud /bin/bash && cd -' 
alias dz='cd ${Your Repository's PATH}/multi-cli && docker-compose exec azure /bin/bash  && cd -'
alias dw='cd ${Your Repository's PATH}/multi-cli && docker-compose exec azure /bin/bash  && cd -'
```

+ `~/.bashrc` の変更を読み込みます。

```
source ~/.bashrc
```

これで短いコマンドで実行が可能になりました :)

# まとめ

Have fun!! :)
