---
title: "如何使用 hugo 搭建个人博客"
date: 2019-09-20T19:17:26+08:00
draft: false
---
  本文默认大家都会在github上部署自己的博客，在此基础上，阐述如何使用hugo搭建一个自己的博客平台，hugo是用go语言实现的一个博客生成器。用它不需要有多好的基础，照着抄代码就行。本文于官方教程的区别是，详细，够详细！基本能避免实现过程中的一些暗坑。
### 第一步

* 下载 hugo 安装包，解压到 D 盘 Software 目录下，（随便你解压到哪，只不过，后面配置环境变量添加 PATH 时记得把你所解压到的文件地址加上就行了），设置环境变量，在 PATH 中添加 D:\Software\hugo,(一定要有 hugo )。到此，之后都不用管这个安装包以及 hugo.exe 了。
    
* 下载地址一般到官网 [hugo下载](https://gohugo.io/getting-started/installing/) 选择你需要的版本。

* 搞定之后可以打开命令行工具运行
```
hugo version
```
来检查你是否下载安装成功，如果执行上面命令后显示 hugo 的版本号及一些事件日期信息，则代表成功。

### 第二步

* 进入官网 [hugo](https://gohugo.io) 官网，点击 Quick Start 你便可得到一份官方的指南，指南上的第一步上面已经做完了。
* 接下来可以按照它的提示照抄代码在你的终端运行。
* 进入 D 盘创建一个博客站点的存放文件如 codes ,
```
cd codes
hugo new site quickstart
```
//此处的 quickstart 必须更换为你的 github 用户名.github.io-creator (比如我的是 wuxiaod.github.io-creator,其中 `-creator` 代表创建的只是一个生成器，具体为何，后面会讲，其实就是一个备份)
* 运行成功后会在 codes 里面生成一个 `wuxiaod.github.io-creator` 的文件夹，里面有几个初始目录文件如 content、themes 等。
* 你可以
 ```
 cd wuxiaod.github.io-creator
 ls -a
 ``` 
* 查看是否有 content 、 themes 等这些目录文件生成。也可以直接通过 VScode 来查看。

### 第三步

* 对应官网教程 Step 3，主要是给你的博客添加一个主题，简单点，先添加一个默认的主题 ananke 吧，等熟悉了再来换。方法还是抄代码
```
cd wuxiaod.github.io-creator //如果已经在此路径，这一句代码不抄
git init   //初始化，会生成 .git 的隐藏文件
git submodule add https://github.com/budparr/gohugo-theme-ananke.git themes/ananke // 把ananke主题添加到 themes 中，这一过程可能会花费一点时间，但也不一定，耐心等待。
echo 'theme = "ananke"' >> config.toml //修改配置文件
```
    
### 第四步

#### 写一个测试文件
```
hugo new posts/自定义文件名.md
```
* 如果报错，一般是该主题有 bug，想改可以通过 VScode 编辑，一般都是些小错误，比如引号没加之类的。
* 运行成功后，会在当前目录下的 /content/posts 下创建你的自定义文件名.md 文档，打开它，你就可以编辑你的测试内容了，这就是你的第一篇博客内容了。至于怎么打开编辑，可以使用 VScode 也可以使用 vi 编辑。
```
    ---
    title: "My First Post"
    date: 2019-03-26T08:47:11+01:00
    draft: true  //写完你的博客内容后，就把 draft 的值改为 false
    ---
     ## 此处填写你的博客内容
     ## 。。。。。。。
     ## 写完保存，下一步
```
### 第五步

* 开一个服务器，你就可以在本地预览你的博客了，
* 执行：（记住，你此刻的路径还是在你博客存放仓库路径，也就是前面设置的 github用户名.github.io-creator，下面简称根目录）
```
hugo server -D
```
* 运行成功后会给你一个 http://localhost:1313/ 的网址，打开你就可以预览到你的博客网站了。

### 第六步

#### 配置一下你的 config.toml
* 在根目录下找到 config.toml 文件，打开

> baseURL = "https://example.org/"   // 先不改

> languageCode = "en-us" // en-us 可以改为 zh-Hans

> title = "My New Hugo Site"   // 改成你的博客网站名吧

> theme = "ananke"  //主题等你想换的时候再来换成你想要的主题吧


### 第七步

* 新建一个终端进入 根目录（github用户名.github.io-creator）
```
 hugo     // 运行成功后会在根目录下生成一个 public 文件。
```
* 到此官方教程结束，可以看出，官方教程虽然简洁，但没有替新手考虑完全，因为新手根本不会把 public 文件放到网上，也可能有些人会，但其中还是有一些坑的。但它默认我们都会。*

### 第八步

* 在根目录下（github用户名.github.io-creator）新建一个 `.gitignore`文件，在这个文件里写上 `/public/`,意思是当前仓库不要存储 public ,忽略 public ，因为它要自成一个仓库
* 接下来
```
cd public
git init
git add .
git  commit -m "第一次部署"
```
* 开始上传，到 github 新建一个仓库,仓库名为 你的github用户名.github.io 。直接点击创建。然后复制 `git remote add origin git@github.com:你的github用户名/你的github用户名.github.io.git` 和 `git push -u origin master` 到终端运行
* 运行成功后刷新你的仓库，就可以去 settings 中的 GitHub Pages 栏生成你的预览网址了，打开这个网址就是你的个人博客了。

**至此，个人博客已经搭建完成，剩下来的就是详细配置（换主题之类的）和写博客了。**

### 第九步

#### 备份，以防万一
* 新建 github 仓库 ，仓库名为 github用户名.github.io-creator
* 复制代码`git remote add origin git@github.com:你的github用户名/你的github用户名.github.io-creator.git`到终端运行(在github用户名.github.io-creator路径下)
* `git add .`//如果有提示信息，最好是根据提示操作一下，不要形成 git 里面还有 git 
* `git commit -m "xxx"`
* `git push -u origin master`// 上传
* 刷新github仓库，你会发现这是一个没有 public 文件的仓库，因为 public 已经被我们放到另一个仓库去了（wuxiaod.github.io），这是一个博客生成仓库，以后如果万一不小心把本地的硬盘弄坏，你也可以直接在此仓库 clone 然后 hugo 在此生成 public

### 第十步

*这一步可做可不做*

  做什么呢？给你的博客网站配置你自己购买的域名
很简单，不过你先要自己买一个域名，可以上外网的 namesilo,也可以到国内的阿里云购买域名。其他可能还有一些免费的域名网站，你也可以自己找一下，然后根据教程绑定一下就行了。如果需要，我再来详细阐述吧。写累了。。。

### 总结

* 每次写博客可以通过命令行在根目录（github用户名.github.io-creator）输入 `hugo new posts/xxx.md`来创建文件，也可以通过直接在根目录下的 /content/posts 文件夹中创建来完成。
* 写完博客后，记得把 draft 的值改为 false，并保存
* 写完博客后，要提交到远程仓库的话必须进行如下操作做：
1. `cd 根目录(github用户名.github.io-creator)`
2. `hugo`
3. `cd public`    //重要步骤
4. `git add .`
5. `git commit -v`
6. `git pull`
7. `git push`
    
finished