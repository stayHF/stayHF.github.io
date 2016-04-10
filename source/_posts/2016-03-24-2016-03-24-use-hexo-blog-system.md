---
title: 使用 Hexo 在 GitHub 上搭建博客
tags:
- Hexo
- Github
- Travis CI
categories: blog
date: 2016-03-24 13:56:13
---

使用博客来记录平时工作、生活中的各种心得和体会，是很好的习惯和积累方法。经常能在各种招聘帖中看到`请提供 GitHub 帐号和个人博客地址`，最近利用闲暇时间使用 Hexo 在 GitHub 上搭建了一个博客。在此，将过程记录下来。
## 阅读之前
本文使用Mac系统进行示例，在完整阅读这篇博客之前，先介绍下文章的整体结构。
* **预备知识**介绍搭建博客之前，需要具备的一些知识点；
* **搭建基本博客**介绍搭建一个基本的Hexo博客、以及如何编写文章和发布；
* **进阶设置**介绍一些更高级的配置；
* **自动化部署**介绍如何在多台机器都能编写博客、以及如何让文章自动化生成和部署。

![](http://7xskzj.com1.z0.glb.clouddn.com/blog_overview.png)

<!-- More -->

## 预备知识
在开始介绍搭建过程之前，我们需要掌握一些基本的知识和技能。
### Git相关
整个博客的搭建、后续的发布和部署都需要使用Git的相关命令，包括：
* 克隆仓库
* 创建分支
* 更新和提交
* GitHub 的 Personal Access Token

GitHub 提供了一个客户端 [GitHub Desktop](https://desktop.github.com)，提供了这些基本功能。如果对这块不了解，可以先去玩玩它。

### Homebrew
Homebrew 是做什么的？[官网](http://brew.sh/index_zh-cn.html) 给出了明确解释：**OS X 不可或缺的套件管理器！** 同时，也给出了安装方法。

### Node.js 和 NPM
* Node.js 内嵌 **V8** 引擎，是目前非常流行的JavaScript运行环境；
* NPM 是 Node.js 包管理器。

我们使用 [NVM](https://github.com/creationix/nvm)(Node.js Version Manager) 来安装 Node.js 环境.
#### 首先安装 NVM
```bash
$: brew install nvm
```
#### 使用 NVM 安装 Node.js
```shell
$: nvm ls
node -> stable (-> v5.9.0) (default)
stable -> 5.9 (-> v5.9.0) (default)
iojs -> N/A (default)
$: nvm install 5.9.0
```
这样 Node.js 和 NPM 就都安装好了。
### Hexo
Hexo的安装很简单:
```shell
$: npm install hexo-cli -g
```
关于Hexo的更多指令，去[官网](https://hexo.io)看看吧！
## 搭建基本博客
掌握[预备知识](#预备知识)后，我们就可以开始搭建博客了。
### 创建仓库
GitHub在[这里](https://pages.github.com/)给出了如何去创建一个博客仓库的过程:
![](http://7xskzj.com1.z0.glb.clouddn.com/create_blog_repo.png)
就是说如果你在 GitHub 上的用户名是 `andy`, 那么你需要创建一个叫`andy.github.io` 的仓库。

创建成功之后，在仓库里放一个叫index.html的文件，假设是下面的内容：
```html
<!DOCTYPE html>
<html>
  <head>
    <title>Hello World!</title>
  </head>
  <body>Hello world!</body>
</html>
```
几分钟之后，GitHub服务器会自动为你开通博客域名 **https://andy.github.io**

访问一下，看看效果吧！
### 创建博客目录
准备工作已经完成，下面就可以在本地创建一个博客目录，以后我们写博客就都在这个目录下进行。

```shell
$: hexo init blog
blog $: cd blog
blog $: npm install
```
这里简单介绍下blog的目录结构：
```shell
blog
   |---scaffolds             ------> 模版目录
   |           |---draft.md      ------> 博客草稿模版
   |           |---page.md       ------> 页面模版
   |           |---post.md       ------> 博客模版
   |---source                ------> 数据目录
   |        |---_drafts          ------> 草稿目录
   |        |---_posts           ------> 博客目录
   |---themes                ------> 主题目录
   |        |---landscape        ------> 自带默认主题
   |---_config.yml           ------> 博客站点配置文件
   |---package.json          ------> AMD包配置文件
```
### 配置
在开始编写博文之前，需要修改 **_config.yml** 来对博文站点进行配置。
这些配置包括：
* 网站
* 网址
* 目录
* 文章
* 分类和标签
* 日期／时间格式
* 分页
* 扩展

大部分的内容请参考 [Hexo官网的配置文档](https://hexo.io/zh-cn/docs/configuration.html)。

下面主要说一下**扩展**里关于**部署**部分的配置：
```yml
deploy:
  type: git
  repo: https://{token}@github.com/andy/andy.github.io.git
  branch: master
  message: "update post"
```
注意上面的 **{token}**, 它是你 GitHub 上的 Personal Access Token。这样有一个好处，就是你在部署时就不需要输入GitHub 用户名和密码。

### 草稿
创建博文指令：
```shell
blog $: hexo new [layout] title
```
`layout`分为 post、page 和 draft, 不指定的话默认是 **post**。

例如：`hexo new draft "begin-blog"` 将在 **blog/source/_drafts/** 目录下创建一篇博文草稿 **begin-blog.md**

之后，用你喜欢的编辑器打开 **begin-blog.md** 来写作。

### 发布
草稿要使用发布命令来将其移动到 **blog/source/_posts** 目录下。
```shell
blog $: hexo publish begin-blog
```
简单起见，我会直接创建 **layout 为 post** 的博文，省去**发布**过程。

### 生成
将发布的博文从 **.md** 生成为 **.html**
```shell
blog $: hexo generate
```
### 预览
可以在本地启动一个http server来预览生成的博文。
```shell
blog $: hexo server
```
服务器默认侦听4000端口，浏览器打开 http://localhost:4000

### 部署
博文没有问题，就可以部署到 GitHub 上了。
```shell
blog $: hexo deploy
```
试试访问 https://andy.github.io 看看吧！

现在，你有了一个自己的博客，使用默认的主题。可以很方便的写博客和发布博客。
或许，你觉得这个主题你并不喜欢，或者一些很细节的问题没有解决。比如：评论、分享、搜索等，那么可以再看看下面的进阶配置。

## 进阶配置
### 配置主题
选一个合适的主题非常重要。合适的主题让你的心情更加舒畅，没准让你能孕育出一篇旷世奇文。

[Hexo 官网](https://hexo.io/themes/)有很多非常优秀的主题，这里我以 [NexT](https://github.com/iissnan/hexo-theme-next) 主题作为例子，因为它看起来简洁而又功能齐全。

安装 NexT 主题
```shell
blog $: git clone https://github.com/iissnan/hexo-theme-next.git themes/next
```

启用 NexT 主题

在站点配置文件 **_config.yml** 中，修改使用的主题：
将
```shell
theme: landscape
```
修改为
```shell
theme: next
```

### 配置阅读次数

NexT 主题使用 [leancloud](https://leancloud.cn/) 统计每篇博文的访客。首先去注册并拿到：
* app_id
* app_key

配置 NexT 主题的 _config.yml (位于blog/themes/next)
```shell
leancloud_visitors:
  enable: true
  app_id: {your_app_id}
  app_key: {your_app_key}
```

### 配置评论系统

NexT 支持的评论系统有 **Disqus** 和 **duoshuo**, 鉴于国内的网络环境，使用duoshuo还是更好一些。

去duoshuo注册拿到 shortname， 并配置主题配置文件:
```shell
duoshuo_shortname: your_duoshuo_shortname
```

### 配置站点地图
这里只示例向Google提供站点地图, 让其它人能通过Google搜索到你的博文。

默认的博客目录并没有带生成站点地图的组件，需要我们手动添加：
```shell
blog $: npm install --save hexo-generator-sitemap
blog $: npm install --save hexo-generator-robotstxt
```
配置站点配置文件
```yml
sitemap:
  path: sitemap.xml

Plugins:
- hexo-generator-robotstxt
- hexo-generator-sitemap

robotstxt:
  useragent: "*"
  sitemap: /sitemap.xml
```
最后，去google网站添加验证，参考：
- [hexo向google提交sitemap](http://www.yuanye097.com/2015/10/27/hello-world/)
- [Google Webmaster tools](http://theme-next.iissnan.com/third-party-services.html#google-webmaster-tools)

### 配置站内搜索
可以预见到当博文比较多时，使用站内搜索来搜索博文非常必要。这里使用 Swiftype 进行站内搜索。

[去Swiftype注册并创建一个搜索引擎](http://theme-next.iissnan.com/third-party-services.html#swiftype)，得到swiftype_key, 配置`主题配置文件`:
```yml
swiftype_key: your_swiftype_key
```

### 使用图床
介绍使用七牛的图床，首先去七牛注册并创建一个空间, 拿到：
* 空间名称
* access_key
* secret_key

下载本地命令行同步工具 [qasync](http://developer.qiniu.com/code/v6/tool/qrsync.html), 假设放在/usr/local/bin/qiniu-devtools:
1. 在/usr/local/bin/qiniu-devtools目录下新建 **conf.json** 文件， 内容如下：
```json
{
    "src": "/Users/andy/Pictures/qiniu-images",
    "dest": "qiniu:access_key={access_key}&secret_key={secret_key}&bucket={空间名称}",
    "debug_level": 1
}
```
2. 修改~/.bash_profile文件，增加：
```shell
alias qiniu="/usr/local/bin/qiniu-devtools/qrsync /usr/local/bin/qiniu-devtools/conf.json"
```
3. 让~/.bash_profile文件生效：
```shell
$: source ~/.bash_profile
```
4. 将你要上传到七牛的图片放到`/Users/andy/Pictures/qiniu-images`， 然后在命令行执行：
```shell
$: qiniu
```
上传成功之后，就可以七牛网站上拿到图片的外链地址了。

现在，你的博客使用着自己喜欢的主题，阅读次数、Google搜索、站内搜索、评论等一应俱全，是不是很开心，但还有些技巧也很有意思，能够简化你博客的部署过程，不妨往下看看？
## 自动化部署
看到文章最开始的配图了吗？这里将图片再贴一次，并描述一个强需求的使用场景：
![](http://7xskzj.com1.z0.glb.clouddn.com/blog_overview.png)

1. 假设你在公司和家里都想能对你的博客进行操作，那么将本地博客目录提交到 GitHub 来进行管理就是个好主意；
2. 静态博文和原始博文在 GitHub 的同一个仓库的**不同分支**上；
3. 当 Travis CI 检测到你的原始博文有变动时，将自动生成静态博文，并将其自动提交到你的博客仓库中。

将这张图的内容分解为下面几步来实现。

### 将本地博客目录提交到Github
GitHub上的博客仓库默认分支是 **master**，用来存放你生成的静态博文。 通过域名访问博客时，WebServer也是取的这个分支下的内容。
在这个仓库上创建一个分支 **source**，用来存放你的原始博文。

我们之前是将本地静态博文直接部署到 GitHub 博客仓库的**master**分支。
1. 博客仓库克隆到本地：
```shell
$: git clone https://www.github.com/andy/andy.github.io.git
```
2. 创建 **source** 分支
```shell
$ cd andy.github.io
andy.github.io $: git checkout -b source
```
3. 删除 **source** 分支下除了**.git**目录外的所有文件
```shell
andy.github.io $: ls | xargs rm -rf
```
4. 将本地博客目录中的文件复制过来
```shell
andy.github.io $: cp /path/to/blog/* ./
```
5. 在目录下新建 `.gitignore` 文件，内容如下：
```text
.deploy*/
node_modules/
public/
db.json
```
6. 提交 **source** 分支
```shell
andy.github.io $: git commit -am "first commit"
andy.github.io $: git push origin source
```

### 加密 Personal Access Token
要授权 Travis CI 对我们博客仓库的 **master分支** 进行更新，我们需要告诉它 token，但这个token的权限很高。一旦泄漏，别人就可以完全操作的博客仓库。

因此，Travis CI 提供了一种加密机制。将token加密后的内容放在你的构建脚本中是安全的，因为只有 Travis CI 才可以解密使用。

1. 申请 GitHub Personal Access Token

  申请请参考链接 [申请GitHub Token](https://help.github.com/articles/creating-an-access-token-for-command-line-use/)

  **Token申请到后只显示一次，请保存在安全的地方!**


2. 安装 Travis CI 命令行工具：
```shell
$: gem install travis
```
3. 使用 GitHub账号登录：
```shell
$: travis login
```
4. 创建 Travis CI 配置文件 `.travis.yml`
```shell
andy.github.io $: touch .travis.yml
```
5. 加密 token
```shell
andy.github.io $: travis encrypt 'GH_TOKEN={TOKEN}' --add
```
将其中`{TOKEN}` 部分换成你申请到的token即可。
执行完后，会在 `.travis.yml` 中增加类似以下内容：
```shell
env:
  global:
  - secure: 12345ABJKLFJDALSJFALUERI3U2N3N2LJNVZJFDKLAJF
```

### 开启博客仓库的 Travis CI
接下来，我们去 [Travis CI网站](https://www.travis.org), 开启博客仓库的持续集成。
![](http://7xskzj.com1.z0.glb.clouddn.com/start_travis_ci.png)
如果看不到你的仓库，点击一下右上角的同步。

点击仓库的小齿轮，配置一下细节：
开启 `Build only if .travis.yml is present`

### 配置 Travis CI
最后，我们完善一下 Travis CI 配置文件 `.travis.yml`
这里给出一个实例：
```yml
language: node_js
node_js:
- '5'
branches:
  only:
  - source
before_install:
- npm install -g hexo-cli
install:
- npm install
script:
- hexo g
after_success:
- git config --global user.name "andy"
- git config --global user.email "andy@xxx.com"
- sed -i'' "/^ *repo/s~github\.com~${GH_TOKEN}@github.com~" _config.yml
- hexo d --silent

cache:
  directories:
    - node_modules
env:
  global:
  - secure: JFDLASJFALJF4U538412JFKLDAJSVNFADLGJFALFJALJF
```

内容很容易理解，就不解释了。

现在，试试修改一篇博文，并提交一次看看吧！
