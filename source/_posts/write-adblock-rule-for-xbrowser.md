---
title: 为X浏览器撰写广告拦截规则
date: 2019-07-26 11:48:03
tags: adblock
categories: tutorials
---


## 简介

X浏览器并不完全兼容[ABP](https://adblockplus.org/zh_CN/filters)规则的写法。ABP诞生于PC时代，出于移动设备的性能考虑X浏览器没有完全实现ABP的拦截策略和规则，但是提供了简单高效的拦截策略和规则写法，基本上兼顾了性能和拦截效果。经测试X浏览器可以在浏览页面的时候执行3W+条拦截规则而对页面的渲染并不会产生显著的影响。

X浏览器内部提供自动规则导入工具可以把大部分ABP规则导入为X浏览器所用，但并不建议直接导入ABP官方完整规则，因为ABP官方绝大多数的规则是针对PC浏览器的，加载过多规则只会带来无谓的性能消耗。



## 快速写法示例


```
www.example.com
```

<center>拦截域名为 "www.example.com" 的请求</center>

```
/content/ads
```

<center>拦截以"/content/ads"作为请求路径的url </center>


```
/runtime/js/index960.js
```

<center>拦截某个主机下路径为/runtime/js/index960.js的脚本</center>


```
###ad-container
```

<center>移除ID为"ad-container"的页面元素</center>



```
##.ad-container
```

<center>移除类名为"ad-container"的页面元素</center>


```
##div[title*="adv"]
```

<center>移除所有属性为title并且包含"adv"字串的div元素</center>


## 规则说明

和ABP一样X浏览器提供了两类不同的规则策略。"拦截策略"和"隐藏元素策略"你可以针对不同类型的广告选择合适的拦截策略。


#### 拦截策略

拦截策略是通过匹配广告网址的url，在网络请求之前就被拦截。这种方式针对从外部加载资源的广告比较有效，拦截性能也比较高。
"拦截策略"提供了两种匹配方式，分别是基于域名和目录的匹配方式以及基于正则的匹配方式，从性能的角度考虑你应该优先使用基于域名和目录的匹配方式撰写规则，正则的匹配方式作为补充。


##### 基于域名和目录的匹配方式

```
example.com
```

<center>这条策略会拦截所有顶级域名为example.com的广告网址，如: www.example.com,abc.example.com等会被拦截</center>

```
/content/ads
```

<center>拦截任意域名下以"/content/ads"目录开头的广告资源url，如"http://www.example.com/content/ads/ad.js"，"http://ad.example.com/content/ads/player/third/ad.js"都会被拦截 </center>

```
example.com/content/ads
```

<center>拦截指定域名下的包含匹配路径的url</center>


```
www.example.com/content/ads/ad.js
content/ads/ad.js
```

<center>以上两条规则分别用于拦截具体的资源文件，如"http://www.example.com/content/ads/ad.js?timestep=2122323311" </center>


##### 基于正则的匹配方式

对于懂得正则表达式的同学可以通过正则对广告链接实现更加灵活的模式匹配,正则表达是规则约定以"--"开头。换句话说"--"后面紧跟你要写的正则表达式，见下面的示例。

```
--ads
```

<center>这条规则会拦截所有url中包含"ads"字串的广告。如："https://www.example.com/data/myads/popup.js" 以及 "http://www.example.com/data/ads.js" </center>

```
--\.swf$
```

<center>这条规则会拦截掉所有.swf后缀的广告资源如："http://www.example.com/ad/banner.swf" 但不会拦截 "http://www.example.com/content/swf/index.html" </center>

```
--ad(\d{1,2})?\.example\.com
```

<center>匹配ad.example.com,ad1.example.com,ad20.example.com等ad+数字编号开头的子域</center>


```
--ad\w*\.js$
```

<center> 匹配包含字串"ad"字样的JS脚本。如："http://www.example.com/content/sina_ad_wap.js" </center>

#### 隐藏元素策略

对于不是通过加载外部资源和脚本的广告，换句话说，我们无法通过拦截的方式移除广告。这时候可以通过隐藏元素规则移除页面的广告元素。隐藏元素规则的写法和ABP隐藏元素规则一样以"##"开头，后面紧跟[CSS选择器语法](http://www.runoob.com/cssref/css-selectors.html) 。下面是一些隐藏元素规则的示例。


```
###mybanner
````

<center>隐藏id为"mybanner的元素</center>

```
##.ad-container
```

<center>隐藏class为"ad-container"的元素</center>


```
##div[title*="adv"]
```

<center>只拦截example.com域名下id为"mybanner"的元素</center>



<p></p>
和ABP的隐藏元素规则一样，可以通过增加域名前缀来控制隐藏元素规则的作用域,如下示例

```
example.com###mybanner
```


<center>只拦截example.com域名下id为"mybanner"的元素</center>


#### 规则作用域
默认情况下你写的拦截规则是对白名单除外的所有站点都会生效，有时候为了避免规则对其他站点造成误杀我们可以限定规则的作用域。通过作用域限定规则只在特定域名下才会生效。

设定规则的作用域只需在原有规则后面加上"@"然后紧跟我们需要限定的域名，下面都是含有作用域的合法规则。

```
ad.xxx.com@example.com
ad.xxx.com/content/ads@example.com
###mybanner@example.com
```


为了兼容ABP的隐藏元素规则作用域的写法，以下两种写法是等价的。

```
###mybanner@example.com
example.com###mybanner
```


## 测试您的规则


我们提供了一个拦截[测试用例](/rule-test-case/)页面，你可以用这个页面测试你的拦截策略是否能够正常工作。