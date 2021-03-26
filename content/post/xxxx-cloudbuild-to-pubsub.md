# Pub/Sub から Cloud Build を実行してみる






https://cloud.google.com/build/docs/automating-builds/create-pubsub-triggers

つくる構成

GCS に何かファイルを置く -> Pub/Sub 経由で Cloud Build が作動
どんな作業をしたのかSlackに通知


PubSub から渡されたデータとしてどのようなものがあるのかをすべて出したらいいんじゃないかな


Container regstry ->　pubsub->  Clouod Build 
Cloud Storage のイベント -> pubsub->  Cloud Build 


まずは package gcp のほうで上記の2種類をやる