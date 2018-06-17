---
title: Hexo+Next搭建个人博客（一）
date: 2018-06-17 13:43:40
tags:
---

### 简介

Hexo是一个快速、简洁且高效的静态站点生成框架，它基于Node.js。它有以下特点：

- **超快速度**

  Node.js所带来的超快生产速度，让上百个页面在几秒内瞬间完成渲染。

- **支持Markdown**

  Hexo支持GitHub Flavored Markdown的所有功能，甚至可以整合Octopress的大多数插件。

- **一键部署**

  只需要一条指令即可部署到GitHub Pages，或者其他网站。

- **丰富的插件**

  Hexo拥有强大的插件系统，安装插件可以让Hexo支持Jade，CoffeeScript。

基于Hexo这个优秀的博客框架，很多很多优秀的开发者奉献出了它们基于 Hexo 开发的主题。

NexT 因其 精于心，简于形 的风格，一直被广大用户所喜爱。

### 安装前提

安装Hexo相当简单。然而在安装前，您必须检查电脑中是否安装了以下应用程序：

Node.js

Git

如果您的电脑中已安装上述必备程序，接下来只需要使用npm即可完成Hexo的安装。

``` shell
  $ npm install -g hexo-cli
```


### 安装Git

根据你的主机系统，选择下列命令：

Linux (Ubuntu, Debian)：sudo apt-get install git-core

Linux (Fedora, Red Hat, CentOS)：sudo yum install git-core

### 安装nvm

安装Node.js的最佳方式是使用nvm。

``` shell
  $ wget -qO- https://raw.githubusercontent.com/creationix/nvm/v0.32.1/install.sh | bash
```




