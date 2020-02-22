# sass笔记总结

### 安装sass

为了在我们的电脑上能够使用sass，我们首先需要安装ruby（ 一种简单快捷的面向对象（面向对象程序设计）脚本语言 ），通过ruby中所带的gem去安装sass。

ruby在官网即可下载，但下载速度极慢，请耐心等待，也可以私信笔者以分享网盘。（以window为例）ruby下载完成后我们，我们可以再终端输入`ruby -v`来查看版本信息。

由于gem默认下载太慢，我们修改为淘宝镜像下载，`gem -d https://gems.ruby-china.com/`

然后我们通过gem来下载，`gem install sass`

以上用到的命令，总结如下

```shell
ruby -v  ##查看ruby的版本
gem -v  ##查看gem的版本

gem souces --remove https://rubygems.org/  ##删除默认的下载路径
gem souces -d https://gems.ruby-china.com/  ##添加淘宝的下载路径
gem souces -l ##查看目前添加的下载路径
gem updata  ##更新新版本的ruby内容

gem install sass  ##下载sass
gem install sass --version=3.3  ##下载3.3版本的sass
gem uninstall sass --version=3.3  ##删除3.3版本的sass
gem list  ##查看所有下载的ruby包

sass -v   ##查看sass版本信息

```

### 使用sass

在文件夹下创建一个*.sass文件，通过`sass *.sass *.css`可以将其转变为css文件。

当然我们在真正项目开发中，我们不会直接使用sass，而是通过工具来创建并使用sass，这里我们介绍一个工具叫compass。

通过`gem install compass`来下载compass。我们通过`compass create project-name`来创建一个项目目录。 目录下有三个文件或目录，sass文件为你书写sass文件的地方，stylesheets文件为生成的css文件的地方，config.rb是一些配置信息。

进入sass目录，我们将在screen.sass文件中进行编码。

在此之间，我们在项目目录下的终端执行`compass watch`来实时监听文件变化，当你在sass目录下书写代码时，代码会自动转为css样式并存储到stylesheets文件中。

scss与sass作用相似，而且可以相互转换，为了方便，我们一下统一以scss的语法来教学。

以上用到的命令，总结如下

```shell
sass a.sass a.css  ##将a.sass文件转为a.css文件并输出
sass-convert  a.sass a.scss  ##相互转化sass格式与scss格式

compass create project_name  ##常见compass项目目录
compass watch  ##监听项目目录
```



### scss语法与使用

###### scss基本用法

* 注释：/**/与//皆可以

* 变量：$headline-ff 

* 引入文件：import “file_path”  

* if语句：@if 条件 {执行体}

* 函数：@functions name(parms) {}; @mixin name(parms) {}

* 常用方法：@at-root 

  

 ###### 代码展示

_variables.scss文件

加了下划线，声明为局部文件，在生成css文件时会忽略此文件

```scss
//演示声明变量
$headline-ff : Arial;
```

_mixin.scss文件

```scss
//演示函数定义
@mixin col($width:30%) {
    width:$width;
}
@mixin col-6() {//函数中有类名的，在调用后会直接生成里面的类
    .col-6{
        width:30%;
    }
}
@mixin col-sm($width:50%) {
    @if type-of($width)!=number{
        @error "输入的不是数值类型"
    }
    @if not unitless($width){
        @if unit($width)!="%" {
            @error "输入的单位不是百分值";
        }
    } @else {
      @warn "输入得不是百分值";
      $width:(percentage($width)/100);
    }
    @media(min-width:768px){
        width:$width;
        float:left;
    } 
}
```



screen.scss文件

宿主文件

