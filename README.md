# iganari.github.io

![](https://github.com/iganari/iganari.github.io/workflows/Publish_GitHub_Pages/badge.svg)

## Web

+ Escape from
  + https://iganari.github.io/

## 説明

個人ブログのソースコードです。

Hugo とそのテンプレートを使って構築しています。

ホスティングは GitHub Pages にて行っています。


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

command: /bin/sh  # for Debug                  <--- この行のコメントアウトを取る
tty: true         # for Debug                  <--- この行のコメントアウトを取る
# command: hugo server --bind 0.0.0.0          <--- この行をコメントアウトする



### Docker Compose の起動
docker-compose up -d



### コンテナに入ってみる
docker exec -it ${your_container_name} /bin/sh
```
