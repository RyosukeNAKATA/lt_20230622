---
marp: true
theme: uncover
class:
    - lead
    - invert
---

# md→github pages
## GitHub Actionsを使ったAuto Deploy

23卒/chai 中田亮佑(RyosukeNAKATA)

2023/06/23 LT会資料

---

LT会やmtgなどのちょっとした発表

---

# スライド作りはめんどくさい

---

[Marpリンク](https://marp.app/)([marp-cli/GitHub](https://github.com/marp-team/marp-cli))

![height:300](https://github.com/marp-team/marp/blob/main/marp-dark.png?raw=true)

---

![height:100](https://github.com/marp-team/marp/blob/main/marp-dark.png?raw=true)

- mdファイルからpdf/htmlに出力
- GitHub ActionsによるAuto Deployが可能 :arrow_left: これ大事

---


事前にGitHubリポジトリを用意&cloneしておく

1. グローバルにインストールする場合
(nodeをbrew経由で入れさせられる:rage:)

```
❯ brew install marp-cli
```
2. ローカルにインストール(自分はこっち)

```bash
$ project_home/
❯ npm install --save-dev @marp-team/marp-cli
...
```

---

ディレクトリ構成(mainブランチ)
```
.
├──.github
│  └── workflows
│     └── deploy.yml 🆕
├── package.json
├── README.md
├── .marprc.yml  🆕
└── src
   ├── index.html  🆕
   └── slides  🆕
      └── how_to_deploy.md  🆕
```


---

ディレクトリ構成(mainブランチ)
```
.
├──.github
│  └── workflows
│     └── deploy.yml # デプロイのアクションを設定
├── package.json # デプロイ時に起動するnpmコマンドを記載
├── README.md
├── .marprc.yml # marpの設定ファイル
└── src
   ├── index.html # スライドのリンクを書いておくと便利
   └── slides # この配下のmdファイルがスライドになる
      └── how_to_deploy.md
```

---

`.marprc.yml`

```
lang: ja-JP
inputDir: "./src/slides/"
output: "./public/slides/"
```
---

`package.json`

```
{
+  "scripts": {
+    "dev": "marp -s",
+    "build": "marp && cp src/index.html public"
+  },
  "devDependencies": {
    "@marp-team/marp-cli": "^3.0.2"
  }
}
```

---

`.github/workflows/deploy.yml`

```
name: Publish GitHub Pages
on:
  push:
    branches:
      - main

jobs:
  publish:
    runs-on: ubuntu-latest
    timeout-minutes: 5
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
        with:
          node-version: 14
      - run: npm install
      - run: npm run build
      - uses: peaceiris/actions-gh-pages@v3
        with:
          deploy_key: ${{ secrets.ACTIONS_DEPLOY_KEY }}
          publish_dir: ./public
```

---

```
$ ~/.ssh/
❯ ssh-keygen -t rsa -b 4096 -C "$(git config user.email)" -f gh-pages -N ""
```

---

公開鍵

Settings→Deploy Keys→Add deploy key→適当な名前を付けて保存(Allow write accessにチェックをする)

秘密鍵

Settings→Secrets & Variables → Actions → New Repository secret → `ACTIONS_DEPLOY_KEY` という名前で値を保存

---

# github pagesの設定

settigns→pages→gh-pagesブランチのrootでデプロイするようにする

---


# あとはでmdファイルを
# 書いて全部push!
