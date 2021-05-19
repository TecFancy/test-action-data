---
title: 用 Github Actions 自动部署 Hexo 博客
tags:
  - Hexo
categories:
  - 博客
date: 2020-11-23 22:18:24
---

## 背景

在配置 Hexo 和写博客的过程中，每次发布博客都要提交变更、hexo clean 和 hexo depoly，步骤实在是太繁琐。有没有一种方式可以实现自动化地部署博客呢？答案是有的。当前市面上有关自动化部署的工具确实不少，像常用的 jinkens、travis、circleci 和 GitHub Actions 等。

这里我就用 GitHub Actions 吧，毕竟博客是托管在 GitHub 上的，一套流程走下来也是方便的很。

<!-- more -->

## Hexo

首先我们需要确保在本地已经安装了 hexo-cli，安装方式参见 [官网](https://hexo.io/zh-cn/docs/)。

其次，我们要在 GitHub 上部署 pages，参考 [这里](https://pages.github.com/)。

打开 Hexo 的配置文件 `_config.yml`，确保配置文件中有以下配置：

```yml
deploy:
  type: git
  repository: git@github.com:smpower/smpower.github.io.git
  branch: master
```

这里解释一下，上面配置的意思是使用 `hexo-deployer-git` 插件将 Hexo 生成的静态网站资源提交到 `repository` 仓库的 `master` 分支上。当我们访问博客的时候，实际上就是访问的这个分支的静态资源。

{% note info %}
注意：请将 `repository` 改为你自己的仓库地址，并且将 `branch` 修改为你的 gh-pages 分支。
{% endnote %}

## 生成密钥

这里我们生成的密钥是用在仓库中的。

```bash
ssh-keygen -t rsa -b 4096 -C "Hexo Deploy Key" -f github-deploy-key-hi-ruofei-com -N ""
```

这会在当前目录下生成两个文件：

- github-deploy-key-hi-ruofei-com —— 私钥
- github-deploy-key-hi-ruofei-com.pub —— 公钥

## Github 配置密钥

我们把 `私钥` 放在我们存放 Hexo 原始文件代码的仓库里面，用来触发 Actions 使用。

把 `公钥` 放在 GitHub pages 对应的仓库里面，用于 Hexo 部署时的写入操作。

### 配置私钥

首先在 GitHub 上打开保存 Hexo 原始代码的仓库，访问 `Settings -> Secrets`，页面如下：

![](https://gitee.com/smpower/oss/raw/master/hi-ruofei.com/m3xXzS.png)

然后点击 `New repository secret`:

![](https://gitee.com/smpower/oss/raw/master/hi-ruofei.com/XdMDG7.png)

`Name` 处填写 `HEXO_DEPLOY_KEY_HI_RUOFEI_COM`，注意大小写，这个后面的 GitHub Actions Workflow 要用到，一定不能写错。

在 `Value` 处填写 `github-deploy-key-hi-ruofei-com` 中的内容：

![](https://gitee.com/smpower/oss/raw/master/hi-ruofei.com/pVy6sf.png)

添加了私钥以后的页面如下：

![](https://gitee.com/smpower/oss/raw/master/hi-ruofei.com/fayLTw.png)

### 配置公钥

接下来我们需要访问存放网页的仓库，也就是 Hexo 部署以后的仓库，比如：yourname.github.io 这种，访问 Settings -> Deploy keys：

![](https://gitee.com/smpower/oss/raw/master/hi-ruofei.com/j4pxPH.png)

点击 `Add deploy key` 按钮来添加一个新的公钥：

![](https://gitee.com/smpower/oss/raw/master/hi-ruofei.com/3KaPrB.png)

在 Title 中输入：HEXO_DEPLOY_PUB_HI_RUOFEI_COM 字样，当然也可以填写其它自定义的名字。

在 Key 中粘贴 github-deploy-key-hi-ruofei-com.pub 文件的内容。

{% note info %}
注意：一定要勾选 Allow write access 来打开写权限，否则无法写入会导致部署失败。
{% endnote %}

![](https://gitee.com/smpower/oss/raw/master/hi-ruofei.com/JHvTlw.png)

最后添加好了公钥的界面如下：

![](https://gitee.com/smpower/oss/raw/master/hi-ruofei.com/asgIIE.png)

## 创建 Workflow

首先在 Hexo 的仓库中创建一个新文件：.github/workflows/deploy.yml，文件名可以自己取，但是一定要放在 .github/workflows 目录中，文件的内容如下：

```yml
name: Hexo Deploy

on:
  push:
    branches:
      - master

jobs:
  build:
    runs-on: ubuntu-18.04
    if: github.event.repository.owner.id == github.event.sender.id

    steps:
      - name: Checkout source
        uses: actions/checkout@v2
        with:
          ref: hexo # 这是你的 Hexo 源文件所在的分支

      - name: Setup Node.js
        uses: actions/setup-node@v1
        with:
          node-version: "14.x"

      - name: Setup Hexo
        env:
          ACTION_DEPLOY_KEY: ${{ secrets.HEXO_DEPLOY_KEY_HI_RUOFEI_COM }}
        run: |
          mkdir -p ~/.ssh/
          echo "$ACTION_DEPLOY_KEY" > ~/.ssh/id_rsa
          chmod 700 ~/.ssh
          chmod 600 ~/.ssh/id_rsa
          ssh-keyscan github.com >> ~/.ssh/known_hosts
          git config --global user.email "contact@mail.ruofei.site"
          git config --global user.name "ruofei"
          npm install hexo-cli yarn -g
          yarn

      - name: Deploy
        run: |
          hexo clean
          hexo deploy
```

简单解释一下，当我们推送内容到远程 hexo 分支的时候，就会触发这个 Workflow。

使用 Ubuntu 18.04 作为 hexo deploy 的系统。

首先 checkout 源代码(在 hexo 分支)，然后设置使用最新的 Node.js v14.x LTS 作为 node 解释器。

接下来就是创建 SSH 相关的配置文件，注意 secrets.HEXO_DEPLOY_KEY_HI_RUOFEI_COM 就是对应我们之前设置的私钥，所以名字一定不要搞错。

git config 相关的名字和邮件地址替换成大家自己使用的就好了。

最后就是安装 Hexo CLI，各个依赖模块和部署了。

## 验证

下面就是 GitHub Actions 页面显示的运行结果：

![](https://gitee.com/smpower/oss/raw/master/hi-ruofei.com/bWdSrU.png)

前面有绿色钩钩的，就表示部署成功，红色叉叉的表示失败。如果部署失败，还会收到 GitHub 的邮件提醒。

好了，以上就是利用 GitHub Actions 自动部署 Hexo 到 GitHub Pages 的方法，有疑问的请在下方留言哦 😊

（你看到这篇日志的时候我可能还没配置留言功能呢，或者因为审核暂时将留言功能关闭了，这个时候你就可以给我发邮件啦 ～）
