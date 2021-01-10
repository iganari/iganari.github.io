+++
title = "Serverless NEG を試す ~サブドメインの設定~"
date = "2020-07-27T02:00:00Z"
tag = [
    "GCP",
    "Serverless"
]
draft = false
toc = true
backtotop = true
disable_comment = true
+++

2020/07 時点での Serverless NEG のハンズオン資料です。

複数の記事に跨って作成しており、3番目の記事になります。

<!--more-->
---


# 構成

この記事の内容は長いため複数の記事に跨って作成しています。

適宜、興味のある記事を参照して下さい。

+ [Serverless NEG とは?](https://iganari.github.io/blog/2020-07-27_try-serverless-neg-00/)
+ [Cloud RUN, App Engine, Cloud Functions の準備](https://iganari.github.io/blog/2020-07-27_try-serverless-neg-01/)
+ <本記事> [サブドメインの設定](https://iganari.github.io/blog/2020-07-27_try-serverless-neg-02/)
+ [External HTTP(S) Load Balancer の作成](https://iganari.github.io/blog/2020-07-27_try-serverless-neg-03/)
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

+ External HTTP(S) Load Balancer で使用するサブドメインの準備をします。

# 静的 IP アドレスの準備

+ 静的 IP アドレスを確保します。

```
gcloud compute addresses create ${_common}-example-ip \
    --ip-version=IPV4 \
    --global
```

+ 確保した静的 IP アドレスを確認します。

```
gcloud compute addresses describe ${_common}-example-ip \
    --format="get(address)" \
    --global
```
```
### 例

# gcloud compute addresses describe ${_common}-example-ip \
>     --format="get(address)" \
>     --global
34.107.216.140
```

# Prepare Sub Domain

+ 確保した静的 IP アドレスを、自分で用意したサブドメインの A レコードとして設定します。

{{< figure src="/images/2020/07/try-serverless-neg_01.png" >}}


# まとめ

これで External HTTP(S) Load Balancer で使用するサブドメインの準備が出来ました!!

次は [External HTTP(S) Load Balancer の作成](https://iganari.github.io/blog/2020-07-27_try-serverless-neg-03/) をやっていきます!

Have fun! :)
