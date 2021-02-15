+++
title = "[新機能] Cloud Build Trigger が定期実行( Run on schedule )出来るようになったので試してみる"
date = 2021-02-13T00:00:00Z
tags = [
    "GCP",
    "Cloud Build"
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

2021 年 2 月の時点では `Pre-GA` となっているので、所謂 `β版` となります

Cloud Scheduler を Cloud Build のページから設定していく

Cloud Build とは GCP のフルマネージド CI/CD 機能であり、昨今の DevOps には欠かせない機能です

この Cloud Build の実行トリガーは連携している Repository のイベントか、API 的に外部から実行するしかありませんでした

今回の新機能は GCP のサービスの一つである Cloud Scheduler を使うことで、定期実行を設定できるようにするものです

今まで、 Cloud Build を定期実行させたい場合は自分で以下の構成をする必要がありました

```
Cloud Scheduler -> Pub/Sub -> Cloud functions -> Cloud Build
```

今回の新機能では以下のよう(※ ユーザが実装する部分という意味で)になります

```
Cloud Scheduler -> Cloud Build
```

Cloud Build を使った DevOps にて出来ることが増えるので、早めに習得しておきましょう :)


### 公式ドキュメント

[Creating manual triggers | Scheduling your build](https://cloud.google.com/build/docs/automating-builds/create-manual-triggers#scheduling_your_build)


# 実際にやってみる

# Cloud Build Trigger を作成

### GCP コンソールより、 `Cloud Build` をクリック

{{< figure src="/images/2021/02/13/01.png" >}}

### Cloud Build の API を有効化する(初回のみ)

{{< figure src="/images/2021/02/13/02.png" >}}

### Cloud Build の画面( History )

{{< figure src="/images/2021/02/13/03.png" >}}

### Cloud Build の画面( Triggers ) に移動し、 `CREATE TRIGGER` をクリック

{{< figure src="/images/2021/02/13/04.png" >}}

### テスト的に Trigger を作成する

+ Name : `run-schedule-test` を記入
+ Event: `Manual invocation` を選択
+ Source: `cloudbuild.yaml が入っている リポジトリ`
    + サンプルとして下記のソースコードを使用しています
    + [GitHub: package-gcp/builds/sample](https://github.com/iganari/package-gcp/tree/main/builds/sample)

他の設定はデフォルトのままで。

設定を入れたら `CREATE` をクリック

{{< figure src="/images/2021/02/13/05.png" >}}

### Cloud Build Trigger の作成が完了したので `RUN` をクリック

{{< figure src="/images/2021/02/13/06.png" >}}

### main ブランチを選択し、 Trigger を実行

{{< figure src="/images/2021/02/13/07.png" >}}

### `History` をクリックし、実際の実行結果を確認するために実行中の Build をクリック

{{< figure src="/images/2021/02/13/08.png" >}}

### Build の内容を確認し、正常に実行出来ているかを確認する

{{< figure src="/images/2021/02/13/09.png" >}}

# 作成した Cloud Build Trigger を定期実行するように設定する

### Cloud Scheduler の API を有効化する

下記の URL を別タブで開き、 Cloud Scheduler の API を有効化する

https://console.cloud.google.com/marketplace/product/google/cloudscheduler.googleapis.com

{{< figure src="/images/2021/02/13/10.png" >}}

### 作成した Trigger のケバブメニュー(縦に3つの点があるボタン)をクリック

{{< figure src="/images/2021/02/13/11.png" >}}

### `Run on schedule` をクリック

{{< figure src="/images/2021/02/13/12.png" >}}

### Cloud Scheduler で使用する Service Account を選択する

デフォルトで選択されているものを使用する

{{< figure src="/images/2021/02/13/13.png" >}}

### Cloud Scheduler を実行するリージョンを選択する

`asia-northeast1` を選択

{{< figure src="/images/2021/02/13/14.png" >}}

### Cloud Scheduler の設定をする

+ Name : `Cloud Scheduler の Job 名`
+ Frequency: `繰り返しの設定`
+ TimeZone: `繰り返しの設定のタイムゾーン`

今回は 5 分毎に実行するように設定する

{{< figure src="/images/2021/02/13/15.png" >}}

### 作成完了

{{< figure src="/images/2021/02/13/16.png" >}}

### History をクリックし、定期実行されるのを待つ

{{< figure src="/images/2021/02/13/17.png" >}}

### 1 時間くらい待った結果、ちゃんと 5 分毎に実行されているのが分かる

{{< figure src="/images/2021/02/13/18.png" >}}

# まとめ

:)