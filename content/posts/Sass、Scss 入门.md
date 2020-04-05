---
title: "Sass、Scss入门"
date: 2019-08-26T11:26:04+08:00
draft: false
---

### Scss入门：

>Sass（英文全称：Syntactically Awesome Stylesheets）是一个最初由Hampton Catlin设计并由Natalie Weizenbaum开发的类 CSS 语言。
发行时间：2007年；稳定版本：2016年3月28日；
Sass 十分简洁，语法中几乎不含括号。
后来前端工程师表示不含括号看不懂，于是 Sass 的开发者又提供了 Scss，含括号。
弱弱的前端工程师终于表示能看懂了。Sass的官方解释器是开源的并且用Ruby语言写成，但是也有用PHP、C语言、Java等实现的版本（C语言版本叫做llibSass，Java语言版本叫做JSass）。SassScript提供以下功能：变量、嵌套、混入（Mixin）、选择器继承等。


安装 parcel, 下面这条命令会自动安装node.sass

`npm i -D parcel`

运行：

`npx parcel index.html`

实例：

HTML

```

<!DOCTYPE html>
<html lang="zh">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>scss</title>
    <link rel="stylesheet" href="./a.scss">
</head>
<body>
    <h1>一起学 Scss 吧</h1>
    <div class="nav1">
        <ul>
            <li>1</li>
            <li>2</li>
            <li>3</li>
        </ul>
    </div>
    <div class="nav2">
        <ul>
            <li>1</li>
            <li>2</li>
            <li>3</li>
        </ul>
    </div>
    <div class="demo">
        
    </div>
</body>
</html>

```
 SCSS：
 ```
 
// 嵌套：
body{
    h1{
    color: green;
}}
.nav{
    border: 1px solid grey;
    >ul{
        background: white;
        >li{
            border: 1px solid red;
        }
    }
}

// ---------------------++++++--------------
// 变量 ：

$grey: #666;
$border-width: 1px;
.nav{
    border: $border-width solid $grey;
    >ul{
        background: white;
        >li{
            border: $border-width solid red;
        }
    }
}

// ---------------------++++++----------------
// mixin :

@mixin debug {
    border: 1px solid red;
}
.nav1{
    //border: 1px solid red; 等同于下面一句
    @include debug;
    >ul{
        background: white;
        >li{
            border: 1px solid red;
        }
    }
}
// mixin 还支持参数
//@mixin debug($border-color: red) { //如果传了其他色就显示其他色，默认为red
//     border: 1px solid $border-color;
// }
// .nav1{
//     //border: 1px solid red; 等同于下面一句
//     @include debug; //默认red
//     >ul{
//         background: white;
//         >li{
//             @include debug(green); //传的green
//         }
//     }
// }

//-----------------------+=++=====-------------

// placeholder ,解析成css 后不会把效果代码写两遍（用mixin 会生效但代码会重复），而是把选择器合并如 .nav2>ul>li,.demp{...}
// 体现在 效果复用 ，demo 和 nav2 的阴影效果是一样的

%box{
    box-shadow: 0 0 3px grey;
    margin: 10px;
    background: white;
    border-radius: 4px;
}
.nav2{
    >ul{
        background: white;
        >li{
           @extend %box;
        }
    }
}
.demo{
    height:100px;
    @extend %box;
}

```

![代码目录结构：](https://i.loli.net/2019/12/12/IyVrv7uqKWoFNge.png)