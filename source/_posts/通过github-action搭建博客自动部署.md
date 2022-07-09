---
title: 通过github-action搭建博客自动部署
tags:
  - 博客
  - git
  - devops
date: 2022-07-09 14:15:47
---


Github Actions是Github提供的一种CI/CD工具，这里介绍如何使用github action来实现项目的自动部署。

<!--more-->

通常我们通过hexo部署博客，需要命令`hexo g`将写好的Markdown文件转换为HTML文件，然后在使用`hexo d`将生成的public文件推送到Github仓库中。同时如果需要备份博客的源文件还需要在通过git命令将文件推送到仓库或者分支。这里有了github actions我们可以通过自动化脚本一键完成。

#### Github Actions

![github actions](https://docs.github.com/cn/actions)是github 提供的一个持续集成和持续交付(CI/CD)平台，可以用于自动执行构建、测试和部署管道。可以通过Github Action来创建Devops。

Github Actions的工作流是一个可配置的自动化过程，它将运行一个或者多个作业。工作流程由签入到存储库的YAML文件定义，并在存储库的事件触发时运行。也可以手动触发，或者按定义的时间表触发。

Github Actions的工作流定义在`.github/workflows`文件夹中。


#### 开始配置

在之前部署的博客仓库中（一般命名为`{username}.github.io`这种形式），创建一个分支用于保存源码，使用master/main分支进行博客部署。

创建 Githun Personal Access Token用于pipeline将文件推送到主分支。具体方法参考![Creating a personal access token](https://docs.github.com/cn/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token)

设置Personal Access Token到仓库的Secrets。repo--> settings --> secrets。后面pipeline会通过这个环境变量拿到Githun Personal Access Token。

修改pipeline YAML脚本文件。我的文件如下：

```YAML
# This is a basic workflow to help you get started with Actions

name: Publish_blog

# Controls when the workflow will run
on:
  # Triggers the workflow on push or pull request events but only for the "main" branch
  push:
    branches: [ "blog-src" ]
  pull_request:
    branches: [ "blog-src" ]

  # Allows you to run this workflow manually from the Actions tab
  workflow_dispatch:
  
env:
  TZ: Asia/Shanghai

# A workflow run is made up of one or more jobs that can run sequentially or in parallel
jobs:
  # This workflow contains a single job called "puslish"
  puslish:
    name: Hexo blog build & deploy
    runs-on: ubuntu-latest # using the latest ubuntu as build and deploy env

    strategy:
      matrix:
        node-version: [14.x]
        # See supported Node.js release schedule at https://nodejs.org/en/about/releases/

    # Steps represent a sequence of tasks that will be executed as part of the job
    steps: 
    # Checks-out your repository under $GITHUB_WORKSPACE, so your job can access it
    - name: Checkout codes
      uses: actions/checkout@v3

    - name: Setup NodeJs
      uses: actions/setup-node@v3
      with:
        node-version: ${{ matrix.node-version }}
        cache: 'npm'

    - name: Install hexo dependencies
      run: | 
        npm install -g hexo-cli
        npm install
    
    - name: Build and Generate files
      run: | 
        hexo clean
        hexo generate

    - name: Deploy Hexo Blog
      env: 
        GITHUB_REPO : github.com/zhaoyun4411/zhaoyun4411.github.io
      run: | 
        ls
        cd ./public && git init && git add .
        git config --global user.name "yun.zhao"
        git config --global user.email "2294583704@qq.com"
        git add .
        git commit -m "GitHub Actions Auto Builder at $(date +'%Y-%m-%d %H:%M:%S')"
        git push --force --quiet "https://${{ secrets.ACCESS_TOKEN }}@$GITHUB_REPO" master:master
```

之后推送到`blog-src` pipeline 就可以自动部署博客了。

#### 参看文献

1. ![使用 GitHub Actions 自动部署 Hexo 博客到 GitHub Pages](https://zhuanlan.zhihu.com/p/161969042)
2. ![github actions](https://docs.github.com/cn/actions)
3. ![GitHub Actions 入门教程](http://www.ruanyifeng.com/blog/2019/09/getting-started-with-github-actions.html)
4. ![Creating a personal access token](https://docs.github.com/cn/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token)
5. ![GitHub 官方的 action](https://github.com/actions)
6. ![GitHub 官方市场中的 action](https://github.com/marketplace?type=actions)
7. ![第三方收集的有用的 action](https://github.com/sdras/awesome-actions)

