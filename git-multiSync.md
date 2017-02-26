title: Github Pages+hexo博客的创建与多机更新博客的实现

date: 2017/2/26 19:00:00

catergories:

- Study

tags:

- BlogTools
- Github
- hexo

---

### 0 前言

---

本篇博文主要介绍在Windows 10系统上创建Github Pages+hexo博客的过程，同时，考虑到可能会在不同地点使用不同机器进行博客的更新操作，所以本篇博文还会介绍如何实现多台机器更新博客内容的方法，主要涉及的名词有：

- Github与Github Pages

  [Github](https://github.com/) 是一个面向开源以及私有软件项目的托管平台，简单来说就是一个远程仓库，注册账号以后可以在Github上存放软件代码，当然也可以存放文件，但是要注意的是，免费使用的情况下，你的Github是开放的，也就是所有人都可以看到你的仓库里存放了什么，如果想要加密则需要付费。

  Github Pages则是在Github上搭建的博客或者网页，网页地址都是 `<your_githubname>.github.io` 。既然Github可以存放软件代码，那当然也可以存放博客文章以及网页需要的HTML/CSS/JS文件，从而Github Pages以Github仓库作为网页服务器，向外界提供一个可以访问的静态网页。

- Markdown

  [Markdown](http://sspai.com/25137/) 是一种用于写作的轻量级标记语言，虽然不像Word那样拥有大量的排版和字体设置，可以排版出一篇或精美或专业的文章，但是Markdown中简单的标记字符易于使用和记忆，甚至可以完成脱离鼠标，单单使用键盘就可以完成一篇文章的书写以及简洁精致的排版，这让使用者可以更加专心的码字，而不是过多地在意繁琐的排版工作。

  Markdown有很多相关的编辑器，这里强烈推荐[Typora](https://typora.io/) 。

  *有关Markdown编辑器的介绍和分析，请见另一篇博文《Typora——Markdown编辑界的极简主义》* 

- hexo

  [hexo](https://hexo.io/) 是一款基于[node.js](https://nodejs.org/en/) 、开源的静态博客生成器。所谓“静态”，指的是所生成的博客网页本身只能提供浏览，而不能提交信息（当然，如果嵌入第三方插件，则可以通过插件提交信息，这是后话...）。hexo将书写好的Markdown文件根据选定的hexo主题转换成一堆HTML/CSS/JS文件，并支持本地预览网页，如果觉得没有问题，hexo还提供部署的功能，将网页文件上传到Github中，之后就可以通过访问Github Pages看到已发布的博客网页。

- Git

  [Git](https://git-scm.com/) 是一种开源的分布式版本管理系统，Github是Git发展壮大之后的产物，只支持Git作为唯一的版本库格式进行托管。

  *有关Git的命令操作，请见另一篇文章《Git Study》*

### 1 创建Github Pages并建立Github与本地的连接

---

#### 1.1 注册Github并创建一个新仓库（Repository）

登录Github网站并注册账号成功之后，可以按照<span id="create-repo"></span>[Create a new repository on GitHub](https://help.github.com/articles/create-a-repo/#create-a-new-repository-on-github) 的官方教程，一步一步地创建一个新的仓库，需要注意的只有一点：即因为我们这里是为了创建Github Pages博客的仓库，所以Repository name一定要是`<your_githubname>.github.io` 的形式。

#### 1.2 建立Github与本地的连接（Authenticating）

为了能够将本地的博客内容提交到远端的Github仓库中，需要建立本地与Github仓库的连接<span id="create-githublink"></span>，即要让本地通过Github的身份验证（Authenticating）。考虑到Git默认使用SSH协议，所以我们使用SSH协议建立本地与Github之间的连接，参照[Connecting to GitHub with SSH](https://help.github.com/articles/connecting-to-github-with-ssh/) 。*（以下命令需要安装Git，并在Git Bash中完成输入）* 

当然也可以使用HTTPS建立连接，具体可以参照[Authenticating with GitHub from Git](https://help.github.com/articles/set-up-git/#next-steps-authenticating-with-github-from-git) 并做进一步的阅读。

##### 1.2.1 本地生成新的SSH Key

- 执行以下命令，输入你注册Github时使用的邮箱：

   `ssh-keygen -t rsa -b 4096 -C "your_email@example.com"` 

- 出现以下提示的时候，说明已经使用你的邮箱生成了一个新的SSH Key：

  `Generating public/private rsa key pair.` 

- 随后会出现以下提示，如果你对保存SSH Key的位置没有特殊的要求时，可以直接回车选择默认保存位置进行SSH Key的保存，保存的位置就是括号中所示的位置（根据实际系统有所不同）：

  ```
  Enter a file in which to save the key (/Users/you/.ssh/id_rsa):[Press enter]
  ```

- 接下来，需要为你的SSH Key输入密码，当然也可以选择不要密码，直接两个回车就可以了：

  ```
  Enter passphrase (empty for no passphrase): [Type a passphrase]
  Enter same passphrase again: [Type passphrase again]
  ```

**注** ：如果你之前就有SSH Key，那么你可以检查一下是不是有合适的SSH Key可以使用：`ls -al ~/.ssh` 

##### 1.2.2 将SSH Key加入ssh-agent

- 确定ssh-agent正在运行，执行以下命令开启ssh-agent：

  `eval $(ssh-agent -s)` 

- 将SSH Key添加到ssh-agent中：

  `ssh-add ~/.ssh/id_rsa` 

##### 1.2.3 将SSH Key添加到Github账号中

- 将SSH Key复制到粘贴板：

  ````
  clip < ~/.ssh/id_rsa.pub
  ````

- 登录Github，点击右上方的头像，在下拉菜单中选择**Setting** ，然后**SSH and GPG keys** —— **New SSH Key** 或者**Add SSH Key** ，在"Title"里输入你想要的名称，然后在下方的“Key”文本框内添加刚才复制的SSH Key，最后点击**Add SSH Key** ，密码验证一下就好。

##### 1.2.4 测试一下SSH连接是否成功

- 执行以下命令：

  `ssh -T git@github.com` 

  若输出以下两则信息中的一种，且其中一种是你的签名信息，那么输入`yes` 并回车即可：

  ```
  The authenticity of host 'github.com (192.30.252.1)' can't be established.
  RSA key fingerprint is 16:27:ac:a5:76:28:2d:36:63:1b:56:4d:eb:df:a6:48.
  Are you sure you want to continue connecting (yes/no)?
  ```

  ```
  The authenticity of host 'github.com (192.30.252.1)' can't be established.
  RSA key fingerprint is SHA256:nThbg6kXUpJWGl7E1IGOCspRomTxdCARLviKw6E5SY8.
  Are you sure you want to continue connecting (yes/no)?
  ```

- 之后就可以看到验证成功的消息，确认其中有你的用户名：

  ```
  Hi <username>! You've successfully authenticated, but GitHub does not provide shell access.
  ```

  如果出现了`access denied` 的错误信息，请参照 [Error: Permission denied (publickey)](https://help.github.com/articles/error-permission-denied-publickey) 以及 [read these instructions for diagnosing the issue](https://help.github.com/articles/error-permission-denied-publickey) 进行修复。

### 2 Hexo本地建站

---

#### 2.1 hexo安装

hexo是基于node.js且在博客部署的时候需要Git命令操作的，所以在安装hexo之前，需要先安装[node.js](https://nodejs.org/en/) 和[Git](https://git-scm.com/download/win) 。

完成上述两者的安装后，打开Git Bash，执行以下命令即可完成hexo的安装：

```
npm install -g hexo-cli
```

#### 2.2 hexo站点建立

- **1. 确定博客本地站点的位置：** 

  ~~选择一个位置，作为今后博客本地站点文件的存放位置， 在该位置建立名为`<your_githubname>.github.io` 的文件夹，`<your_githubname>.github.io` 也就是之前在Github上创建的仓库名~~ 

  由于我想要建立的是一个可以实现多机更新的博客，所以应该将hexo站点建立在**本地的Github仓库**中，这样在一台机器上配置好的主题和配置文件就可以随时上传到**Github远程仓库**中保存。如果希望在其他机器上建立hexo站点，在安装Git的基础上，可以先将之前保存在Github远程仓库中的hexo站点文件**克隆**到本地，形成Github本地仓库，然后在本地仓库中安装hexo即可使用，不需要重复配置。

  同时，由于我们的博客静态网页是发布在远程仓库的`master` 分支，即主分支上，所以我们需要在`master` 分支的基础上，**新建一个分支**，命名随意，我这里记为`hexo-files` ，用于保存推送上来的hexo站点文件，同时在Github中将`hexo-files` 分支设置为当前仓库的**默认分支**，这样可以方便将本地仓库的文件推送到远端仓库中。

  综上，在上文[创建Github远端新仓库](#create-repo) 以及新建并设置默认分支为`hexo-files` 的基础上，将这个新仓库**克隆**到本地，从而建立本地的Github仓库：

  ```
  # 切换到在你希望建立Github本地仓库的路径下
  git clone git@github.com:<your_githubname>/<your_githubname>.github.io.git
  ```

  上述命令会自动创建一个名为`<your_githubname>.github.io` 的文件夹，并将远程仓库中的文件都放到这个文件夹中。同时，可以发现文件夹中有一个名为`.git` 的文件夹，这个文件夹保存了有关Github本地仓库的信息以及本地仓库与远程仓库的连接信息，也就是能将`<your_githubname>.github.io` 文件夹中内容推送到远程仓库中的关键。

  **注** ：由于后续建站的时候，`hexo init` 命令初始化时，会将`.git` 文件夹覆盖，所以在这一步结束的时候，需要先将`.git` 文件夹保存起来，等待下一步`hexo init` 之后再复制进来。

- **2. 建站：**

  进入站点文件夹`<your_githubname>.github.io` 内，依次执行以下两条命令，完成hexo站点的建立：

  ```
  hexo init
  npm install
  ```

  **注** ：记得将`.git` 文件夹复制到`<your_githubname>.github.io` 中。

  此时，在Git Bash中进入`<your_githubname>.github.io` 目录下，可以看到当前所处Github分支是`hexo-files` ，如果不是，请返回上一步，设置好默认分支，再克隆远程仓库到本地。

  完成后，站点文件夹内的目录结构大致如下：

  ```
  .
  ├── .git
  ├── _config.yml
  ├── package.json
  ├── node_modules
  ├── scaffolds
  ├── source
  |   └── _posts
  └── themes
  ```

  各文件和文件夹的含义大致为：

  - `_config.yml` ，存放站点的配置信息，比如站点主题、站点名、站点菜单、站点链接以及第三方插件等等；

  - `package.json` ，存放插件信息，可以查看安装了哪些插件；

  - `node_modules` ，存放站点主题相关的样式文件；

  - `scaffolds` ，模板文件，生成静态网页时，hexo会将Markdown文件中`---` 以上的部分对比模板文件，然后生成新的静态网页文件；

  - `source` ，存放用户写好的Markdown文件，想要生成静态网页的Markdown文件写好之后，就可以放在`_post` 文件夹下。除了`_post` 文件夹，其他以`_` 开头的文件夹都会被忽略，而且，只有将Markdown文件放在`_post` 文件夹下，生成之后才会具有完整的主题样式。

    当然，如果想在网页上创建类似于`About` 的更多菜单选项，可以在`source` 文件夹下新建更多的文件夹，并在其中添加Markdown文件，当然，这些文件生成静态网页后不会具有完整主题样式，只能手动调整。

  - `themes` ，存放主题文件。

  **注** ：由于默认的NPM镜像有时候会很慢，在这时候可以将默认的源切换到淘宝镜像：

  ```
  npm config set registry "https://registry.npm.taobao.org"
  ```

#### 2.3 hexo站点配置

站点大部分的配置都可以通过`_config.yml` 文件实现，具体各个部分的含义可以参考[Hexo-配置](https://hexo.io/zh-cn/docs/configuration.html) 。

#### 2.4 hexo新内容生成

将一篇写好的Markdown文件放入`_post` 文件夹中（如果你还不知道怎么写，可以先参照和使用文件夹中已有的一个示例），关于如何写一篇Markdown文章，推荐使用[Typora](https://typora.io/) Markdown编辑器，关于Markdown语法，推荐[Typora For Markdown 语法](http://www.jianshu.com/p/092de536d948) ，当然也可以自行搜索。

将Markdown文件生成静态网页文件，执行以下命令之后，会产生一个`public` 文件夹，其中包含的就是所生成的静态网页文件和样式：

``` 
hexo generate
# 或者简化为 hexo g
```

启动hexo本地服务器，然后在浏览器中输入`http://localhost:4000/` ，就可以在本地预览新生成的网页：

```
hexo server
```

需要注意的是，单纯写一篇文章，生成之后只会在网页显示一部分，如果要阅读全文或者支持文章分类，必须在文章开头添加[Front-matter](https://hexo.io/zh-cn/docs/front-matter.html) ，如下所示：

```
title: Hexo
date: 2017/02/27 00:00:00
categories:
- Study
tags:
- Hexo
- Github
---
Hello World.
```

#### 2.5 hexo主题更换

hexo的主题很多，可以访问[Themes|Hexo](https://hexo.io/themes/) 选取。

以下将以[Noise](https://github.com/lotabout/hexo-theme-noise) 主题为例，讲解一下主题的更换。

- 找到主题的Github主页，主页上一般都会有安装的步骤和使用教程；
- 将主题的代码克隆到`/themes` 文件夹下，并安装主题和主题渲染器：

```
git clone https://github.com/lotabout/hexo-theme-noise themes/noise
npm install hexo-renderer-less --save
npm install hexo-renderer-jade --save
```

- 修改配置文档`_config.yml` ，将`theme` 的值由`landscape` 修改为`noise` 。
- 重新执行一遍生成操作即可预览:

```
hexo clean
hexo generate
hexo server
```

#### 2.6 小结

以上就完成了hexo的本地建站工作，如果只想在本地玩玩，到这里就完成了，日常的工作就是写写Markdown文章，然后在站点目录下依次执行以下命令即可：

```
hexo generate
hexo server
# visit http://localhost:4000/
```

PS. 顺便总结一下不问原因的、从零开始的快速建站方法：

```
# install node.js and git
npm install -g hexo-cli
# choose a path and clone <your_githubname>.github.io.git to local dir
git clone git@github.com:<your_githubname>/<your_githubname>.github.io.git
cd <your_githubname>.github.io
# 先将 .git 文件夹复制
hexo init
# 再将 .git 文件夹粘贴进来
npm install
# write a markdown file and put it into /source/_post/
hexo generate
hexo server
```

### 3 hexo部署到Github

---

- 安装插件：

```
npm install hexo-deployer-git --save
```

- 修改配置文件`_config.yml` ：

```
deploy:
  type: git
  repo:  https://github.com/<your_githubname>/<your_name>.github.io.git
  branch: master
# branch: master 意思是，部署的时候选择master分支发布
```

​	**注 ：配置文件中冒号后面一定要空一格** 

- 将hexo站点文件推送到远程仓库中，并将生成的静态网页部署到Github Pages：

```
git add .
git commit -m "注释"
git push origin hexo-files # 意思是，将本地仓库hexo-files分支的内容推送到名为origin的远程仓库的默认分支中
# 生成静态网页并部署到Github上
hexo generate
hexo deploy
# 或者简化  hexo g -d
```

​	有时候会跳出一个窗口，输入Github账号和密码即可。

- 登录`https://<your_githubname>.github.io` ，查看部署完成的博客内容。

**注** ：我还没有出现过部署不成功的情况，如果有，请查看[hexo部署后没动静，咋办](http://lowrank.science/Hexo-Github/) 。

### 4 hexo博客更新以及多机更新流程

---

- **Status1** ：如果上一次更新时使用的就是当前的机器，那么只需要将新完成的Markdown文件放入`_post` 文件夹内，按照情况执行以下命令即可：

```
# 本地预览时
hexo generate
hexo server
# 确定完成，需要推送&发布
hexo clean # 清理一下减少推送的数据量
git add .
git commit -m "注释"
git push origin hexo-files
hexo g -d
```

- **Status2** ：如果上一次更新时使用的不是当前机器，那么首先需要确定当前机器已经[建立了Github与本地的连接](#create-githublink) 并且已经安装了node.js和Git，之后根据情况执行以下命令：

```
# 创建本地仓库
git clone git@github.com:<your_githubname>/<your_githubname>.github.io.git
cd <your_githubname>.github.io
# 若当前机器是第一次用于编写发布博客，则需要安装hexo
npm install -g hexo-cli
# 否则，从这里开始
npm install hexo 
npm install
npm install hexo-deployer-git --save
# npm install hexo-renderer-less --save
# npm install hexo-renderer-jade --save
# 之后，就可以按照上述的 Status1 推送和发布博客了
```

### 5 总结

本文介绍了Hexo+Github Pages博客的建立方法和过程，以及如何实现多机更新Github博客的原理和方法。

总的来说，如果是第一次搭建Github博客，要学习的东西还是蛮多的，如果不明白其中的道理，加上网上零零散散不完全的教程，经常会绕到死胡同里。其中，我认为掌握Github**仓库**和**分支**的概念是很重要的，当然这也是学习Github中很重要的两个知识点。

在简单学习Github之后，我又萌生了将未完成的Markdown文件存储在Github远程仓库上的想法，这样未编写完成的Markdown文章就可以在不同机器上抓取并编写，然后推送共享，随时随地编写文章，直到完成。这对于我一个经常在实验室和宿舍两头跑的学生来说，是一件挺方便的事情。

之后的工作......应该是系统地学习Github，了解Github上更多有趣的东西吧，先放个链接吧，[如何使用Github](https://www.zhihu.com/question/20070065) 以及[Github上有趣的项目](https://www.zhihu.com/question/23498424)。