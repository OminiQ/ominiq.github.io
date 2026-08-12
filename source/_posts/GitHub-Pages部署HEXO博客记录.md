---
title: GitHub Pages 部署 Hexo 博客记录
tags: Hexo
abbrlink: 153d1a8b
date: 2025-02-08 03:52:12
---

新年开工，心血来潮部署一个博客来记录生活或者工作的片段。以前部署过 [notion-next](https://github.com/tangly1024/NotionNext) ，但是由于页面太杂乱配置复杂没有坚持下去，直到我看到了 [Hexo-theme-keep](https://github.com/theme-keep/hexo-theme-keep-starter)，简单漂亮

### 本地环境安装
整个安装步骤分两个部分，如果部署在云服务器第一部分就已经可以完成博客的部署，接下来只要开放端口或者代理，配置域名别人就能访问你的博客

Hexo 是一个基于 Node.js 的静态博客框架，所以需要先安装 Node.js 然后安装博客本体和主题

#### 安装 Node.js
因为本地环境为 Linux，我直接使用的是 apt 包管理器安装，如果要使用新版本的话可以使用 [NVM (Node Version Manager)](https://nodejs.org/zh-cn/download)
``` bash
$ sudo apt update
$ sudo apt install nodejs npm
```

安装完后检查 Node.js 版本
``` bash
$ node -v
$ npm -v
```
#### 安装 Hexo
``` bash
$ npm install hexo-cli -g
$ hexo init blogfile
$ cd blogfile
$ npm install
$ hexo server
```
由于使用 apt 管理器安装 node 遇到权限不足导致无法在系统目录 /usr/local/lib/node_modules 中安装全局 npm 包。以下是解决方案：

``` bash
$ mkdir ~/.npm-global #创建用户级全局安装目录
$ npm config set prefix '~/.npm-global' #配置 npm 使用此目录
$ echo 'export PATH=~/.npm-global/bin:$PATH' >> ~/.bashrc #将以下内容添加到 ~/.bashrc
$ source ~/.bashrc #应用配置
$ npm install hexo-cli -g #重新安装
```
#### 安装 Keep 主题

``` bash
$ cd blogfile
$ npm install hexo-theme-keep
```
Keep 主题安装完成后，在 Hexo 配置文件 _config.yml 中将 theme 设置为 keep。
因为使用 NPM 安装 Keep 主题，那么需要把 node_modules/hexo-theme-keep/_config.yml 路径下的主题配置文件复制到 your-hexo/source/_data/keep.yml

``` bash
$ mkdir -p source/_data
$ cp node_modules/hexo-theme-keep/_config.yml source/_data/keep.yml
```
### 部署 GitHub Pages
由于没有云服务器，我打算将博客部署在 GitHub Pages，大致思路为将本地的 Hexo 博客生成静态内容，将生成的网页推送的 GitHub 仓库，以后写了博客重新生成一遍再推送就行
#### 创建 GitHub 仓库
注意仓库名称为 username.github.io  “username”用 GitHub 账户名替换

#### 安装配置 git
``` bash
$ sudo apt update
$ sudo apt install git
$ git --version
```
你需要配置 Git 的用户名和邮箱地址，通常使用与你的 GitHub 账户一致的信息：
``` bash
$ git config --global user.name "Your Name"
$ git config --global user.email "your.email@example.com"
```
为了安全地连接 GitHub，需要生成一个 SSH 密钥对，并将公钥添加到 GitHub 上

``` bash
ssh-keygen -t rsa -b 4096 -C "your.email@example.com"
cat ~/.ssh/id_rsa.pub
```
复制输出的整个公钥。在刚刚创建仓库的 setting ，SSH and GPG keys 菜单添加公钥，然后测试 SSH 连接

``` bash
ssh -T git@github.com
```
#### 推送到 GitHub 仓库
安装 hexo-deployer-git。
``` bash
$ npm install hexo-deployer-git --save
```
在 _config.yml 中添加以下配置（如果配置已经存在，将其替换为如下）
``` yml
deploy:
  type: git
  repo: https://github.com/<username>/<project>
  # example, https://github.com/hexojs/hexojs.github.io
  branch: gh-pages
```
执行 
``` bash
hexo clean && hexo deploy
```
浏览 username.github.io 就能查看网站能否运作啦。