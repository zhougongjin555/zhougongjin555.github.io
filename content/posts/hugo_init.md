---
title: "Hugo_init"
date: 2022-12-15T14:46:29+08:00
draft: true
---

# hugo构建个人博客并部署到github

hugo 官方网站： `https://gohugo.io/getting-started/quick-start/`

```itemId
itemId
```

## 1、 构建基础hugo项目

```bash
hugo new site quickstart  # 初始化项目

cd quickstart 

git clone https://github.com/adityatelange/hugo-PaperMod themes/PaperMod --depth=1  # 下载主题
echo "theme = 'Papermod'" >> config.yaml  # 将主题放入配置文件

hugo server -D    # 构建并启动本地博客


```

![image-20221214144437006](https://cdn.jsdelivr.net/gh/zhougongjin555/pics@main//imagesimage-20221214144437006.png)

## 2、新建博客内容

```
hugo new posts/test.md

hugo server -D
```

## 3、挂载GitHub pages

我个人经常使用 GitHub，也见到很多大佬利用 GitHub pages 挂载自己的个人网站，发现配置起来也很简单，因此选择使用 GitHub pages 来进行配置，关于 GitHub pages 可以查看[官网](https://link.zhihu.com/?target=https%3A//pages.github.com/)，主要包括四个步骤：

1. 创建一个与 `username` 同名的 **空**`username.github.io` 仓库，不包含任何内容，如 `readme.md，比如我的用户名为`xxx, 因此我创建了一个仓库，名为`xxx.github.io`;

2. 克隆仓库到本地
   git clone [https://github.com/xxx/xxx.github.io](https://link.zhihu.com/?target=https%3A//github.com/xxx/xxx.github.io)

3. 添加个人网站内容到该仓库
   \# copy 生成的网站内容到仓库文件夹下
   cp -rf quickstart/public/* [http://xxx.github.io/](https://link.zhihu.com/?target=http%3A//xxx.github.io/)

4. 将文件内容同步更新到 GitHub 服务器上
   cd [http://xxx.github.io](https://link.zhihu.com/?target=http%3A//xxx.github.io)
   git add .
   git commit -m "init the website"
   git push

   此时，通过进入 [https://xxx.github.io](https://link.zhihu.com/?target=https%3A//kinredon.github.io/) 即可访问自己的个人网站。

上面的步骤略显麻烦，每次需要将生成在 `public` 文件夹下的目录拷贝到 xxx`.github.io` 目录下，然后发布到远程服务器。根据 [Host on GitHub](https://link.zhihu.com/?target=https%3A//gohugo.io/hosting-and-deployment/hosting-on-github/)，发布到 GitHub Pages 有两种方式, 一种是直接使用仓库目录下的 `doc` 目录作为原本 `public` 目录，详情可以参考 [hugo 博客搭建](https://link.zhihu.com/?target=https%3A//patrolli.github.io/xssq/post/hugo_%E5%8D%9A%E5%AE%A2%E6%90%AD%E5%BB%BA/)。我采用的方式是利用 GitHub Action 自动完成上述过程。

## 4、GitHub Action相关

原理：使用一个新的 `source` 分支进行写作，写作完成后上传 `source` ，GitHub Action 自动将 `source` 分支的 `publish` 文件夹拷贝到 `master` 分支，从而完成文章的发布。

```bash

git add origin git@github.com:xxx/xxx.github.io.git   # 与远程分支建立联系

git checkout -b Source   # 切换本地分支

git remote add_upstream origin Source # 新建并上传远程分支


```

