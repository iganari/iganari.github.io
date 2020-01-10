# 構築時のめも

## 簡易手順

1. GitHub 上に `iganari.github.io` という Repository を作成する(Private)
1. ホストマシンに clone し、 develop ブランチを作成して push
1. まずはローカル開発をするために、 docker-compose をもちいて、 HUGO の環境を作る

## HUGO の始めの一歩

+ hugo の環境を用意する ---> :whale:

```
docker run --rm -it \
  -v $(pwd):/usr/local/iganari \
  -w /usr/local/iganari \
  -p "1313:1313" \
  jguyomard/hugo-builder \
  /bin/ash
```

+ :whale: まずは雛形を作成

```
export my_site_dir='iganari-github-io'

hugo new site ${my_site_dir}
```
```
### 例


# export my_site_dir='iganari-github-io'
# hugo new site ${my_site_dir}
Congratulations! Your new Hugo site is created in /usr/local/iganari/iganari-github-io.

Just a few more steps and you're ready to go:

1. Download a theme into the same-named folder.
   Choose a theme from https://themes.gohugo.io/, or
   create your own with the "hugo new theme <THEMENAME>" command.
2. Perhaps you want to add some content. You can add single files
   with "hugo new <SECTIONNAME>/<FILENAME>.<FORMAT>".
3. Start the built-in live server via "hugo server".

Visit https://gohugo.io/ for quickstart guide and full documentation.
#
```
```
### 例


# tree
.
├── README.md
├── docker-compose.yml
└── iganari-github-io
    ├── archetypes
    │   └── default.md
    ├── config.toml
    ├── content
    ├── data
    ├── layouts
    ├── static
    └── themes

7 directories, 4 files
# 
```

+ :whale: hugo コマンドに内包されている server コマンドを使う

```
cd ${my_site_dir}
hugo server --bind 0.0.0.0
```

+ ブラウザから確認する
  + この時点ではブランクのページが表示されるのみ
  + http://127.0.0.1:1313

+ テーマをインポートする
  + 今回は [Hugo Future Imperfect](https://themes.gohugo.io/future-imperfect/) を使用する
  + [Even](https://themes.gohugo.io/hugo-theme-even/) も捨てがたい…

```
mkdir themes
git clone https://github.com/jpescador/hugo-future-imperfect.git themes/hugo-future-imperfect
```

+ :whale: HUGO の設定ファイルにテーマを記述

```
echo 'theme = "hugo-future-imperfect"' >> config.toml
```

+ ブラウザから確認する
  + なにか表示が変われば、無事にロード出来ている
  + http://127.0.0.1:1313

+ :whale: 後は自分でコンテンツを作っていく
  + 本家の GitHub Repository
    + https://github.com/pacollins/hugo-future-imperfect-slim
  + サンプル
    + https://github.com/pacollins/hugo-future-imperfect-slim/blob/master/exampleSite/config.toml

## Docker Compose で動くようにする :whale::whale:

+ 現状のリソースを確認する
  + `iganari-github-io` ディレクトリの下に HUGO のソースがあるので、そこをワークディレクトリにしつつ、 `hugo server` を起動するようにする

```
$ tree -L 2
.
├── README.md
├── docker-compose.yml
├── iganari-github-io
│   ├── archetypes
│   ├── config.toml
│   ├── content
│   ├── data
│   ├── layouts
│   ├── resources
│   ├── static
│   └── themes
└── init-memo.md

8 directories, 4 files
```

+ docker-compose.yml は下記のよう

```
version: '3.3'

services:
  web:
    image: jguyomard/hugo-builder
    container_name: hugo-container
    ports:
      - "1313:1313"
    volumes:
      - ./:/usr/local/iganari/iganari.github.io
    working_dir: /usr/local/iganari/iganari.github.io/iganari-github-io
    # command: /bin/sh  # for Debug
    # tty: true         # for Debug
    command: hugo server --bind 0.0.0.0
```

+ 実行してみる

```
docker-compose up -d
```
```
### 例

$ docker-compose ps
     Name                 Command             State           Ports         
----------------------------------------------------------------------------
hugo-container   hugo server --bind 0.0.0.0   Up      0.0.0.0:1313->1313/tcp
```

+ ブラウザから確認する
  + 意図した内容がブラウザで確認出来れば OK
  + http://127.0.0.1:1313

+ Docker Compose のコマンドめも

```
### コンテナのイメージだけ作る
docker-compose build


### コンテナのイメージ作りつつ、(プロセスとして)起動する
docker-compose up


### コンテナのイメージ作りつつ、(デーモンとして)起動する
docker-compose up -d


### プロセスを見る
docker-compose ps


### 起動しているのを停止
docker-compose stop


### サービス(?)の削除(<- うまい言葉が出てこない)
docker-compose rm
```

## GitHub Actions を設定する

### YAML

よしなに

### デプロイのための鍵を設定する

+ GitHub Actions にて、 Hugo を Build し、かつコミットもしないといけないので、 Repository に対して、権限が無いといけない
+ 従って、GitHub Actions 内で使用する secret を用意する

### 鍵の作り方

https://github.com/peaceiris/actions-gh-pages#getting-started

```
ssh-keygen -t rsa -b 4096 -C "$(git config user.email)" -f gh-pages -N ""
```
