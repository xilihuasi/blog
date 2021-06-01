---
title: Sass用法介绍
date: 2017-02-28 23:20:10
tags: [css, sass]
categories: css
---

CSS 决定前端的颜值，但其落后的语法一直被诟病。CSS 本身是简单并有趣的，但当样式表变得庞大、复杂后，CSS 变得难以维护。直到 Sass,Less,Stylus 等预处理工具的出现，才使这一问题得到缓解。<!--more-->接下来就以 Sass 为例来介绍。

### Sass

什么是 Sass？GitHub 上是这样介绍的，Sass 让 CSS 再一次变得有趣。Sass 是 CSS 的拓展，添加了嵌套规则，变量，混合，选择器继承等特性。Sass 并不改变 CSS 的语法特性，只是通过命令行或 web 框架插件把 Sass 编译成标准格式化的 CSS。
Sass 有两种语法。一种全新的主要的语法是"SCSS"(Sass 3),是 CSS 的超集，可以使用 CSS 的一切语法。SCSS 文件采用`.scss`后缀。另一种较旧的语法采用缩进格式（跟 python 类似），取代 CSS 的大括号和分号，这种缩进语法文件采用`.sass`后缀。

```
//.scss
body{
    div{
        background-color:#f5f5f5;
    }
}

//.sass
body
  div
    background-color:#f5f5f5
```

