---
title: http缓存
date: 2020-04-23 10:39:45
tags:
---

总结一下http缓存相关的字段。
<!-- more -->

手上做的项目比较小，由于也是内网使用的原因，传输比较快，也没必要使用缓存。面试需要...总结一下http缓存字段。

## TL;DR

现代浏览器中，http1.1缓存相关的需求，用cache-control(`在写这个单词的时候，我纠结了一下是用大写还是小写，发现http头部字段是case-insensitive的`)字段加max-age字段就可以全部实现了，其他的都是历史遗留，现在为了兼容性所以仍在使用。

## Cache-Control
Cache-Control字段可以设置的directive比较多，所以我按照下面的规则分个类：

### How
- no-cache 响应可以被缓存，但是每次使用缓存前需要跟服务器验证缓存是否过期
- no-store 响应不可以被缓存，每次都要使用重新请求下载

### Who
- public 响应可以被所有地方缓存，包括中间服务器，比如代理、CDN等等
- private 一些用户关联的数据，指定private表明响应只可以被客户端缓存

### How long
- max-age 表示缓存有效的时间，以秒为单位，比如`max-age=90`
- s-maxage 跟max-age一样，只不过是对中间服务器使用的，比如proxy，`s-`代表shared，对于中间服务器来说，s-maxage优先级比max-age高
- must-revalidate 当中间服务器的缓存过期时，必须要重新跟源服务器验证后才可以继续使用
- proxy-revalidate
- no-transform

### Pragma
最老的http缓存字段了吧，http1.0时期的，可以设置成pragma: no-cache，跟cache-control一个效果，表示使用缓存前需要验证。Pragama的优先级比cache-control要高

### Expires
expires字段指定一个timestamp字符串，表示缓存的过期时间。要注意cache-control字段下的max-age和s-maxage比这个字段的优先级要高，但是为了兼容性，一般这两个都会设置。

用expires设置过期时间，验证时，会使用if-modified-since字段来验证，问题是只要设置时间格式，就会有精度的问题，精度范围之外发生多次改变时，验证就会不准确。ETag就解决了这个问题。

还有一个问题就是客户端和服务端时区不一致的时候，设置expires的也会问题。