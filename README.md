# iganari.github.io

![](https://github.com/iganari/iganari.github.io/workflows/Publish_GitHub_Pages/badge.svg)

## Web

+ Escape from
  + https://iganari.github.io/

## 説明

+ 個人ブログのソースコードです。
+ Hugo とそのテンプレートを使って構築しています。
+ ホスティングは GitHub Pages にて行っています。

## デプロイ方法

1. `release` branch に対して、PR を作りマージする(推奨は `develop` branch から、 `release` branch に PR を作成する)
1. GitHub Actions を通じて、`release` branch から、 `master` branch に自動マージが実行される
1. `master` branch = https://iganari.github.io/ となる

## ローカルの開発方法

### Docker Compose を使って起動する

+ Repository をクローンします。

```
git clone hoge
cd iganari.github.io
```

+ Hugo のテーマをクローンします。

```
git clone https://github.com/jpescador/hugo-future-imperfect.git themes/hugo-future-imperfect
```

+ Docker Compose を起動します

```
docker-compose up -d
```

+ Web ブラウザから確認します。

http://127.0.0.1:1313


### ドラブルシューティング

+ docker-compose でコンテナが起動しない or すぐに停止する場合
  + dockder-compose.yml を編集し、コンテナに入ってエラー文を確認しましょう。

```
### docker-compose.yml の修正
vim docker-compose.yml

---------------------------------------
# command: /bin/sh  # for Debug                <--- この行のコメントアウトを取る
# tty: true         # for Debug                <--- この行のコメントアウトを取る
command: hugo server --bind 0.0.0.0            <--- この行をコメントアウトする
---------------------------------------
↓
---------------------------------------
command: /bin/sh  # for Debug
tty: true         # for Debug
# command: hugo server --bind 0.0.0.0
---------------------------------------



### Docker Compose の起動
docker-compose up -d



### コンテナに入ってみる
docker exec -it $(docker ps | grep hugo-container | awk '{print $1}') /bin/sh
```