由于不同编辑器对文档的缩进不尽统一，为了避免严格的格式要求导致报错以及书写的方便，建议采用`.scss`后缀名的文件。还没安装 Sass 的朋友可以在[该地址](http://www.sassmeister.com/)在线体验（采用 scss 文件语法）。

### Sass 语法

#### 变量

朋友，你是否为需求变更全局修改样式感到烦恼，是否因为字体宽高的数学计算感到力不从心，不用怕 Sass 让你重振雄风。Sass 允许使用变量来存储你想在样式表中重用的值，比如颜色，字号或者其他任意 CSS 值。Sass 通过`$`符号定义变量。

##### 普通变量

在 Sass 中，我们可以把反复用到的属性值或者经常修改的值定义成变量，在属性名或字符串中使用则需要写在#{}中。例如：

```
//代码块中，input.scss代表Sass输入文件，output.css代表Sass编译输出文件，后续小节中同理。
//input.scss
$font:17px;
$top:top;
$img-url:"/index/";
p{
    font-size:$font;
    margin-#{$top}:10px;
}
.site-nav{
    background-image:url(#{$img-url}icon.png);
}

//output.css
p{
    font-size:17px;
    margin-top:10px;
}
.site-nav{
    background-image:url(/index/icon.png);
}
```

##### 默认变量

默认变量的含义是，如果一个变量被声明赋值那就用它声明的值，否则就用默认值。Sass 设置默认变量只需在值后面加上!default 即可。

```
//input.scss
$font:18px !default;
p{
    font-size:$font;
}

//output.css
p{
    font-size:18px;
}
```

需要覆盖的时候只需要像普通变量一样重新赋值就可以了（注：有些教程里写必须在默认变量前声明覆盖，相信有心的小朋友已经发现是错误的了）。

```
//input.scss
$font:18px !default;
$font:20px;
p{
    font-size:$font;
}

//output.css
p{
    font-size:20px;
}
```

##### 全局变量和局部变量

在 Sass 中，定义在选择器中的变量为局部变量，反之则为全局变量。当在局部范围（选择器、函数、混合宏）内声明全局范围已经存在的变量时，局部变量的作用域仅限局部范围。

```
//input.scss
$color:#000;
p{
    color:$color;
}
.warn-txt{
    $color:red;
    color:$color;
}

//output.css
p{
    color:#000;
}
.warn-txt{
    color:red;
}
```

如果需要在局部范围内覆盖，需要在值后添加!global。**需要注意的是，局部范围声明覆盖，之前的值不会被覆盖**。

```
//input.scss
$color:#000;
a{
    color:$color;
}
.warn-txt{
    $color:red !global;
    color:$color;
}
p{
    color:$color;
}

//output.css
a{
    color:#000;
}
.warn-txt{
    color:red;
}
p{
    color:red;
}
```

##### 多值变量

多值变量类似 js 中的数组，声明时只需要将多个值用空格隔开即可。可以通过 length($color)来获取值的个数，nth($color,index)来获取 index 位置的值（index 范围为 1 到 length($color)）。

```
//input.scss
$color:#000 #3fc894 #ff6262;
p{
    color:nth($color,2);
    margin-top:length($color)px;
}

//output.css
p{
    color:#3fc894;
    margin-top:3px;
}
```

#### 嵌套

嵌套是一种可以让你优雅地写样式的特性，可以让你像写页面元素一样写样式，让代码更简洁，结构清晰、容易维护。嵌套可以在选择器和样式属性上使用。

```
//input.scss
.box{
    padding:10px;
    background:{
        size:cover;
        color:#ddd;
    }
    a{
        color:#000;
    }
    img{
        width:100px;
    }
}

//output.scss
.box {
    padding:10px;
    background-size:cover;
    background-color:#ddd;
}
.box a{
    color:#000;
}
.box img{
    width:100px;
}
```

需要注意的是，嵌套不宜过多以免生成的选择器过长。一些可以合并的属性如 border-color,border-width 尽量合并书写。

#### 混合器

Sass 中使用@mixin 声明混合器，可以传递参数，参数名以$符号开始，也可以给参数设置默认值。声明的@mixin 通过@include 调用。我们经常使用的单行文本溢出显示省略号的属性，可以通过混合器来避免代码重复书写问题。

##### 无参数混合器

```
//input.scss
@mixin ellipsis{
    width:100%;
    overflow:hidden;
    white-space:nowrap;
    text-overflow:ellipsis;
}
p{
    @include ellipsis;
}

//output.css
p{
    width:100%;
    overflow:hidden;
    white-space:nowrap;
    text-overflow:ellipsis;
}
```

##### 带参数的混合器

带参数的混合器，可以根据业务需求的不同来定义不同的值。例如定义一个动画延迟

```
//input.scss
@mixin animate-delay($second){
    -webkit-animation-delay: $second;
    animation-delay: $second;
}
.rect{
    @include animate-delay(2s);
}

//output.css
.rect{
    -webkit-animation-delay: 2s;
    animation-delay: 2s;
}
```

#### 导入

说起导入，CSS 其实也有@import 功能，只是很少被开发者使用。因为在一个 CSS 文件中导入的其他 CSS 文件，只有当执行到@import 规则时浏览器才会下载该文件，这会拖慢页面加载速度并且显示也会出现问题。Sass 中的@import 会在生成 CSS 文件的时候，把引入的文件与当前文件合并成同一文件，无需渲染时再加载。另外，导入文件中定义的变量，在当前文件中同样可用。在使用 Sass 中使用导入时，可以省略后缀名。

```
//aInput.scss
body{
    background-color:#f5f5f5;
}
//bInput.scss
@import "aInput";
p{
    color:#000;
}

//bOutput.css
body{
    background-color:#f5f5f5;
}
p{
    color:#000;
}
```

如果 aInput.scss 文件只是过渡文件，在编译 sass 目录的时候不想输出其对应的 CSS 文件，可在文件名前添加下划线，\_aInput.scss。这样 Sass 在编译的时候就会忽略该文件。

#### 继承

Sass 允许选择器继承另一个选择器的所有样式,并联合声明，使用关键字@extend。

```
//input.scss
.sign-box{
    border:solid 1px #ddd;
}
.info-box{
    @extend .sign-box;
    background-color:#f5f5f5;
}

//output.css
.sign-box, .info-box{
    border:solid 1px #ddd;
}
.info-box{
    background-color:#f5f5f5;
}
```

#### 注释

Sass 有两种注释方式，一种是标准的 CSS 注释方式`/* */`，另一种是双斜杠`//`单行注释（由于 CSS 不支持双斜杠注释，因此在输出时不会被转译出来）。

```
//input.scss
/* 段落字体颜色 */
//双斜杠注释测试
p{
    color:#000;
}

//output.css
@charset "UTF-8";
/* 段落字体颜色 */
p{
    color:#000;
}
```

#### 条件判断及循环

##### @if 判断

Sass 中的@if 语句和 js 中的 if 类似，可以单独使用也可配合@else 使用。

```
//input.scss
$color : green;
.nav-bar{
    @if $color == green {
        background-color:#3fc894;
    } @else if $color == red {
        background-color:#ff6262;
    } @else {
        background-color:#fff;
    }
}

//output.css
.nav-bar{
  background-color: #3fc894;
}
```

##### 三目运算

三目判断语法为：if($condition,$condition_true,$condition_false)。

```
//input.scss
$hasBorder:true;
.wrapper{
    border:solid if($hasBorder,1px,0px) #ddd;
}

//output.css
.wrapper {
  border: solid 0px #ddd;
}
```

##### for 循环

for 循环有两种形式，一种是@for $i from < begin> to < end>,另一种是@for $i from < begin> through < end>。二者的区别在于，to 不包含结束边界，而 through 包含。举个栗子：

```
//input.scss
/* 一般不建议这样命名*/
@for $i from 1 to 3{
    .item-#{$i} {
        width:10px * $i;
    }
}
@for $i from 1 through 3{
    .wrapper-#{$i}{
        width:10px * $i;
    }
}

//output.css
@charset "UTF-8";
/* 一般不建议这样命名*/
.item-1 {
  width: 10px;
}
.item-2 {
  width: 20px;
}
.wrapper-1 {
    width:10px;
}
.wrapper-2 {
    width:20px;
}
.wrapper-3 {
    width:30px;
}
```

##### each 循环

Sass 中的 each 语法为：@each $i in < list or map>,$i 可根据使用情景自定义名称。

###### 一维列表

```
//input.scss
$icon-list : sign, info, store;
@each $i in $icon-list {
    .#{$i}-icon {
        background-image:url('/imgs/#{$i}.png');
    }
}

//output.css
.sign-icon {
  background-image: url("/imgs/sign.png");
}
.info-icon {
  background-image: url("/imgs/info.png");
}
.store-icon {
  background-image: url("/imgs/store.png");
}
```

###### 多维列表

多维列表 each 循环至少需声明一个变量，执行时会按照列表维度顺序解析。

```
//input.scss
$icon-list:(sign,0px 0px,default),(info,0px -50px,pointer),(store,-50px 0px,pointer);
@each $name, $position ,$cursor in $icon-list{
    .#{$name}-icon{
        background:url('/imgs/#{$name}.png') no-repeat;
        background-position:$position;
        cursor:$cursor;
    }
}

//output.css
.sign-icon {
  background: url("/imgs/sign.png") no-repeat;
  background-position: 0px 0px;
  cursor: default;
}
.info-icon {
  background: url("/imgs/info.png") no-repeat;
  background-position: 0px -50px;
  cursor: pointer;
}
.store-icon {
  background: url("/imgs/store.png") no-repeat;
  background-position: -50px 0px;
  cursor: pointer;
}
```

###### Map

```
//input.scss
$head:(.title:20px,.container:17px);
@each $key, $value in $head{
    .#{$key}{
        font-size:$value;
    }
}

//output.css
.title {
  font-size: 20px;
}
.container {
  font-size: 17px;
}
```

### Sass 安装

由于本人使用的 Windows 系统，只尝试过在该系统下安装。为了保证各个系统的用户都能顺利安装，这里就提供个[传送门](http://sass-lang.com/install)，关于安装 Sass，有它就够了！

### 结语

可以看到 Sass 的这些特性解决了很多之前写 CSS 的痛点，显著提升了开发效率和 CSS 的可维护性。这里只介绍了一些基础用法，关于 Sass 更深入的用法可以继续关注我的后续文章，或者自己动手研究。

### 参考

- http://sass-lang.com/
- http://www.w3cplus.com/sassguide/
- http://riny.net/2014/sass-guide/
