+++
title = "[新機能] Cloud Build Trigger が指定した時間に実行( Run on schedule )出来るようになったので試してみる"
date = 2021-02-15T00:00:00Z
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

Cloud Build の追加機能を試してみました :)

<!--more-->
---

# 注意点

この記事は下記の記事のアーカイブです ;)

```
WIP
```

# 概要

Cloud Build の Trigger を指定した時間に実行する機能が追加実装されました。

その機能をさっそく使ってみようと思います :)

なお、 2021 年 2 月の時点では `Preview` となっています。

### Cloud Build とは

GCP のフルマネージドの CI/CD 基盤であり、昨今の DevOps には欠かせない機能の一つです。

[Cloud Build](https://cloud.google.com/build)

### 今回の追加機能について

今までは Cloud Build を実行するトリガーは、連携している Repository に対してのイベントをフックするか、 API 的に外部から実行するしかありませんでした。

新しい追加機能は Cloud Build から Cloud Scheduler を直接設定することが出来るようになりました。

つまり…

今まで、 Cloud Build を定期実行させたい場合は自分で以下の構成をする必要がありました。

+ `Cloud Scheduler` -> `Cloud Pub/Sub` -> `Cloud Functions` -> `Cloud Build`

{{< figure src="/images/2021/02/15/01.png" >}}

今回の追加機能では以下のよう(※ ユーザが実装する部分という意味で)になります。

+ `Cloud Scheduler` -> `Cloud Build`

{{< figure src="/images/2021/02/15/02.png" >}}

意識して管理するコンポーネントが減るので運用負荷が減り、サービス開発においても出来ることが増えるので、早めに習得しておきましょう :)


### 公式ドキュメント

[Creating manual triggers | Scheduling your build](https://cloud.google.com/build/docs/automating-builds/create-manual-triggers#scheduling_your_build)

# 実際にやってみる

# Cloud Build Trigger を作成

### GCP コンソールより、 `Cloud Build` をクリック

{{< figure src="/images/2021/02/15/03.png" >}}

### Cloud Build の API を有効化する(初回のみ)

{{< figure src="/images/2021/02/15/04.png" >}}

### Cloud Build の画面( History )

{{< figure src="/images/2021/02/15/05.png" >}}

### Cloud Build の画面( Triggers ) に移動し、 `CREATE TRIGGER` をクリック

{{< figure src="/images/2021/02/15/06.png" >}}

### テスト的に Trigger を作成する

+ Name : `run-schedule-test` を記入
+ Event: `Manual invocation` を選択
+ Source: `cloudbuild.yaml が入っている リポジトリ`
    + サンプルとして下記のソースコードを使用しています
    + [GitHub: package-gcp/builds/sample](https://github.com/iganari/package-gcp/tree/main/builds/sample)

他の設定はデフォルトのままで。

設定を入れたら `CREATE` をクリック

{{< figure src="/images/2021/02/15/07.png" >}}

### Cloud Build Trigger の作成が完了したので `RUN` をクリック

{{< figure src="/images/2021/02/15/08.png" >}}

### main ブランチを選択し、 Trigger を実行

{{< figure src="/images/2021/02/15/09.png" >}}

### `History` をクリックし、実際の実行結果を確認するために実行中の Build をクリック

{{< figure src="/images/2021/02/15/10.png" >}}

### Build の内容を確認し、正常に実行出来ているかを確認する

{{< figure src="/images/2021/02/15/11.png" >}}

# 作成した Cloud Build Trigger を定期実行するように設定する

### Cloud Scheduler の API を有効化する

下記の URL を別タブで開き、 Cloud Scheduler の API を有効化する

https://console.cloud.google.com/marketplace/product/google/cloudscheduler.googleapis.com

{{< figure src="/images/2021/02/15/12.png" >}}

### 作成した Trigger のケバブメニュー(縦に3つの点があるボタン)をクリック

{{< figure src="/images/2021/02/15/13.png" >}}

### `Run on schedule` をクリック

{{< figure src="/images/2021/02/15/14.png" >}}

### Cloud Scheduler で使用する Service Account を選択し、 `CONTINUE` をクリック

デフォルトで選択されているものを使用する

{{< figure src="/images/2021/02/15/15.png" >}}

### Cloud Scheduler を実行するリージョンを選択するために、 `SET REGION` をクリック


{{< figure src="/images/2021/02/15/16.png" >}}

### Cloud Scheduler を実行するリージョンを選択する

`asia-northeast1` を選択

{{< figure src="/images/2021/02/15/17.png" >}}

### Cloud Scheduler の設定をする

+ Name : `Cloud Scheduler の Job 名`
+ Frequency: `繰り返しの設定`
+ TimeZone: `繰り返しの設定のタイムゾーン`

今回は 5 分毎に実行するように設定する

{{< figure src="/images/2021/02/15/18.png" >}}

### 作成完了

{{< figure src="/images/2021/02/15/19.png" >}}

### History をクリックし、定期実行されるのを待つ

{{< figure src="/images/2021/02/15/20.png" >}}

### 1 時間くらい待った結果、ちゃんと 5 分毎に実行されているのが分かる

{{< figure src="/images/2021/02/15/21.png" >}}

# まとめ

今回は Cloud Build の追加機能を試してみました。

サービス開発がより効率的に出来るようになるので、 GA (General Availability) が楽しみですね!!

なお繰り返しになりますが、この記事を書いている時点ではこの追加機能は Preview なので、ご利用は自己責任かつ計画的にお願いします。


Have fun !! :)
