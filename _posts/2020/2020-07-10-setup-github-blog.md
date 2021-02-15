---
layout: post
title: GitHub 上搭建个人博客
category: GitHub
tags: [GitHub, Jekyll, 博客]
keywords: GitHub,Jekyll,博客
excerpt: GitHub上搭建个人博客有以下优点，个人域名、无广告。对程序员来说，不失为一个好的选择。
---

GitHub 上搭建个人博客有以下优点，个人域名、无广告。对程序员来说，不失为一个好的选择。下面具体讲解。

## 1、创建GitHub博客

在 GitHub 上创建名为 `<yourname>.github.io` 的 repo。

博客的访问地址是 `https://<yourname>.github.io/`。

## 2、本地安装Jekyll开发环境

[Jekyll](https://jekyllrb.com/) 是一个网站生成工具，只生成静态网页，不需要数据库支持。Jekyll 生成的网站可以免费部署在 GitHub 上。笔者使用 Windows 系统，以下步骤在 Windows 上完成。

### 安装Ruby

下载安装 [RubyInstallers](https://rubyinstaller.org/)。

### 安装Jekyll
```
gem install jekyll
```

### 使用Jekyll

使用以下命令创建测试博客。

```
jekyll new testblog
cd testblog
jekyll serve --host localhost
```

如果前面两步没问题，输出应该是这样。

```
Configuration file: C:/Data/Workspace/Eclipse/Jekyll/testblog/_config.yml
            Source: C:/Data/Workspace/Eclipse/Jekyll/testblog
       Destination: C:/Data/Workspace/Eclipse/Jekyll/testblog/_site
 Incremental build: disabled. Enable with --incremental
      Generating...
       Jekyll Feed: Generating feed for posts
                    done in 0.771 seconds.
 Auto-regeneration: enabled for 'C:/Data/Workspace/Eclipse/Jekyll/testblog'
    Server address: http://localhost:4000/
  Server running... press ctrl-c to stop.
```

在浏览器中输入 [http://localhost:4000/](http://localhost:4000/)，即可打开刚创建的博客。

## 3、下载使用Jekyll模板

### 下载Jekyll模板

在 [Jekyll Themes](http://jekyllthemes.org/) 上选择模板，并下载。笔者选择的是 [Yummy Theme](http://jekyllthemes.org/themes/yummy-theme/)。这个风格简单大方，很符合程序员风格。

### 使用Jekyll模板

解压模板文件夹，删除 `Gemfile` 和 `Gemfile.lock` 这两文件。这两个文件中包含了当时编译模板使用的 Ruby gem 依赖包的版本。由于 Ruby gem 的升级，当时的依赖包版本，在最新的 Ruby gem 中有可能已经不存在了，所以必须删除。最新的 Ruby gem 不会生成这个两个文件。

修改 `_config.yml`，替换自己的 GitHub 信息。

删除 `_posts` 中的内容，撰写自己的博客内容。

用 `jekyll serve --host localhost` 启动 Jekyll。

启动后，控制台中有可能会出现相关依赖没找到的情形。使用 `gem install <dependency>` 安装依赖。

### 上传Jekyll模板

`Git Push` 模板文件夹的内容到 GitHub 的 repo 中。刷新博客地址，即可看到自己撰写的博客。
