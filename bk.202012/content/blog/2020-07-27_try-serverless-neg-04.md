+++
author = "iganari"
categories = ["GCP", "Serverless"]
date = "2020-07-27"
description = ""
featuredalt = "Try Serverless NEG 04"
featuredpath = "date"
linktitle = ""
title = "Serverless NEG を試す ~リソースの削除~"
type = "post"
+++

# 構成

この記事の内容は長いため複数の記事に跨って作成しています。

適宜、興味のある記事を参照して下さい。

+ [Serverless NEG とは?](https://iganari.github.io/blog/2020-07-27_try-serverless-neg-00/)
+ [Cloud RUN, App Engine, Cloud Functions の準備](https://iganari.github.io/blog/2020-07-27_try-serverless-neg-01/)
+ [サブドメインの設定](https://iganari.github.io/blog/2020-07-27_try-serverless-neg-02/)
+ [External HTTP(S) Load Balancer の作成](https://iganari.github.io/blog/2020-07-27_try-serverless-neg-03/)
+ <本記事> [リソースの削除](https://iganari.github.io/blog/2020-07-27_try-serverless-neg-04/)

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

+ GCP Project の設定をしておきます。

```
gcloud config set project ${_pj_id}
```

+ サンプルコードをダウンロードし、本記事のサンプルコードが格納しているディレクトリに移動します。

```
cd [Your WorkSpace]
git clone https://github.com/iganari/package-gcp.git
cd package-gcp/compute/networkendpointgroups/serverless
```

# やること

+ ここまでに作成してきたリソースを削除します。

# リソースの削除

+ LB リソースの削除

```
gcloud compute forwarding-rules delete ${_common}-https-content-rule --global

gcloud compute target-https-proxies delete ${_common}-https-proxy

gcloud compute ssl-certificates delete ${_common}-www-ssl-cert 

gcloud compute url-maps remove-path-matcher ${_common}-url-map --path-matcher-name=${_common}-path-matcher

gcloud compute url-maps delete ${_common}-url-map 

gcloud compute backend-services delete ${_common}-backend-service-run --global

gcloud compute backend-services delete ${_common}-backend-service-app --global

gcloud compute backend-services delete ${_common}-backend-service-func --global
```

+ Serverless NEG リソースの削除

```
gcloud beta compute network-endpoint-groups delete ${_common}-serverless-neg-run --region=asia-northeast1

gcloud beta compute network-endpoint-groups delete ${_common}-serverless-neg-app --region=asia-northeast1 

gcloud beta compute network-endpoint-groups delete ${_common}-serverless-neg-func --region=asia-northeast1 
```

+ 静的 IP アドレスの開放

```
gcloud compute addresses delete ${_common}-example-ip --global
```

# まとめ

これでリソースの削除も完了です!!

お疲れさまでした :)
