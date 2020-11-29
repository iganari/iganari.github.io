+++
author = "iganari"
categories = ["GCP", "Serverless"]
date = "2020-11-25"
description = ""
featuredalt = "devcontainer"
featuredpath = "date"
linktitle = ""
title = "devcontainer"
type = "post"
+++

# 内容まとめ

VS Code の Dev Contaner を使ってみた

思っていたよりいいよ :)

# hoge

正確には 

Visual Studio Code の Remote Containers という機能

# メリット

ローカルに隔離された環境を手軽に作ることが出来る
ローカルをbindすることも出来る
コンテナを鬼道する時にvscodeの拡張機能を指定することが出来、コンテナ内でだけ使うことが出来る

とにかく、ローカルホストを汚さずに、かつ手軽に開発環境を作ることが出来る

例えるなら、昔の流行ったvagrantのコンテナ版

# 良い記事

サイオスさんの。

# 実際に作ってみた

Github

https://github.com/iganari/devcontainer_sample

↑ rename したい


### 特徴

+ gcloud, terraform コマンドを使う
  + Dockerfile
+ ホストの `.ssh` をマウントしている
  + `devcontainer.json` の `mounts`
+ VS Code の拡張機能を入れている
  + `devcontainer.json` の `extensions`

# 作り方際の作り方

テンプレートから作るのが良い

### ディレクトリを開く

コードを置いていくので VS Code でディレクトリを開く必要があります

今回は `vscode-test` という空のディレクトリを予め用意し、それを開きます




{{< figure src="/img/2020/11/vcode-devcontainer-01.png" >}}


{{< figure src="/img/2020/11/vcode-devcontainer-02.png" >}}
{{< figure src="/img/2020/11/vcode-devcontainer-03.png" >}}
{{< figure src="/img/2020/11/vcode-devcontainer-04.png" >}}
{{< figure src="/img/2020/11/vcode-devcontainer-05.png" >}}
{{< figure src="/img/2020/11/vcode-devcontainer-06.png" >}}


```
$ tree -a -L 3 vscode-test
vscode-test
└── .devcontainer
    ├── Dockerfile
    ├── devcontainer.json
    └── library-scripts
        ├── common-debian.sh
        └── docker-debian.sh

2 directories, 4 files
```




### 左下のメニュー(?)をクリック

{{< figure src="/img/2020/11/vcode-devcontainer-07.png" >}}

### `Remote - Container` をクリック

{{< figure src="/img/2020/11/vcode-devcontainer-08.png" >}}

### VS Code が再読み込みを行います

正常に再読み込み出来ると左下に `Dev Container` と出ます

{{< figure src="/img/2020/11/vcode-devcontainer-09.png" >}}

### docker のテスト

docker コマンドが実行可能だということが分かる

ホストマシンの Docker と連動しているのがわかります

{{< figure src="/img/2020/11/vcode-devcontainer-10.png" >}}


```
$ docker ps
CONTAINER ID        IMAGE                                              COMMAND                  CREATED             STATUS              PORTS               NAMES
b15c433ecbad        vsc-vscode-test-3cb0b3ccaa4656c8c325ac5c9e357dcd   "/usr/local/share/do…"   4 hours ago         Up 4 hours                              busy_williams
```

# VS Code 上で root で実行できるようにする

### `devcontainer.json` を編集

+ root で実行するようにする

```
### Before
// Comment out connect as root instead. More info: https://aka.ms/vscode-remote/containers/non-root.
"remoteUser": "vscode"
```
```
### After
// Comment out connect as root instead. More info: https://aka.ms/vscode-remote/containers/non-root.
// "remoteUser": "vscode"
```

### 反映

+ `Remote-Containers: Reopen in Container` をクリック

{{< figure src="/img/2020/11/vcode-devcontainer-11.png" >}}

### 確認

Dev Container の中では root になっていることを確認

{{< figure src="/img/2020/11/vcode-devcontainer-12.png" >}}



# VS Code の拡張機能を入れる

WIP

### 拡張機能を探す

{{< figure src="/img/2020/11/vcode-devcontainer-13.png" >}}

{{< figure src="/img/2020/11/vcode-devcontainer-14.png" >}}

### json を修正

+ Before

```
// Add the IDs of extensions you want installed when the container is created.
"extensions": [
	"ms-azuretools.vscode-docker"
],
```

+ After

```
// Add the IDs of extensions you want installed when the container is created.
"extensions": [
	"ms-azuretools.vscode-docker",
    "eamodio.gitlens",
    "mhutchie.git-graph",
],
```

## 反映

割愛

## 確認

+ GitLens と Git Graph が入っていることが分かる

{{< figure src="/img/2020/11/vcode-devcontainer-15.png" >}}























# ホストをマウントしてみる




### ssh の設定をマウント

WIP

-> 反映

# Dockerfile を修正して機能を追加する

WIP




# まとめ



お疲れさまでした :)

