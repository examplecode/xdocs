---
title: 为X浏览器撰写用户脚本
date: 2019-07-26 12:06:18
tags: 
categories: tutorials
lang: zh-CN

---


## 什么是用户脚本?

用户脚本是一段用户自定义的javascript代码，它可以在页面加载的过程中自动运行。 通过用户脚本可以达到对原始网页功能增强或则解除一些网页的限制的目的。 

用户脚本具有小巧，灵活，易用等特点，可以非常方便的扩展浏览器功能，不夸张的说只有你想不到没有做不到。一些常规的浏览器功能都可以通过用户脚本的形式实现，比如阅读模式，智能预读，夜间模式等。



## 第一个用户脚本

只要您懂一些javascript代码，就可以为浏览器撰写用户脚本，下面是一段最简单的一个用户脚本.

``` javascript
alert('hello');
```


为了兼容[bookmarklet](https://baike.baidu.com/item/Bookmarklet/610461?fr=aladdin) 下面的写法也可以被X浏览器所识别。

``` javascript
javascript:alert('hello');
```


你可以通过 浏览器设置》浏览器脚本》 添加脚本 为您的浏览器增加一个自定义脚本，如下图所示。

![image-20190726144850387](image-20190726144850387.png)
## 脚本的执行方式

用户脚本默认情况下在页面加载的时候自动执行，当然你可以通过设置[脚本作用域](#脚本作用域)来决定脚本是否全局生效，还是在特定的域名下生效。

除此之外脚本也可以设置为浏览器UI上的扩展点手动触发，目前存在三种常规的扩展点。 页面工具菜单、长按菜单、主菜单。



![image-20190726144926217](image-20190726144926217.png)



设置菜单扩展点作为脚本执行方式后，会自动在相应的菜单生成菜单选项作为脚本执行入口，如下图所示。

![image-20190726160940270](image-20190726160940270.png) ![image-20190726161055833](image-20190726161055833.png) ![image-20190726161235675](image-20190726161235675.png)


## 脚本作用域

当您设置脚本为自动执行时，我可以通过设置脚本的作用域来决定脚本是全局生效还是在特定的站点生效。 下面是作用域规则的表示方式。

```
*
```
匹配所有站点
```
www.example.com
```
精确匹配特定的域名，脚本只在   www.example.com 域名下执行


```
example.com
.example.com
*.example.com
```

以上任意写法都可以用来匹配主域名为example.com 的站点

```
www.example.com,abc.com,www.youku.com
```
匹配多个域名，中间以"," 分割

## 元数据


元数据，是放置在脚本头部的一些描述性标记，用来对脚本进行描述及设定。下面是X浏览器支持的一些元数据描述关键字。


        @name

用来描述脚本名称
        
        @author

脚本作者

        @description

脚本简介

        @incluide

这个标记可以存在多行，用来描述该脚本的作用域

        @match

和@include标记类似，可以存在多行。用来描述脚本作用域


        @exclude

这个标记用来排除一些域名，通常用来告诉全局脚本不要在特定的域名下执行。

        @require

这也是一个多行标记，指定该脚本需要引用第三方库或者脚本。

         @run-at

默认情况下脚本会在页面加载完毕后才会执行，我们可以通过"@run-at" 用来控制脚本的执行时机。参考下面的可选值。

- document-start 告诉浏览器脚本在dom树构建之前执行
- document-end 告诉浏览器脚本在dom树构建完毕后执行


以上是x浏览器目前所支持的元数据标记，这些标记兼容部分油猴脚本的元数据标识，更多详细信息请参考[这里](https://www.tampermonkey.net/documentation.php)




<b>下面是一个带有元数据的脚本示例,依赖第三方库vconsole  </b>

``` javascript
// @name 开发者调试工具
// @auth power by vconsole
// @description 这是一个开发者调试控制台工具，类似于Chrome的开发者开发者工具。可以帮助用户查看调试页面。
// @require https://cdn.jsdelivr.net/npm/vconsole@3.3.0/dist/vconsole.min.js

try {
    var vc = new VConsole();
    vc.show();
} catch (e) {
    mbrowser.showToast("load VConsole failed");
}
    

```

