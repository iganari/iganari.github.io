+++
author = "iganari"
categories = ["gcp", "hogehoge", "fugafuga"]
date = "2020-xx-xx"
description = ""
featuredalt = "vnc-raspberrypi-from-macos ja"
featuredpath = "date"
linktitle = ""
title = "GCP で自分のリソース階層を作ってみる #1 GCP のリソース階層を理解する"
type = "post"
+++

# 注意点

:warning: この記事は 2020 年 7 月の情報を元にしています。

# GCP のリソース階層を理解する

# サマリ

+ GCP にはリソース階層 (Resource Hierarchy) というものがある
+ ユーザとリソース階層の理解

# Resource hierarchy

https://cloud.google.com/resource-manager/docs/cloud-platform-resource-hierarchy?hl=en

![](https://cloud.google.com/resource-manager/img/cloud-folders-hierarchy.png)

+ 私達が通常使用する GCP リソースは GCP プロジェクトに紐付いている
+ GCP プロジェクト自体はその中で完結してアプリを動かすことが出来る
  + GCP プロジェクトを跨いでアプリを動かすことが出来る
  + GCP においてはプロジェクトは要件ごとに割とサクサク作って運用することが望ましい(たぶん)
+ GCP プロジェクトを管理する機構が存在する
  + 組織(とフォルダ)である
  + 複数の GCP プロジェクトを意味のある単位でまとめることで、より運用に適した管理が出来るようになる
















