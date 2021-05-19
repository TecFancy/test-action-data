---
title: 基于 Docker 的 sonarqube 安装与配置
categories: 开发工具
tags: sonarqube
---

本文介绍了如何使用 Docker 镜像来搭建、配置 sonarqube，在开始之前你需要：

- 一台服务器
- 一个域名

首先，确保你的服务器中已经安装了 Docker，了解如何[安装 Docker](https://docs.docker.com/get-docker/)。
安装好 Docker 之后，拉取 sonarqube 的社区镜像：

``` bash
$ sudo docker pull sonarqube:8.0-community
```
