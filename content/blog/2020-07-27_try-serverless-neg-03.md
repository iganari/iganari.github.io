+++
author = "iganari"
categories = ["GCP", "Serverless"]
date = "2020-07-27"
description = ""
featuredalt = "Try Serverless NEG 03"
featuredpath = "date"
linktitle = ""
title = "Serverless NEG を試す ~External HTTP(S) Load Balancer の作成~"
type = "post"
+++

# 構成

この記事の内容は長いため複数の記事に跨って作成しています。

適宜、興味のある記事を参照して下さい。

+ [Serverless NEG とは?](https://iganari.github.io/blog/2020-07-27_try-serverless-neg-00/)
+ [Cloud RUN, App Engine, Cloud Functions の準備](https://iganari.github.io/blog/2020-07-27_try-serverless-neg-01/)
+ [サブドメインの設定](https://iganari.github.io/blog/2020-07-27_try-serverless-neg-02/)
+ <本記事> [External HTTP(S) Load Balancer の作成](https://iganari.github.io/blog/2020-07-27_try-serverless-neg-03/)
+ [リソースの削除](https://iganari.github.io/blog/2020-07-27_try-serverless-neg-04/)

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

+ External HTTP(S) Load Balancer の作成します。

# External HTTP(S) Load Balancer の作成

## 各種の Serverless NEG を作成する

+ Cloud Run の Serverless NEG を作成する

```
gcloud beta compute network-endpoint-groups create ${_common}-serverless-neg-run \
    --region=asia-northeast1 \
    --network-endpoint-type=SERVERLESS  \
    --cloud-run-service=${_common}-run
```

+ App Engine の Serverless NEG を作成する

```
gcloud beta compute network-endpoint-groups create ${_common}-serverless-neg-app \
    --region=asia-northeast1 \
    --network-endpoint-type=SERVERLESS  \
    --app-engine-service=${_common}-app
```

+ Cloud Functions の Serverless NEG を作成する

```
gcloud beta compute network-endpoint-groups create ${_common}-serverless-neg-func \
    --region=asia-northeast1 \
    --network-endpoint-type=SERVERLESS  \
    --cloud-function-name=func
```

+ 作成した NEG を確認します。
  + :warning: 2020年7月現在は、GCP コンソール上では確認することが出来ません(※ β版)

```
gcloud beta compute network-endpoint-groups list
```
```
### 例

# gcloud beta compute network-endpoint-groups list
NAME                                     LOCATION         ENDPOINT_TYPE  SIZE
check-serverless-neg-serverless-neg-app   asia-northeast1  SERVERLESS     0
check-serverless-neg-serverless-neg-func  asia-northeast1  SERVERLESS     0
check-serverless-neg-serverless-neg-run   asia-northeast1  SERVERLESS     0
```

## 各種の Backend Service を作成する

+ Cloud Run 用の Backend Service を作成します。

```
gcloud compute backend-services create ${_common}-backend-service-run \
    --global
```

+ App Engine 用の Backend Service を作成します。

```
gcloud compute backend-services create ${_common}-backend-service-app \
    --global
```

+ Cloud Functions 用の Backend Service を作成します。

```
gcloud compute backend-services create ${_common}-backend-service-func \
    --global
```

+ Backend Service を確認します。

```
gcloud compute backend-services list
```
```
### 例

# gcloud compute backend-services list
NAME                                      BACKENDS  PROTOCOL
check-serverless-neg-backend-service-app            HTTP
check-serverless-neg-backend-service-func           HTTP
check-serverless-neg-backend-service-run            HTTP
```

## Backend Service に Cloud Run 用の Serverless NEG を設定する

+ Cloud Run 用の Backend Service に Cloud Run 用の Serverless NEG を設定します。

```
gcloud beta compute backend-services add-backend ${_common}-backend-service-run \
    --global \
    --network-endpoint-group=${_common}-serverless-neg-run \
    --network-endpoint-group-region=asia-northeast1
```

+ App Engine 用の Backend Service に App Engine 用の Serverless NEG を設定します。

```
gcloud beta compute backend-services add-backend ${_common}-backend-service-app \
    --global \
    --network-endpoint-group=${_common}-serverless-neg-app \
    --network-endpoint-group-region=asia-northeast1
```

+ Cloud Functions 用の Backend Service に Cloud Functions 用の Serverless NEG を設定します。

```
gcloud beta compute backend-services add-backend ${_common}-backend-service-func \
    --global \
    --network-endpoint-group=${_common}-serverless-neg-func \
    --network-endpoint-group-region=asia-northeast1
```

+ Backend Service を確認します。

```
gcloud compute backend-services list
```
```
### Ex.

# gcloud compute backend-services list
NAME                                       BACKENDS                                                                        PROTOCOL
check-serverless-neg-backend-service-app   asia-northeast1/networkEndpointGroups/check-serverless-neg-serverless-neg-app   HTTP
check-serverless-neg-backend-service-func  asia-northeast1/networkEndpointGroups/check-serverless-neg-serverless-neg-func  HTTP
check-serverless-neg-backend-service-run   asia-northeast1/networkEndpointGroups/check-serverless-neg-serverless-neg-run   HTTP
```

## URL map の作成

+ Load Balancer で使用する URL map を作成します。
  + デフォルトは Cloud Run にマッピングするようにします。

```
gcloud compute url-maps create ${_common}-url-map \
    --default-service ${_common}-backend-service-run
```

+ URL map のデフォルト以外の設定をします。

```
gcloud compute url-maps add-path-matcher ${_common}-url-map \
    --path-matcher-name=${_common}-path-matcher \
    --path-rules "/app=check-serverless-neg-backend-service-app,/func=check-serverless-neg-backend-service-func" \
    --default-service=check-serverless-neg-backend-service-run
```

+ URL map を確認します。

```
gcloud compute url-maps list
```
```
### Ex.

# gcloud compute url-maps list
NAME                          DEFAULT_SERVICE
check-serverless-neg-url-map  backendServices/check-serverless-neg-backend-service-run
```

## マネージド SSL を設定する

+ www-ssl-cert という名前で Google マネージド SSL certificate リソースの作成をします。

```
export _my_domain=$(echo ${_common}.hejda.org)
```
```
gcloud compute ssl-certificates create ${_common}-www-ssl-cert \
    --domains ${_my_domain}
```

+ Certificate リソースの確認をします。

```
gcloud compute ssl-certificates list
```
```
### Ex.

# gcloud compute ssl-certificates list
NAME                               TYPE     CREATION_TIMESTAMP             EXPIRE_TIME  MANAGED_STATUS
check-serverless-neg-www-ssl-cert  MANAGED  2020-07-26T00:35:54.246-07:00               PROVISIONING
    check-serverless-neg.hejda.org: PROVISIONING
```

## Load Balancer で使用する Forwarding Rule の作成

+ リクエストをプロクシにルーティングする forwarding rule を作成します。

```
gcloud compute forwarding-rules create ${_common}-https-content-rule \
    --address=${_common}-example-ip \
    --target-https-proxy=${_common}-https-proxy \
    --global \
    --ports=443
```

+ Forwarding Rule の確認をします。

```
gcloud compute forwarding-rules list
```
```
### 例

# gcloud compute forwarding-rules list
NAME                                     REGION  IP_ADDRESS      IP_PROTOCOL  TARGET
check-serverless-neg-https-content-rule          34.107.216.140  TCP          check-serverless-neg-https-proxy
```

---> これで、目的の Serverless NEG を使った External HTTP(S) Load Balancer の作成が完了しました!!

## Web ブラウザで確認

ここまで作ってきたリソースを Web ブラウザで確認していきます。


Check the resources with a Web browser.

+ URL map on GCP console.

{{< figure src="/img/2020/07/try-serverless-neg_02.png" >}}

+ `/` は Cloud Run にマッピングされています。

{{< figure src="/img/2020/07/try-serverless-neg_03.png" >}}

+ `/run` は Cloud Run にマッピングされています。

{{< figure src="/img/2020/07/try-serverless-neg_04.png" >}}

+ `/app` は App Engine にマッピングされています。

{{< figure src="/img/2020/07/try-serverless-neg_05.png" >}}

+ `/func` は Cloud Functions にマッピングされています。

{{< figure src="/img/2020/07/try-serverless-neg_06.png" >}}

+ 上記のルールに該当しない場合は Cloud Run にマッピングされています。

{{< figure src="/img/2020/07/try-serverless-neg_07.png" >}}


# まとめ

これで Serverless NEG を使った External HTTP(S) Load Balancer の作成が出来ました!!

最後に [リソースの削除]() をします。

Have fun! :)
