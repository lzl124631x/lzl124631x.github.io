---
layout: post
title:  "bower初接触"
date:   2015-11-28 00:00:00 +0800
tags: bower
---

之前从Steve Sanderson的博文[Architecting large Single Page Applications with Knockout.js](http://blog.stevensanderson.com/2014/06/11/architecting-large-single-page-applications-with-knockout-js/)中学习了用[Yeoman](http://yeoman.io/)创建Knockout的Single Page Application(SPA)的方法. Yeoman这个Scaffolding工具使用Bower安装各种包(Package). 在生成的SPA的根目录中可以看到.bowerrc文件, 该文件用来对bower进行设置, 如我这里可以看到简单的

```
{
  "directory": "src/bower_modules"
}
```

它设置了将所有通过bower安装的Package都安装到"src/bower_modules"目录下. 更详细的设置参见[Bower Configuration](http://bower.io/docs/config/).

SPA根目录中还有一个重要的bower相关的文件 -- bower.json. 该文件记录了通过bower安装的所有Package的信息. 如我这里可以看到

```
{
  "name": "SPA",
  "version": "0.0.0",
  "private": true,
  "dependencies": {
    "components-bootstrap": "~3.1.1",
    "crossroads": "~0.12.0",
    "hasher": "~1.2.0",
    "requirejs": "~2.1.11",
    "requirejs-text": "~2.0.10",
    "knockout": "~3.3.0-alpha",
    "knockout-projections": "~1.1.0"
  },
  "resolutions": {
    "knockout": "~3.3.0-alpha"
  }
}

```

当我想安装新的Package的时候就可以通过bower安装. 不过首先要安装好bower. 安装bower可以用Visual Studio自带的NuGet, 不过下面介绍手动安装的方式.

1. 安装Git.
访问[Git官网](https://git-scm.com/), 下载安装文件, 如Git-2.6.3-64-bit.exe, 运行安装. 安装完后, 点击任意文件夹右键菜单中会出现几个和Git相关的按键, 如Git Bash是打开Git的命令行窗口. 在该窗口中输入`git --version`可以看到相应版本号.
2. 安装Node.js.
访问[Node.js官网](https://nodejs.org/en/), 下载安装文件, 如node-v5.1.0-x64.msi, 运行安装. 现在打开Git Bash输入`node -v`和`npm -v`就可以看到相应的版本号, 说明安装成功.
3. 安装bower.
`npm install -g bower`
4. 通过bower安装Package.
    1. 在SPA目录的Git Bash中运行`bower install`后, bower会将bower.json中定义好的Packages安装到.bowerrc指定的目录下.
    2. 要安装新的包时, 如安装fastclick.js, 只需要运行`bower install fastclick -S`即可. 其中`-S`指令帮你把新安装的包的信息更新到bower.json, 如果不带这个参数则bower.json不变.