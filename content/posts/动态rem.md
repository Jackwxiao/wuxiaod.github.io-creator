---
title: "手机专用之动态 rem"
date: 2019-10-14T09:26:04+08:00
draft: false
---

#### 手机专用的自适应方案

rem 是什么？

  em:  一个 m 的宽度, 一个汉字的宽度
  rem:  root em  根元素的 font-size 大小
  
  网页上的默认字体大小为 16px
  chrome 最小的字体大小为 12px
  
  em 是 元素自己的font-size 大小

如： 自己 font-size: 16px;
则 1em = =font-size

>百分比布局虽然可以适应各屏幕大小，但无法设置高度。还有很多其他不确定性

缩放

  **动态rem：**

* 首先： 让html 的font-size 和 页面宽度一样, 即 1rem = 页面宽度
1 rem == html font-size == viewport width
使用 JS 动态调整 REM

```
<meta name="viewport" content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">

<script>
    var  pageWidth = window.innerWidth
    document.write('<style>html{ font-size : ' + pageWidth  + 'px;}</style>')

<script>

```

* 其次：除了字体，所有单位都用 rem 来设置

微调：
加meta:vp 
尽量不要用小数 ，所以，令 html 的font-size 页面宽度的 1/10（pagewidth/10）// 根据实际实际稿计算
当某个东西很小时，如1px 直接使用1px,可以混用。

那么问题来了，这样的话，每次都要去算rem。。。。

#### 用sass/scss自动转换
px ->rem

1. 安装 sass
 先设置淘宝源
 `npm config set registry https://registry.npm.taobao.org/`
 编辑 ~/.bashrc 文件
 ` export SASS_BINARY_SITE="https://npm.taobao.org/mirrors"  `
 终于可以npm install 了
 ` npm i -g node-sass `

2. 使用 
`node-sass -wr scss -o css` 
 监听 scss 转为 css

完整版：

在 SCSS 里使用 PX2REM

* npm config set registry https://registry.npm.taobao.org/
* touch ~/.bashrc
* echo 'export SASS_BINARY_SITE="https://npm.taobao.org/mirrors/node-sass"' >> ~/.bashrc
* source ~/.bashrc
* npm i -g node-sass
* mkdir ~/Desktop/scss-demo
* cd ~/Desktop/scss-demo
* mkdir scss css
* touch scss/style.scss
* start scss/style.scss
* node-sass -wr scss -o css


编辑 scss 文件就会自动得到 css 文件
在 scss 文件里添加

```
@function px( $px ){
  @return $px/$designWidth*10 + rem;
}

$designWidth : 640; // 640 是设计稿的宽度，你要根据设计稿的宽度填写。如果设计师的设计稿宽度不统一，就杀死设计师，换个新的。。。

.child{
  width: px(320);
  height: px(160);
  margin: px(40) px(40);
  border: 1px solid red;
  float: left;
  font-size: 1.2em;
}
```
即可实现 px 自动变 rem

！！！ 
学会用命令行
学英语
看文档