+++
author = "iganari"
categories = ["GCP", "Serverless"]
date = "2020-07-27"
description = ""
featuredalt = "Try Serverless NEG 00"
featuredpath = "date"
linktitle = ""
title = "Serverless NEG を試す ~Serverless NEG とは?~"
type = "post"
+++

# 構成

この記事の内容は長いため複数の記事に跨って作成しています。

適宜、興味のある記事を参照して下さい。

+ <本記事> [Serverless NEG とは?](https://iganari.github.io/blog/2020-07-27_try-serverless-neg-00/)
+ [Cloud RUN, App Engine, Cloud Functions の準備](https://iganari.github.io/blog/2020-07-27_try-serverless-neg-01/)
+ [サブドメインの設定](https://iganari.github.io/blog/2020-07-27_try-serverless-neg-02/)
+ [External HTTP(S) Load Balancer の作成](https://iganari.github.io/blog/2020-07-27_try-serverless-neg-03/)
+ [リソースの削除](https://iganari.github.io/blog/2020-07-27_try-serverless-neg-04/)

# やること

Serverless Network Endpoint Groups の概要を把握します

# Serverless NEG とは?

正式名称は Serverless Network Endpoint Groups です。

GCP には Network Endpoint Group (NEG) という機能があり、 [Zonal Network Endpoint Groups](https://cloud.google.com/load-balancing/docs/negs/zonal-neg-concepts?hl=en) と [Internet Network Endpoint Groups](https://cloud.google.com/load-balancing/docs/negs/internet-neg-concepts?hl=en) という 2 種類の NEG がありました。

2020/07/08 に GCP のサーバレスなコンポーネントに対応した NEG の Beta 版が出ました。

+ リリースノート
  + https://cloud.google.com/release-notes?hl=en#July_08_2020

現在、 Serverless NEG が対応しているのは Cloud Run, Cloud Functions, App Engine です。

2020/07/27 の時点では Pre-GA というステータスであり、GA までに機能やサポートの制限の変更がある可能性がある状態です。

+ Serverless network endpoint groups overview
  + https://cloud.google.com/load-balancing/docs/negs/serverless-neg-concepts?hl=en

次の図は、 HTTP(S) Load Balancing model に Serverless NEG がどのように収まるかを示しています。

![](https://cloud.google.com/load-balancing/images/lb-serverless-simple.svg?hl=en)

上記のように、Load Balancer の実際のバックエンドサービスとして、Cloud Run などを使用することが出来るようになります。

Cloud Run や App Engine などは、単独でも強力なスケーリング機能や高い拡張性があるサーバレスなコンポーネントですが今回のアップデートにより、そのコンポーネントを GCP の強力な Load Balancer のバックエンドに設定することが出来るようになり、構築出来るアプリケーションの拡張性がさらに高くなります。


上記の図では、Serverless NEG は `or` で表現されていますが、 URL map などを適宜設定することにより、 1 つの Load Balancer の配下に複数のサーバレスコンポーネントを設定することも出来ます。

今回は、 1 つの Load Balancer の配下に、Cloud Run, App Engine, Cloud Functions を 1 つずつ設定して、Web ブラウザで挙動を見てみようと思います :) 


# まとめ

これで Serverless NEG の概要を把握しました!!

次は [Cloud RUN, App Engine, Cloud Functions の準備](https://iganari.github.io/blog/2020-07-27_try-serverless-neg-01/) をやっていきます!

Have fun! :)
