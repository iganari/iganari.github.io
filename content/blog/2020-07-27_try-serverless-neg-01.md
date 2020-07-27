+++
author = "iganari"
categories = ["GCP", "Serverless"]
date = "2020-07-27"
description = ""
featuredalt = "Try Serverless NEG 01"
featuredpath = "date"
linktitle = ""
title = "Serverless NEG を試す ~Cloud RUN, App Engine, Cloud Functions の準備~"
type = "post"
+++

# 構成

この記事の内容は長いため複数の記事に跨って作成しています。

適宜、興味のある記事を参照して下さい。

+ [Serverless NEG とは?]()
+ [Cloud RUN, App Engine, Cloud Functions の準備]()
  + 本記事
+ [サブドメインの設定]()
+ [External HTTP(S) Load Balancer の作成]()
+ [リソースの削除]()

# 事前準備

+ GCP との認証をします。

```
gcloud auth login -q
```

+ GCP コマンドのため、環境変数を設定しておきます。

```
### New Setting
export _pj_id='Your GCP Project ID'
export _common='check-serverless-neg'
```

+ サンプルコードをダウンロードし、本記事のサンプルコードが格納しているディレクトリに移動します。

```
cd [Your WorkSpace]
git clone https://github.com/iganari/package-gcp.git
cd package-gcp/compute/networkendpointgroups/serverless
```

# やること

Serverless NEG の backend に設定する Cloud RUN, App Engine, Cloud Functions の準備をします。

# Cloud Run のサンプルの準備

+ Cloud Run のサンプルコードがあるディレクトリに移動します。

```
cd cloudrun
```

+ Cloud Run 上で使用するコンテナイメージを Google Container Registry (GCR) にデプロイします。

```
gcloud builds submit --tag gcr.io/${_pj_id}/${_common}-run
```

+ Cloud Run をデプロイします。

```
gcloud run deploy ${_common}-run \
    --image gcr.io/${_pj_id}/${_common}-run \
    --platform managed \
    --region asia-northeast1 \
    --allow-unauthenticated
```
```
### 例

# gcloud run deploy ${_common}-run \
>     --image gcr.io/${_pj_id}/${_common}-run \
>     --platform managed \
>     --region asia-northeast1 \
>     --allow-unauthenticated
Deploying container to Cloud Run service [check-serverless-neg-run] in project [~~~~~~~~~~] region [asia-northeast1]
✓ Deploying new service... Done.
  ✓ Creating Revision...
  ✓ Routing traffic...
  ✓ Setting IAM Policy...
Done.
Service [check-serverless-neg-run] revision [check-serverless-neg-run-00001-lut] has been deployed and is serving 100 percent of traffic at https://check-serverless-neg-run-3umtulj4sq-an.a.run.app
```

+ Web ブラウザで確認します。
  + 上述のデプロイコマンドの出力結果に以下の URL が出力されています。
  + https://check-serverless-neg-run-3umtulj4sq-an.a.run.app

{{< figure src="/img/2020/07/try-serverless-neg_sample-run.png" >}}

+ ルートディレクトリに戻ります。

```
cd -
```

# App Engine のサンプルの準備

+ App Engine のサンプルコードがあるディレクトリに移動します。

```
cd appengine
```

+ サンプルから app.yaml を作成します。

```
cat app.yaml.sample | sed "s/YOUR_SERVICE/${_common}-app/g" > app.yaml
```

+ App Engine にアプリをデプロイします。

```
gcloud app deploy
```

+ デプロイしたアプリの URL を確認します。

```
gcloud app browse -s ${_common}-app
```
```
### 例

# gcloud app browse -s ${_common}-app
Did not detect your browser. Go to this link to view your app:
https://check-serverless-neg-app-dot-[~~~~~~~~~~].an.r.appspot.com
```

+ Web ブラウザで確認します。
  + 上述のデプロイコマンドの出力結果に以下の URL が出力されています。
  + https://check-serverless-neg-app-dot-[~~~~~~~~~~].an.r.appspot.com

{{< figure src="/img/2020/07/try-serverless-neg_sample-app.png" >}}

+ ルートディレクトリに戻ります。

```
cd -
```

# Cloud Functions のサンプルの準備

```
cd functions
```
```
gcloud functions deploy func \
  --runtime python38 \
  --trigger-http \
  --region asia-northeast1 \
  --allow-unauthenticated
```

+ Web ブラウザで確認します。
  + GCP コンソールから、Functions の URL を確認することが出来ます。
  + https://check-serverless-neg-app-dot-[~~~~~~~~~~].an.r.appspot.com

{{< figure src="/img/2020/07/try-serverless-neg_sample-func.png" >}}

+ ルートディレクトリに戻ります。

```
cd -
```

# まとめ

これで Serverless NEG の backend に設定する Cloud RUN, App Engine, Cloud Functions の準備が出来ました!!

次は [サブドメインの設定]() をやっていきます!

Have fun! :)
