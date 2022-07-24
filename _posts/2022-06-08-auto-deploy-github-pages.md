---
title: Github Action 自動部署 github-pages
date:   2022-06-28 08:00:00
categories: [CI/CD, Github Action]
tags: [CI/CD, Github Action]
---
Github pages 適合展示靜態頁面，只要在 github 建立 `gh-pages` 分支，可依據用戶名稱和repo名稱來開啟頁面。

```
https://[USER_NAME].github.io/[REPO_NAME]/
```

以這個專案為例
>[https://github.com/chenuin/JavaScript30](https://github.com/chenuin/JavaScript30)

路徑會是
>[https://chenuin.github.io/JavaScript30/](https://chenuin.github.io/JavaScript30/)

使用 JamesIves/github-pages-deploy-action@v4.3.3 有兩個必填參數：
- branch: 分支
- folder: 目錄，`.`代表整個repo根目錄

參數詳細說明請參考[這裡](https://github.com/marketplace/actions/deploy-to-github-pages)，在 repo 裡新增 github action ([連結](https://github.com/chenuin/JavaScript30/blob/master/.github/workflows/gh-pages.yml)):
```yml
name: Github pages

on:
  # Triggers the workflow on push events but only for the "master" branch
  push:
    branches: master

  # Allows you to run this workflow manually from the Actions tab
  workflow_dispatch:

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout 🛎️
        uses: actions/checkout@v3

      - name: Deploy 🚀
        uses: JamesIves/github-pages-deploy-action@v4.3.3
        with:
          branch: gh-pages
          folder: .
          token: ${{ secrets.GH_PAT }}
```

`GH_PAT` 設定方式，請先至 Settings => Developer settings => Personal access tokens (或下方連結)，按右上角新增Token，scopes 可直接勾選 repo 並將Token複製。
>[https://github.com/settings/tokens](https://github.com/settings/tokens)

再到專案的 Settings => Secrets => Actions (或下方連結)，按右上角新增，命名 `GH_PAT` 並將剛剛複製的Token貼上。

>https://github.com/`[USER_NAME]`/`[REPO_NAME]`/settings/secrets/actions

![Github secrets](/assets/posts/2022-06-08-github-secrets.png "Github secrets")

##### 相關文章
- [Github Actions 自動 Jekyll 專案部屬 Github Pages](https://chenuin.blogspot.com/2021/08/github-actions-jekyll-github-pages.html)
- [[Github] 在github.io建立免費的網站](https://chenuin.blogspot.com/2019/04/github-githubio.html)
