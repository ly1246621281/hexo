---
title: GitPage + Hexo + PicGo
date: {{date}}
author: zk
categories:
  - Others
tags: [hexo,website,前端]
description: 搭建免费静态文本网站 
---
# GitPage

作者：工匠羅
链接：https://www.zhihu.com/question/59088760/answer/265741938
来源：知乎
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。



搭建这个博客走了许多弯路。在这里分享总结之后的思路和简化步骤。

- Github Pages
- Hexo 博客框架
- 部署
- Next 主题

## **Github Pages**

Github Pages 其实本身就是 Github 提供的博客服务。 我们在 Github 中创建一个特定格式的 Repository，Github Pages 就会将里面的信息生成一个网页，展示出来。

**操作如下：** 

**1. 注册 Github 账号，然后在 Github 中创建一个以 .[http://github.io](https://link.zhihu.com/?target=http%3A//github.io) 结尾的 Repository。**

1. 1. Repository name: [http://ryanluoxu.github.io](https://link.zhihu.com/?target=http%3A//ryanluoxu.github.io)
   2. 勾选 Initialize this repository with a README
   3. Create repository

**2. 简单地编辑一下 README.md 这个文档。 比如添加：I am trying to create my own blog.. 保存(Commit changes)。**

**3. 打开网页：[http://ryanluoxu.github.io](https://link.zhihu.com/?target=http%3A//ryanluoxu.github.io) 这里就可以看到 README.md 里的内容了。**



如果没有太多的要求，其实直接用 README.md 来写博客也是不错的。这个生成好的 Repository 就是用来存放博客内容的地方，也只有这个仓库里的内容，才会被 [http://ryanluoxu.github.io](https://link.zhihu.com/?target=http%3A//ryanluoxu.github.io) 这个网页显示出来。

## **Hexo**

Hexo 是一个博客框架。它把本地文件里的信息生成一个网页。如果不需要放在网上给别人看，就没 Github Pages 什么事了。

使用 Hexo 之前，需要先安装 Node.js 和 Git。

**操作如下：**

**1. 安装 Node.js**

- 前往 [https://nodejs.org/en/](https://link.zhihu.com/?target=https%3A//nodejs.org/en/)
- 点击 8.9.1 LTS 下载
- 安装
- 打开 Command Prompt， 输入 `node -v`
- 得到：v8.9.1

安装成功



**2. 安装 Git**

- 前往 [https://git-scm.com/](https://link.zhihu.com/?target=https%3A//git-scm.com/)
- 点击 Downloads
- 点击 Windows
- 一般情况，下载会自动开始。如果没有，就点击 click here to download manually
- 安装
- 打开 Command Prompt， 输入 `git --version`
- 得到：git version 2.15.0.windows.1

安装成功

额外说明：如果 Git –version 指令不管用，可能需要到 Environment Variable 那里添加 Path。



**3. 安装 Hexo**

- - 打开 Command Prompt
  - 输入 `npm install -g hexo-cli`
  - 回车开始安装
  - 输入 `hexo -v`
  - 得到 hexo-cli: 1.0.4 等一串数据

安装成功



**4. 创建本地博客**

- - 在D盘下创建文件夹 blog
  - 鼠标右键 blog，选择 Git Bash Here。 如果没有安装 Git，就不会有这个选项。
  - Git Bash 打开之后，所在的位置就是 blog 这个文件夹的位置。（/d/blog）
  - 输入 `hexo init` 将 blog 文件夹初始化成一个博客文件夹。
  - 输入 `npm install` 安装依赖包。
  - 输入 `hexo g` 生成（generate）网页。 由于我们还没创建任何博客，生成的网页会展示 Hexo 里面自带了一个 Hello World 的博客。
  - 输入 `hexo s` 将生成的网页放在了本地服务器（server）。
  - 浏览器里输入 [http://localhost:4000/](https://link.zhihu.com/?target=http%3A//localhost%3A4000/) 。 就可以看到刚才的成果了。
  - 回到 Git Bash，按 Ctrl+C 结束。

此时再看 [http://localhost:4000/](https://link.zhihu.com/?target=http%3A//localhost%3A4000/) 就是无法访问了。



**5. 发布一篇博客**

- - 继续在 Git Bash 里，所在路径还是 /d/blog。输入 `hexo new "My First Post"`
  - 在 D:\blog\source_posts 路径下，会有一个 My-First-Post.md 的文件。 编辑这个文件，然后保存。
  - 回到 Git Bash，输入 `hexo g`
  - 输入 `hexo s`
  - 前往 [http://localhost:4000/](https://link.zhihu.com/?target=http%3A//localhost%3A4000/) 查看成果。
  - 回到 Git Bash，按 Ctrl+C 结束。



## **将本地 Hexo 博客部署在 Github 上**

前面两个部分，我们已经有了本地博客，和一个能托管这些资料的线上仓库。只要把本地博客部署（deploy）在我们的 Github 对应的 Repository 就可以了。

**1. 获取 Github 对应的 Repository 的链接。**

- 登陆 Github，进入到 [http://ryanluoxu.github.io](https://link.zhihu.com/?target=http%3A//ryanluoxu.github.io)
- 点击 Clone or download
- 复制 URL 待用

我的是 `https://github.com/Ryanluoxu/ryanluoxu.github.io.git`



**2. 修改博客的配置文件**

- - 打开配置文件 /d/blog/_config.yml （使用 bash 里的 vi 或者 notepad++）
  - 找到 `#Deployment`，填入以下内容：

```
deploy:    type: git    repository: https://github.com/Ryanluoxu/ryanluoxu.github.io.git    branch: master 
保存退出
```



**3. 部署**

- - 回到 Git Bash
  - 输入 `npm install hexo-deployer-git --save` 安装 hexo-deployer-git 此步骤只需要做一次。
  - 输入 `hexo d`
  - 得到 `INFO Deploy done: git` 即为部署成功

之前我们创建的 ReadMe.md 会被自动覆盖掉。



**4. 查看成果**

前往 [http://ryanluoxu.github.io](https://link.zhihu.com/?target=http%3A//ryanluoxu.github.io) 即可。



## **使用 Next 主题**

[更多 Hexo 的主题看这里](https://link.zhihu.com/?target=https%3A//hexo.io/themes/)

这里以 Next 为例。 大概思路就是把整个主题的文件克隆到我们的主题文件夹中。在配置文件中注明使用该主题。

**操作如下：**

**1. 还是回到 Git Bash。 输入** `git clone https://github.com/iissnan/hexo-theme-next themes/next`

这样，该主题的文件就全部克隆到 D:\blog\themes\next 下面。



**2. 修改博客配置文件**

- - 打开 D:\blog_config.yml
  - 找到 `theme:`
  - 把 Hexo 默认的 lanscape 修改成 next。 即 `theme: next`
  - 找到 `# Site`，添加博客名称，作者名字等。
  - 在 `language` 后面填入 en 或者 zh-Hans，选择英文或者中文。
  - 找到 `# URL`, 填入 url。比如 `url: https://ryanluoxu.github.io`

填入名字后会有很风骚的 © 2017 Ryan Luo Xu 的字样出现在博客底部。



**3. 重新生成部署即可**

- - 回到 Git Bash。输入 `hexo g -d`就可以了。

先把修改的内容生成网页，再部署。



**4. 查看成果**

前往 [http://ryanluoxu.github.io](https://link.zhihu.com/?target=http%3A//ryanluoxu.github.io) 即可。

搭建好之后，继续个性化的设置参考：''''

## 配置 PicGo (CDN 加速)

![img](https://mmbiz.qpic.cn/mmbiz_png/R5ic1icyNBNd7t0Yic5aTV1rqBJKicYJ6an6pMTyqIZuaRkfbRXUmLpicpyfjpGRRUVyww2cS88fa5nRkcBb5eA0PQA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



- 仓库名：用户名/仓库名
- 分支名：所属分支
- token：

- ![img](https://mmbiz.qpic.cn/mmbiz_png/R5ic1icyNBNd7t0Yic5aTV1rqBJKicYJ6an6ZIhtiblBUicYOf3pJlujt4WKiavibPjnSfWawA2jKWZ5I76FFp83fccN5g/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



- 指定路径：仓库下的指定文件夹
- 自定义域名：使用了 CDN 加速，配置格式为：


```
https://cdn.jsdelivr.net/gh/用户名/仓库名
```



**设置 Typora**



![img](https://mmbiz.qpic.cn/mmbiz_png/R5ic1icyNBNd7t0Yic5aTV1rqBJKicYJ6an6KE2AgAOzPrLYBIic0Gh5Q064ymU8EqzmWJJj8XfibialoaatcmN1QSteg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

![img](https://mmbiz.qpic.cn/mmbiz_png/R5ic1icyNBNd7t0Yic5aTV1rqBJKicYJ6an6pQhFF2BIEQDWNQrFom7jCibf1fBZm4miaWECib807y0EpohrS3YBDRwOg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



如果出现以下情况：

![img](https://mmbiz.qpic.cn/mmbiz_png/R5ic1icyNBNd7t0Yic5aTV1rqBJKicYJ6an6z8OSeopfdNCF3icI5y5DulNHaibw72wcb3js7lwWFh5KSZarFa9u1qKQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



点击PicGo界面左侧的PicGo设置->设置Server，并按下图设置（默认已设置）

![img](https://mmbiz.qpic.cn/mmbiz_png/R5ic1icyNBNd7t0Yic5aTV1rqBJKicYJ6an6zdIaCw3R0mibSs3GzEvLtFrEVWW9BkFI1cwHticw8b5KDlHvOIPn3aicA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

至此，便可通过 Typora 优雅的编写 Markdown 文档，图片粘贴到文档就能够自动上传到 github 图床了。

** **

![image-20201205205536651](https://raw.githubusercontent.com/ly1246621281/PicGo/main/img/image-20201205205536651.png)