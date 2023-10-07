---
title:  hexo常用命令
date: {{date}}
author: zk
categories:
  - Others
tags: [hexo,website,前端]
description: 文章摘要 

---
## hexo常用命令

[TOC]

我们在前面的已经略微的接触了一些hexo的命令，如 hexo new "my blog" ， hexo server 等。下面来介绍一下我们经常会用到的hexo命令

### 1、新建

```cpp
hexo new "my blog"
```

新建的文件在 hexo/source/_posts/my-blog.md

### 2、生成静态页面

```undefined
hexo g
```

一般部署上去的时候都需要编译一下，编译后，会出现一个 public 文件夹，将所有的md文件编译成html文件

### 3、开启本地服务

```undefined
hexo s
```

这个命令，我之前已经用过了，开启本地hexo服务用的

### 4、部署

```undefined
hexo d
```

部署到git上的时候，需要用这个命令，下一篇中，我们会使用到这个命令

### 5、清除 public

```undefined
hexo clean
```

当 source 文件夹中的部分资源更改过之后，特别是对文件进行了删除或者路径的改变之后，需要执行这个命令，然后重新编译。

### 6、更新algolia搜索Index

```java
hexo algolia
```

当发布新文章时需要在Algolia数据库添加新的Index，这样才能添加到搜索中

[关于安装algolia，参考next官网请](https://link.jianshu.com?t=http://theme-next.iissnan.com/third-party-services.html#algolia-search)

### 7、安装hexo-git

```hexo
npm install hexo-deployer-git --save

 $ hexo clean  
 //该命令的作用是清除缓存，若不输入此命令，服务器有可能更新不了主题
 $ hexo g -d
```

作者：Johan007
链接：https://www.jianshu.com/p/643577a900f8
来源：简书
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。