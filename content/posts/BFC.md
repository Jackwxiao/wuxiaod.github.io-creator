---
title: "BFC"
date: 2019-10-25T21:26:04+08:00
draft: false
---

#### BFC （块级格式化上下文）block formatting context
 
 当子元素 浮动 时，父容器一般是包不住子元素的，所以要给父容器设置BFC，它能保证不管子元素怎么闹腾，都不影响外部。
 
 如何设置？
 * 浮动（float 不是 none）
 * 绝对定位（ position 为 absolute 或fixed）
 * 内联块（元素具有display:  inline-block）
 * 表格单元格（display:table-cell）
 * 表格标题（display:table-caption ）
 * 具有 overflow 且值不是 visible 的元素
 * display: flow-root

在BFC 中，盒在竖直方向一个接一个的放置，从包含块的顶部开始，两兄弟之间的竖直margin 会合并

**功能1: 爸爸管儿子（但有些元素他就是要出去一部分，包住一部分，那就清除浮动或者加 display： flow-root）**

用BFC 包住浮动元素（不是清除浮动）
```
      <div class ="baba">
        <div class="erzi">
           
        </div>
     </div>
     
    .baba{
  border: 10px solid red;
  display: flow-root;
  
}
    .erzi{
     float: left;
    height: 100px;
    width: 200px;
     border: 5px solid green;
}
```      
**功能2：兄弟之间划清界限（现在可以用flex 布局）**

用float + div 做左右自适应布局


 ```
        <div class="gege"></div>
        <div class ="didi"></div>
        
        .gege{
            width: 100px;
            min-height: 600px;
            border: 1px solid red;
            float: left;
            }
            .didi{
                min-height:600px;
                 border: 5px solid green;
                overflow: auto;
                //display: flow root ；更好，但不一定兼容ie 新出来的
                
             // float + overflow: auto 左右布局就出来了
 }
 
```

不要用 BFC 清除浮动,BFC 于现在来讲就是”多此一举“《自我理解》


#### CSS规范中对 BFC 的描述

>9.4.1 块格式化上下文浮动，绝对定位元素，非块盒的块容器（例如，inline-blocks，table-cells和table-captions）和'overflow'不为'visible'的块盒会为它们的内容建立一个新的块格式化上下文
>在一个块格式化上下文中，盒在竖直方向一个接一个地放置，从包含块的顶部开始。两个兄弟盒之间的竖直距离由'margin'属性决定。同一个块格式化上下文中的相邻块级盒之间的竖直margin会合并
>在一个块格式化上下文中，每个盒的left外边（left outer edge）挨着包含块的left边（对于从右向左的格式化，right边挨着）。即使存在浮动（尽管一个盒的行盒可能会因为浮动收缩），这也成立。除非该盒建立了一个新的块格式化上下文（这种情况下，该盒自身可能会因为浮动变窄）

#### MDN 对 BFC 的描述

>一个块格式化上下文（block formatting context） 是Web页面的可视化CSS渲染出的一部分。它是块级盒布局出现的区域，也是浮动层元素进行交互的区域。

>一个块格式化上下文由以下之一创建：
* 根元素或其它包含它的元素
* 浮动元素 (元素的 float 不是 none)
* 绝对定位元素 (元素具有 position 为 absolute 或 fixed)
* 内联块 (元素具有 display: inline-block)
* 表格单元格 (元素具有 display: table-cell，HTML表格单元格默认属性)
* 表格标题 (元素具有 display: table-caption, HTML表格标题默认属性)
* 具有overflow 且值不是 visible 的块元素，display: flow-rootcolumn-span: all 应当总是会创建一个新的格式化上下文，即便具有 column-span: all 的元素并不被包裹在一个多列容器中。

>一个块格式化上下文包括创建它的元素内部所有内容，除了被包含于创建新的块级格式化上下文的后代元素内的元素。

>块格式化上下文对于定位 (参见 float) 与清除浮动 (参见 clear) 很重要。定位和清除浮动的样式规则只适用于处于同一块格式化上下文内的元素。
>浮动不会影响其它块格式化上下文中元素的布局，并且清除浮动只能清除同一块格式化上下文中在它前面的元素的浮动。
>
#### 张鑫旭对 BFC 的描述

>http://www.zhangxinxu.com/wordpress/2015/02/css-deep-understand-flow-bfc-column-two-auto-layout/

>BFC全称”Block Formatting Context”, 中文为“块级格式化上下文”。啪啦啪啦特性什么的，一言难尽，大家可以自行去查找，我这里不详述，免得乱了主次，总之，记住这么一句话：BFC元素特性表现原则就是，内部子元素再怎么翻江倒海，翻云覆雨都不会影响外部的元素。所以，避免margin穿透啊，清除浮动什么的也好理解了。