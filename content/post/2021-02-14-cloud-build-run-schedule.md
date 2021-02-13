+++
title = "Cloud Build Trigger が定期実行( Run on schedule )出来るようになったので試してみる"
date = 2021-02-14T00:00:00Z
tags = [
    "gcp"
]
draft = true # `true` or `false`
toc = true
backtotop = true
disable_comment = true
+++

Cloud Build の新機能を試してみました :)

<!--more-->
---

# 注意点

この記事は下記の記事のアーカイブです ;)

```
WIP
```

# 概要

Cloud Build の Trigger を定期実行する機能が追加実装された

Cloud Scheduler を Cloud Build のページから設定していく

今までは自分で以下の構成をする必要があった

Cloud Scheduler -> Pub/Sub -> Cloud functions -> Cloud Build

この機能追加では以下のよう(※ ユーザが実装する部分という意味で)

Cloud Scheduler -> Cloud Build


cloud-build-run-schedule

# 実際にやってみる

# Cloud Build Trigger を作成

### GCP コンソールより、 `Cloud Build` をクリック

[f:id:nari_kyu:20210212173815p:plain]

### API を有効化する(初回のみ)

[f:id:nari_kyu:20210212173224p:plain]

### Cloud Build の設定画面( History )

[f:id:nari_kyu:20210212173227p:plain]

### Cloud Build の設定画面( Trigger ) に移動し、 `CREATE TRIGGER` をクリック

[f:id:nari_kyu:20210212174114p:plain]

### テスト的に Trigger を作成する

+ Name : `run-schedule-test`
+ Event: `Manual invocation`
+ Source: `cloudbuild.yaml が入っている リポジトリ` ※1  

後はデフォルトのままでよい

設定を入れたら `CREATE` をクリック


[f:id:nari_kyu:20210212173235p:plain]

+ Cloud Build Trigger の作成完了したので `RUN` を実行してみる

[f:id:nari_kyu:20210213104447p:plain]

+ main ブランチを実行

[f:id:nari_kyu:20210213105150p:plain]

+ `history` をクリックし、実際の実行結果を確認するために実行中の Build をクリック

[f:id:nari_kyu:20210212173250p:plain]

+ Build の内容を確認し、正常に実行出来ているかを確認する

[f:id:nari_kyu:20210212173254p:plain]


# 作成した Cloud Build Trigger を定期実行するように設定する

###Cloud Scheduler  API を有効にする

[https://console.cloud.google.com/marketplace/product/google/cloudscheduler.googleapis.com]

[f:id:nari_kyu:20210213104903p:plain]

###作成した Trigger のケバブメニュー(縦に3つの点があるボタン)をクリック

[f:id:nari_kyu:20210213110313p:plain]

###`Run on schedule` をクリック

[f:id:nari_kyu:20210213110015p:plain]

###Cloud Scheduler で使用する Service Account を選択する画面になるのでデフォルトで選択されているものを使用する

[f:id:nari_kyu:20210213110019p:plain]

###Cloud Scheduler を実行するリージョンを選択する画面では `asia-northeast1` をクリック

[f:id:nari_kyu:20210213110022p:plain]

###`Name( Cloud Scheduler の Job 名)` と `Frequency( 繰り返しの設定 )` と `TimeZone` を設定する

今回は 5 分毎に実行するように設定した 

[f:id:nari_kyu:20210213110026p:plain]

###作成完了

[f:id:nari_kyu:20210213110030p:plain]

### History をクリックし、定期実行されるのを待つ

[f:id:nari_kyu:20210213110033p:plain]

### 1 時間くらい待った結果、ちゃんと 5 分毎に実行されているのが分かる

[f:id:nari_kyu:20210213110036p:plain]

# まとめ

※1   サンプルとして右記のソースコードを使用しています ---> [GitHub: package-gcp/builds/sample](https://github.com/iganari/package-gcp/tree/main/builds/sample)

