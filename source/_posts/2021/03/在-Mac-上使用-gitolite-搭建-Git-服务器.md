---
title: 在 Mac 上使用 gitolite 搭建 Git 服务器
date: 2021-03-26 18:21:48
categories: Git
tags: gitolite
---

## 目标

- 使用 gitolite 搭建本地 git 服务
- 通过 iCloud 同步仓库

## 准备 ssh key

使用 `ssh-keygen` 命令生成 ssh key：

<!-- more -->

```bash
$ ssh-keygen -t rsa -b 4096
Generating public/private rsa key pair.
Enter file in which to save the key (/Users/<username>/.ssh/id_rsa):
Created directory '/Users/ruofei/.ssh'.
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /Users/<username>/.ssh/id_rsa.
Your public key has been saved in /Users/<username>/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:Q80nJRy3c+QEW8Sr1...j63sm3mXw <username>@mbp.local
The key's randomart image is:
+---[RSA 4096]----+
|     ..o .o.+=B+.|
|     .. oo.+ B+ o|
|.    ..  ..      |
+----[SHA256]-----+
```

默认会在 `/Users/<username>/.ssh/` 目录下随机生成 ssh key，建议输入一个密码。

## 允许远程登录 MacBook

打开 `系统偏好设置` -> `共享`，勾选 `远程登录`：

![远程登录](https://gitee.com/smpower/oss/raw/master/hi-ruofei.com/knM4zu.png)

## 使用 iCloud 同步裸仓库

因 gitolite 服务器部署在本地，所以当我们重装系统时，一般会将 gitolite 服务的裸仓库都做一次备份。方便起见，这里使用 iCloud 同步我们的裸仓库。

在 iCloud 中创建一个名为 `repositories` 的文件夹，打开终端，cd 到 iCloud 根目录下：

```bash
$ /Users/<username>/Library/Mobile\ Documents/com~apple~CloudDocs/
```

创建 `repositories` 目录的软连接到当前登录用户的家目录下的 `repositories` 目录：

```bash
$ sudo ln -s /Users/<username>/Library/Mobile\ Documents/com\~apple\~CloudDocs/repositories/ /Users/git/repositories
```

下面测试一下 iCloud 的同步功能是否正常。cd 到 `/Users/git/repositories` 目录下，创建一个名为 `test` 的测试文件：

```bash
$ cd /Users/<username>/repositories && touch test
```

接下来，打开 iCloud 中的 `repositories` 文件夹，如果能看到我们刚才创建的这个 `test` 文件，就说明 iCloud 的同步功能是正常的。

{% tabs 测试 iCloud 同步功能是否正常, 0 %}

<!-- tab /Users/git/repositories/ 目录下的内容 -->

![](https://gitee.com/smpower/oss/raw/master/hi-ruofei.com/fozuqm.png)

<!-- endtab -->

<!-- tab iCloud 中 repositories 目录下的内容 -->

![](https://gitee.com/smpower/oss/raw/master/hi-ruofei.com/8gIbvw.jpg)

<!-- endtab -->

{% endtabs %}

## 安装、配置 gitolite

首先，克隆 gitolite 的源码：

```bash
$ git clone https://github.com/sitaramc/gitolite
# 如果克隆 github 上的仓库失败，可以通过国内镜像仓库克隆，速度快很多
# git clone https://gitee.com/mirrors/gitolite.git
```

克隆源码后建立 gitolite 软连接：

```bash
$ mkdir -p ~/bin
$ gitolite/install -ln /Users/<username>/bin # 需使用绝对路径
```

再将 `gitolite` 追加到 `.bash_profile` 中：

```bash
export PATH=/Users/<username>/bin:$PATH # 如果用 zsh 那就将这句话添加到 .bashrc 文件，注意将 <username> 替换成你的用户名
```

最后设置管理员用户共钥：

```bash
$ gitolite setup -pk YourName.pub # 这里的 YourName.pub 就是之前生成的共钥（ssh key），通常叫做 id_rsa.pub
```

上面的命令执行成功之后，会在 iCloud 的 `repositories` 目录下创建两个仓库：

- `gitolite-admin.git`：这是管理员仓库，添加人员、权限等操作需要将这个仓库克隆下来在其冲的 config 中配置。
- `testing.git`：这个是一个测试仓库。

![初始化的两个仓库](https://gitee.com/smpower/oss/raw/master/hi-ruofei.com/2xkXB3.png)

之后我们提交的代码都会同步到 iCloud。

因为我们是在本地搭建的 gitolite，所以还要在当前管理员用户的 `.ssh` 目录下创建一个配置文件 config：

```bash
$ vim ~/.ssh/config
```

config 文件中填写：

```bash
Host local # local 就表示本机（127.0.0.1）
    HostName 127.0.0.1
    IdentityFile ~/.ssh/id_rsa
```

## 示例

将 `gitolite-admin.git` 仓库克隆下来：

```bash
git clone <username>@local:gitolite-admin.git # <username> 是你的用户名
```

![克隆 gitolite-adming.git 仓库](https://gitee.com/smpower/oss/raw/master/hi-ruofei.com/fHnR56.png)

用编辑器打开 `gitolite-admin` 仓库，修改其中的 `gitolite.conf` 文件：

![修改 gitolite.conf 文件](https://gitee.com/smpower/oss/raw/master/hi-ruofei.com/ZEeaGy.png)

图中新加的名为 `hi-ruofei.com` 仓库名就是本站的源码仓库。

修改之后，提交到 gitolite，gitolite 会自动帮我们创建一个名为 `hi-ruofei.com` 的裸仓库，该仓库会同步到 iCloud 上。

![自动创建 hi-ruofei.com 裸仓库](https://gitee.com/smpower/oss/raw/master/hi-ruofei.com/x51QfE.png)

![保存在 iCloud 上的裸仓库](https://gitee.com/smpower/oss/raw/master/hi-ruofei.com/vY8mFa.png)

## 参考

- [Gitolite](https://gitolite.com/)
- [45.在 Mac 上使用 gitolite 搭建 Git 服务器](https://blog.csdn.net/a464057216/article/details/52644021)