```scss
//文件简单文档
/**
*CONTENTS
*
*SETTINGS
*variables-----------变量几种存储文件
*mixin---------------mixin几种存储文件
*
*TOOLS
*
*COMPONENTS
*reset----------------component内置浏览器重置样式文件
*
*BUSINESS
*
*BASE
*screen.scss-----------针对当前站点主页的样式修饰
*/

/*!
*加一个叹号可以不被压缩
*/

//引入文件
@import "variables","mixin";
//演示变量的使用
.main{
    font-family:$headline-ff;
}
//样式可以嵌套,在main-sec中headline的样式
.headline{
    font-family:$headline-ff;
}
.main-sec{
    .headline{
        font-family:$headline-ee;
        color:blue;
    }
    &:hover{
        color:red;
    }
}

//内置函数的使用
p{
    color:hsl(270,100%,50%)//在转化css文件时，转化为#加十六进制的格式，解决了有些浏览器不支持hsl格式的问题
}
//使用自定义函数@include
@include col-6()
div{
    @include col(70%);
}
//继承代码块@extend
.err{
    color:red;
}
.serious-err{
    @extend .err;
    border:1px blue;
}

```



###### 注意点

*@import*

此处的import是将css中的import重新定义的，与其有所差异

css中的@import中的弊端有如下：

1、必须放在最前面否则不起作用

2、对性能不理，例如A引入了B，只有当加载A时，读取到了import时，才会去读取B，这样浏览器处于堵塞状态，延长了渲染时间。

scss中，@import可以写在任何地方，且会在转化成css文件是将引入文件合并到主文件中。

但是在scss中仍可以使用css3定义的import，在以下情况时会选择使用c3的import：

1、当@import后面跟的文件名以css结尾时

2、当@import后面跟的是http：//开头的时候

3、当@import后面跟的是一个url（）函数的时候

4、当@import后面带有media queries的时候

*注释*

以`/**/`注释的还会显示在生成的css文件中 而`//`注释的不会



*&:hover*

```scss
a{
    &:hover{
        color:red;
    }
}
```

如果不加&，转化完是为a所有字标签添加了这个伪类选择器。

*函数*

scss分为两种函数：

1、functions，与代码块无关的函数，多是自己的内置函数

2、mixin，可重用的代码块，一部分通过@include的方式调用，一部分通过@extend的方式调用

*@extend*

1、extend不可以继承选择器序列

```scss
//该写法为错
.A .B{
    color:red
}
.C{
    @extend .A .B
}
```



2、用%制作只用于继承的代码块

```scss
%err{
    color:red;
}
.serious-err{
    @extend .err;
    border:1px blue;
}
```

这样在转化为css后没有.err这个类及其样式

*media*

scss中的media与css中的media也有一同，scss中的media可以内嵌在css规则中在生成css的时候，media才会提至最外层

*输出格式的样式*

设置config.rb配置文件

`output_style=:expanded`默认输出演示

`output_style=:compressed`输出压缩样式

*at-root*

scss中有很强大的嵌套功能，例如

```scss
.a{
	.b{}
}
```

而他在转化为css文件时的输出为

```css
.a .b{}
```

浏览器解析css的方式是从右向左的，拿上述例子为例，浏览器会先去找到类名为b的元素，然后一层一层遍历看看有没有叫c的父元素，这样使得性能变差。

所以，最佳的办法为如下

```scss
.a{
	@at-root{
		.b{}
	}
}
```

这样转化为的css文件为

```css
.a{}
.b{}
```

### compass简介

compass可以理解为，他为你预先定义好了很多样式，你可以直接通过函数调用的方式来去设置一个类的样式。

compass在sass基础上，构建了一下几大模块。

`reset`浏览器样式重置模块

`Layout`提供页面布局样式	

`css3`跨浏览器的css3能力

`Helpers`与`Utilities`不同的是他的函数多为mixin函数

`Utilities`其他

`Typography`主要修饰文本样式，垂直韵律

`browser`设置默认支持哪个浏览器甚至版本

当然，除了这 内置的几大模块外，我们还可以下载第三方模块，例如normalize

```shell
gem install compass-normalize
```

下载完成后，我们可以直接在config文件中配置引入。例如

```
require 'compass-normalize'
```

然后再scss文件中引入使用

```scss
@import 'normalize'
```

每个模块都用很多用法，在这里不一一列举，可以通过去查文档去使用

