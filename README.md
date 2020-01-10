# iganari.github.io

## ローカルの開発方法

### とりあえず、動かしたい

+ Docker コンテナの起動

```
docker run --rm -it \
  -v $(pwd):/usr/local/iganari \
  -w /usr/local/iganari \
  -p "1313:1313" \
  jguyomard/hugo-builder \
  /bin/ash
```

+ :whale: hugo

```
hugo help
```

### Docker Compose を使って起動する

+ Repo をクローンする

```
git clone hoge
cd iganari.github.io
```

+ テーマをクローンする

```
git clone https://github.com/jpescador/hugo-future-imperfect.git themes/hugo-future-imperfect
```

+ Docker Compose の起動

```
docker-compose up -d
```

+ 確認

http://127.0.0.1:1313
