---
title: 前端工具之gulp
date: 2019-07-29 16:14:21
categories: 
- 前端
tags: 
- gulp
---
### 前言

web项目需要对 JS/CSS 合并、压缩，图片压缩，这样能够： 

1. 减小了文件的体积 
2. 减小了网络传输量和带宽占用 
3. 减小了服务器的处理的压力 
4. 提高了页面的渲染显示的速度

工具 gulp 可以快速帮我们完成这些“重复工作”，gulp 是基于 node 实现 Web 前端自动化开发的工具，利用它能够极大的提高开发效率。

<!-- more -->

### 安装 node 、gulp

gulp 是基于 node 实现的，那么我们就需要先安装 node。

#### 安装 node

Node 是一个基于Chrome JavaScript V8引擎建立的一个平台，可以利用它实现 Web服务，做类似PHP的事。

打开 [官网](https://nodejs.org/) 下载安装 node。

Window安装直接下一步下一步式安装。安装好后WIN+R输入cmd调出DOS窗口，输入node -v 看是能显示版本号，如果没有则查看系统变量Path，把path配置上去（此电脑 -> 右键属性 -> 高级 -> 环境变量）（每个软件的安装都需要配置环境变量，只不过有些是安装工具帮助我们自动配置了）（注意打开终端使用管理员身份）。 

#### 安装 gulp

首先确保已经正确安装了nodejs环境。然后以全局方式安装gulp：

```
npm install -g gulp
```
全局安装gulp后，还需要在每个要使用gulp的项目中都单独安装一次。把目录切换到你的项目文件夹中，然后在命令行中执行：

```
npm install gulp
```
如果想在安装的时候把gulp写进项目package.json文件的依赖中，则可以加上--save-dev：

```
npm install --save-dev gulp
```
这样就完成了gulp的安装。至于为什么在全局安装gulp后，还需要在项目中本地安装一次，有兴趣的可以看下stackoverflow上有人做出的回答：[why-do-we-need-to-install-gulp-globally-and-locally](http://stackoverflow.com/questions/22115400/why-do-we-need-to-install-gulp-globally-and-locally)、[what-is-the-point-of-double-install-in-gulp](http://stackoverflow.com/questions/25713618/what-is-the-point-of-double-install-in-gulp)。大体就是为了版本的灵活性，但如果没理解那也不必太去纠结这个问题，只需要知道通常我们是要这样做就行了。

安装完成后可在命令行输入 gulp -v 以确认安装成功。

### gulp的实施与使用

#### 1. 创建工作目录与发布目录。 

**工作目录**：即没有经过压缩合并等处理的文件存放目录。 

**发布目录**：即项目发布所引用的文件目录地址，这里的文件是经过gulp压缩合并等处理后生成的文件，处理过后的名字可以自定义，也可以不处理即与未处理文件名相同。

#### 2. 创建并编写 gulpfile.js 文件

gulp 的所有配置代码都写在 gulpfile.js 文件。
创建一个 gulpfile.js 文件，在里面编写代码。

此时我们的目录结构是这样子的：

```
├── gulpfile.js
├── node_modules
│ └── gulp
└── package.json
```
#### 3. 运行gulp任务

要运行gulp任务，只需切换到存放gulpfile.js文件的目录(windows平台请使用cmd或者Power Shell等工具)，然后在命令行中执行gulp命令就行了，gulp后面可以加上要执行的任务名，例如gulp task1，如果没有指定任务名，则会执行任务名为default的默认任务。

>民生银行网银客户端项目举例说明：
>
>修改 gulpfile.js 文件
>```
>mix.scripts([
>   'downloadPage.js' // 开发js文件
>], 'dist/js/downloadPage.min.js');//压缩js文件
>```
>
>每次需要压缩文件的时候，进入到gulpfile.js文件的同级目录中，依次执行以下命令：
>   1. npm install
>   2. npm install toastr
>   3. gulp --production

