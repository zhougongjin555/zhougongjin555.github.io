---
title: "Pictures_lab"
date: 2022-12-15T14:48:45+08:00
draft: true
---

# picGo + GitHub + jsdelivr 实现白嫖图床并加速

picGo: `https://github.com/Molunerfinn/PicGo`

## 1、准备阶段

- 创建一个github公共库

  ​	省略...

  

- 创建githubtoken

![image-20221214181607559](https://cdn.jsdelivr.net/gh/zhougongjin555/pics@main/images/image-20221214181607559.png)

![image-20221214181713382](https://cdn.jsdelivr.net/gh/zhougongjin555/pics@main/images/image-20221214181713382.png)

![image-20221214181852555](https://cdn.jsdelivr.net/gh/zhougongjin555/pics@main/images/image-20221214181852555.png)

![image-20221214182016569](https://cdn.jsdelivr.net/gh/zhougongjin555/pics@main/images/image-20221214182016569.png)

## 1、picGo

PicGo是一个用于快速上传图片并获取图片 URL 链接的工具。

PicGo官方指南：[PicGo | PicGo](https://link.zhihu.com/?target=https%3A//picgo.github.io/PicGo-Doc/zh/guide/%23%E5%BA%94%E7%94%A8%E6%A6%82%E8%BF%B0)

本文使用picGo-app为例

![image-20221214182146835](https://cdn.jsdelivr.net/gh/zhougongjin555/pics@main/images/image-20221214182146835.png)

自定义域名：`https://cdn.jsdelivr.net/gh/zhougongjin555/pics@main`

前缀为固定格式 https://cdn.jsdelivr.net/gh/仓库名@分支 实现 jsdelivr cdn加速





## eg: 推荐插件

![image-20221214183523646](https://cdn.jsdelivr.net/gh/zhougongjin555/pics@main/images/image-20221214183523646.png) 
