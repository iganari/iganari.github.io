# 構築時のめも

## 簡易手順

1. GitHub 上に `iganari.github.io` という Repository を作成する(Private)
1. ホストマシンに clone し、 develop ブランチを作成して push
1. まずはローカル開発をするために、 docker-compose をもちいて、 HUGO の環境を作る

## HUGO の始めの一歩

+ hugo の環境を用意する

```
割愛
```

+ まずは雛形を作成

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
