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

### 左下のメニューをクリック

ディレクトリを開くと最下部が紫から青に変わる

{{< figure src="/img/2020/11/vcode-devcontainer-03.png" >}}

### WIP

{{< figure src="/img/2020/11/vcode-devcontainer-04.png" >}}

### Docker from Docker を選択

{{< figure src="/img/2020/11/vcode-devcontainer-05.png" >}}

### 全部選択

{{< figure src="/img/2020/11/vcode-devcontainer-06.png" >}}

### `.devcontaner` が追加されました


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

+ git の登録をしておきます

```
git init .
```

{{< figure src="/img/2020/11/vcode-devcontainer-07.png" >}}

### 左下のメニュー(?)をクリック

{{< figure src="/img/2020/11/vcode-devcontainer-08.png" >}}

### `Remote - Container` をクリック

{{< figure src="/img/2020/11/vcode-devcontainer-09.png" >}}

### VS Code が再読み込みを行います

正常に再読み込み出来ると左下に `Dev Container` と出ます

{{< figure src="/img/2020/11/vcode-devcontainer-10.png" >}}

### docker のテスト

docker コマンドが実行可能だということが分かる

ホストマシンの Docker と連動しているのがわかります

{{< figure src="/img/2020/11/vcode-devcontainer-11.png" >}}


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

{{< figure src="/img/2020/11/vcode-devcontainer-12.png" >}}

### 確認

Dev Container の中では root になっていることを確認

{{< figure src="/img/2020/11/vcode-devcontainer-13.png" >}}



# VS Code の拡張機能を入れる

WIP

### 拡張機能を探す

{{< figure src="/img/2020/11/vcode-devcontainer-14.png" >}}

{{< figure src="/img/2020/11/vcode-devcontainer-15.png" >}}

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

{{< figure src="/img/2020/11/vcode-devcontainer-16.png" >}}


# ホストをマウントしてみる

+ `.ssh` というディレクトリと `.gitconfig` というファイルをマウント

### `devcontainer.json` を編集

+ Before

```
"mounts": [ "source=/var/run/docker.sock,target=/var/run/docker-host.sock,type=bind" ],
```

+ After

```
"mounts": [
    "source=/var/run/docker.sock,target=/var/run/docker-host.sock,type=bind",
    "source=${localEnv:HOME}/.gitconfig,target=/root/.gitconfig,type=bind,consistency=cached",
    "source=${localEnv:HOME}/.ssh,target=/root/.ssh,type=bind,consistency=cached",   
],
```

### 反映

contaienr のリビルド

-> 反映





# Dockerfile を修正して機能を追加する

gcloud コマンドを入れてみます

### Dockerfile をアップデート

+ 下記を Dockerfile の最後に追加

```
RUN echo "deb [signed-by=/usr/share/keyrings/cloud.google.gpg] https://packages.cloud.google.com/apt cloud-sdk main" | tee -a /etc/apt/sources.list.d/google-cloud-sdk.list
RUN curl https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key --keyring /usr/share/keyrings/cloud.google.gpg add -
RUN apt-get update && apt-get install -y google-cloud-sdk
```

### 反映

コンテナのリビルド

### 確認

```
$ gcloud --version
Google Cloud SDK 319.0.0
alpha 2020.11.13
beta 2020.11.13
bq 2.0.62
core 2020.11.13
gsutil 4.55
kubectl 1.16.13
```



# まとめ



お疲れさまでした :)

