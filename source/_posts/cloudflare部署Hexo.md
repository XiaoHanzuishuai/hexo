---
title: 用Cloudflare Pages部署Hexo博客
date: 2026-02-21 15:30:00
tags:
  - Hexo
  - Cloudflare
  - 博客部署
  - 新手教程
categories:
  - 技术教程
keywords: Hexo部署,Cloudflare Pages,Hexo避坑,Reimu主题,Giscus评论
description: 全程复制即用，结合实际踩坑经验，从0到1部署Hexo博客，避开所有高频错误，新手也能一次成功。
---

## 一、前置准备（必做，复制命令检查）
### 检查Node.js和npm（推荐16.x/18.x，版本不符请重新安装）
``` bash
node -v
npm -v
```
### 检查Git（未安装请先安装git）
``` bash
git --version
```
## 二、本地搭建 Hexo 博客
### 步骤 1：初始化 Hexo 项目
``` bash
# 创建并进入博客目录
mkdir ~/hexo && cd ~/hexo

# 初始化Hexo
npm init hexo .
npm install

# 本地预览
npx hexo clean && npx hexo generate && npx hexo server
```
访问 http://你的服务器IP:4000 能看到默认页面，即为成功
### 步骤 2：安装并配置 Reimu 主题
``` bash
# 安装Reimu主题
npm install hexo-theme-reimu@1.12.0 --save
``` 
``` bash
# 生成主题配置文件
cat > ~/hexo/_config.reimu.yml << 'EOF'
avatar: "avatar.jpg"
author: "你的昵称"

generator_search:
  enable: true
  per_page: 10
  content: true

comment:
  enable: true
  title:
    zh-CN: "说些什么吧！"
  default: giscus

giscus:
  enable: true
  repo: "你的GitHub用户名/你的博客仓库名"
  repoId: "你从Giscus官网复制的repo-id"
  category: "Announcements"
  categoryId: "你从Giscus官网复制的category-id"
  mapping: pathname
  strict: 0
  reactionsEnabled: 1
  emitMetadata: 0
  inputPosition: bottom
  theme: light
  lang: zh-CN

clipboard:
  success:
    en: Copy successfully
    zh-CN: 复制成功 ✅
  fail:
    en: Copy failed
    zh-CN: 复制失败 ❌
  copyright:
    enable: false
    count: 50
    license_type: by-nc-sa

code_block:
  expand: true
  ```
  ### 步骤 3：配置语法高亮
  ``` bash
  # 打开Hexo根目录配置文件
nano ~/hexo/_config.yml
  ```
在 # Extensions 下方粘贴以下 YAML 配置：
``` bash
syntax_highlighter: highlight.js
highlight:
  wrap: true
  hljs: false
  ```
### 步骤 4：创建测试文章
``` bash
# 创建测试文章（Linux路径区分大小写）
hexo new "Hello World"
```
## 三、GitHub 创建仓库
### 1.打开 GitHub，新建仓库，命名为 hexo-blog，并设置为 Public（私有仓库无法使用 Giscus 评论）
### 2.入仓库 Settings → Features，勾选 Discussions 选项
### 3.复制仓库地址：https://github.com/你的用户名/hexo-blog.git
### 四、本地代码推送到 GitHub
``` bash
# 进入博客根目录
cd ~/hexo

# 初始化Git仓库
git init

# 添加所有文件到暂存区
git add .

# 提交修改
git commit -m "init: 初始化Hexo博客，配置Reimu主题和Giscus评论"

# 关联远程仓库（替换为你复制的仓库地址）
git remote add origin https://github.com/你的用户名/hexo-blog.git

# 推送到main分支（首次推送需用GitHub个人访问令牌登录）
git push -u origin main
```
## 五、Cloudflare Pages 部署
### 1.登录 Cloudflare 控制台，进入 Pages → 创建项目
### 2.连接 GitHub 账号，选择 hexo-blog 仓库
### 3.配置构建信息：
``` bash
构建命令：npm install && hexo generate
```
### 4.输出目录：public
### 5.根目录：/
### 6.点击「保存并部署」，等待 1-2 分钟，部署完成后即可通过 *.pages.dev 域名访问博客