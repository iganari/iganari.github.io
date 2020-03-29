+++
author = "iganari"
categories = ["Node-RED", "Docker", "Docker Compose"]
date = "2020-02-07"
description = ""
# featured = "20200101_hikkoshi_title_wide.png"
featuredalt = "update-nodered-container ja"
featuredpath = "date"
linktitle = ""
title = "nodered-dockercompose を Node-RED v1.0.3 対応させました [日本語版]"
type = "post"
+++

# 注意点

この記事は下記の記事のアーカイブです ;)

+ https://iganari.hatenablog.com/entry/2020/02/07/084441

# nodered-dockercompose ってそもそも何 ??

+ 自前の Repository です。
  + Docker Compose を用いて、 Node-RED (後述) をマシン上に構築することが出来ます。

https://github.com/iganari/nodered-dockercompose

+ 今回は、Node-RED の v1 がリリースしたので、上記の Repository もアップデートしました。
  + (正確には v1.0.3 にアップデートしました。)

# Node-RED とは??

### 公式サイト

https://nodered.org/about/

### どんなものか

+ 公式ドキュメントから抜粋します。

```
Node-RED is a flow-based programming tool, originally developed by IBM’s Emerging Technology Services team and now a part of the JS Foundation.
===============================
Node-RED はフローベースドプログラミング (flow-based programming) ツールであり、元は IBM Emerging Technology Services チームによって開発され、JS Foundation 配下のプロジェクトを経て、2019 年 3 月よ りNode.js Foundation と JS Foundation が合併して設立された OpenJS Foundation にホストされています。
```

### コミュニティなど

+ Node-RED日本ユーザ会

https://nodered.jp/

+ Connpass

https://node-red.connpass.com/

### その他特徴

+ 最近 v1 がやっとリリースされました!!
  + https://github.com/node-red/node-red-docker/releases/tag/v1.0.0
+ Raspberry Pi などにインストールを行い、IoT などの分野で活躍しています。
+ 独自にカスタマイズをし、商用利用されているケースもあります。


# nodered-dockercompose を使った Node-RED の使用方法

+ clone します。

```
cd ${Your WorkDirectory}
git https://github.com/iganari/nodered-dockercompose.git
cd nodered-dockercompose
```

+ docker-compose コマンドで起動します。

```
docker-compose up -d
```

+ 状態の確認コマンド

```
docker-compose ps
```
```
### 例

$ docker-compose ps
         Name                      Command                      State                   Ports
------------------------------------------------------------------------------------------------------
nodered-docker-compose   npm start -- --userDir /data   Up (health: starting)   0.0.0.0:1880->1880/tcp
```

+ ブラウザでの確認します。
  + http://127.0.0.1:1880

{{< figure src="/img/2020/02/200206-update-nodered-container-01.png" >}}

+ 停止も docker-compose コマンドで行います。

```
docker-compose stop
```

# 今回のアップデートで修正した点などなど

+ 自分で Dockerfile の中でいろいろインストールしていたところを、公式の Repository をものを使用することにしました。
  + メンテナンス性を考慮したため。
+ docker-compose.yml の書き方も公式の Repository に沿って修正しました。
  + https://github.com/node-red/node-red-docker/#docker-stack--docker-compose

# まとめ

Node-RED 自体の構築はシンプルなのでこれくらいで。

あとは、「Node-RED を用いて、何を実現するか!?」 がポイントになります。

とりあえず、LINE Bot や Twitter に繋げて遊んでみたいと思います!!

Have fun! :)
