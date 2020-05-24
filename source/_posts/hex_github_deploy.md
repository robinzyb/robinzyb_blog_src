----
title: Hexo Blog Deploy Memoir
authors: Yongbin Zhuang
date: 2020-05-15 22:47:19
----
# 在Mac使用hexo与github建立博客

这是我的第一个博客，感谢github提供免费的webserver。[hexo](https://hexo.io)是一个开源的blog框架。可以根据markdown文档生成相应的静态网页。

在所有安装之前先下载Mac常用的包管理器Homebrew

## 安装Hexo

[官方安装指南](https://hexo.io/docs/)

需先安装git，和node.js。

```bash
brew install git
brew install node.js
```

两者安装完成后，安装hexo

```bash
npm install -g hexo-cli
```

完毕

## 设置GitHub Page

[官方GitHub Pages建立指南](https://pages.github.com)

只要注意一点：一开始让username.github.io的仓库是空的，到时候所有东西我们都从本地push上去。

## 本地Hexo的设置

首先找个空的文件夹，命名为username.github.io，这个文件夹是之后我们需要跟github仓库连接起来的。

```bash
cd username.github.io
hexo init
```

## 将本地的Hexo Push到Github

```bash
#当前文件夹初始化仓库
git init
#添加github repository
git remote add <你的github repository url>
#把本地文件都传送到github
git push -u origin master
```

## Hexo博客：Source和Page分离

以上只是把一堆文件简单的push到github，hexo还没自动生成网页。接下来为了保持仓库简洁我会专门分出一条branch: hexo-source，然后把master branch留给生成的网页。因为最新的github只能从master branch建立网页，因此必须这么做。

首先本地建立hexo-source branch，并清空master

```bash
git checkout -b hexo-source
#把这个branch 同步到github repository
git push -u origin hexo-source
#接着把master branch 清空
git checkout master
git rm ./\*
git commit
git push
#如果push成功，你会看到主页上的master branch内容是空的，多了一个hexo-source
```

## 使用Travis部署网页

本来是在本地用命令`hexo generate`生成网页然后push到github，现在可以用travis在远程完成这个动作。本地只需要修改一个travis设置文件让travis知道怎么去部署就可以了。

首先加入[travis](https://github.com/marketplace/travis-ci)到github账号，然后在travis中给自己github账号的username.github.io仓库权限。

然后在github中生成一个可以进入该仓库的repo scopes的[token](https://github.com/settings/tokens)，复制该token，放入travis中的**Environment Variables**，名字为GH_TOKEN, token放入token栏中。保存他。

在本地，hexo-source branch中加入`.travis.yml`文件，内容为

```yml
sudo: false
language: node_js
node_js:
  - 10 # use nodejs v10 LTS
cache: npm
branches:
  only:
    - hexo-source # build master branch only
script:
  - hexo generate # generate static files
deploy:
  provider: pages
  skip-cleanup: true
  github-token: $GH_TOKEN
  keep-history: true
  target_branch: master #最后会部署到master branch
  on:
    branch: hexo-source
  local-dir: public
```

还有就是hexo自己的配置文件`_config.yml`，记得更改`url`为`https://username.github.io`, username是自己的用户名。

```bash
git add _config.yml .travis.yml
git commit
git push
```

push成功后等个几分钟travis就会给你Pull Request一个生成好的网页文件。

在检查一下你的https://username.github.io

这样博客就部署好了





