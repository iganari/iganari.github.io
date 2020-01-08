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




### ブラウザで確認したい場合

+ Docker Compose の起動

```
docker-compose up -d
```

+ 確認

http://127.0.0.1:1313