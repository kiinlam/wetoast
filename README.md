WeToast for 微信小程序 toast增强插件
===

## 概述

[WeToast](https://github.com/kiinlam/wetoast) 仿照微信小程序提供的 `showToast` API功能，提供视觉一致的增强插件，弥补小程序`wx.showToast`功能上的不足，如只能显示`success`、`loading`两种icon，且icon不可去除，持续时间最大10秒等。

## 预览

[下载WeToast项目](https://github.com/kiinlam/wetoast/archive/master.zip)，用[微信web开发者工具](https://mp.weixin.qq.com/debug/wxadoc/dev/devtools/download.html)打开项目根目录

## 如何使用

WeTaost插件源码位于`src`目录下，包含3个文件。

- wetoast.js: 脚本代码
- wetoast.wxml: 模板结构
- wetoast.wxss: 样式

使用时只需要加入以上3个文件即可，使用方法可参考本项目示范。

#### 推荐方案

##### Step1、在项目的`app.js`中引入`wetoast.js`，并注册到小程序上，小程序所有Page页面均可使用，无需再次引入

```javascript
let {WeToast} = require('src/wetoast.js')    // 返回构造函数，变量名可自定义
App({
	WeToast    // 后面可以通过app.WeToast访问
})
```

##### Step2、在项目的`app.wxss`中引入`wetoast.wxss`

```css
@import "src/wetoast.wxss";
```

##### Step3、引入WeToast模板结构，

*方式一，在单独页面使用*

```html
<!-- 文件 index.wxml 中 -->
<import src="../../src/wetoast.wxml"/>
<template is="wetoast" data="{{...__wetoast__}}"/>
```

*方式二，创建公用包含文件，将所有公用模板放在一起*

```html
<!-- 文件 footer.wxml 中 -->
<import src="src/wetoast.wxml"/>
<template is="wetoast" data="{{...__wetoast__}}"/>
<!-- 其他xxoo模板 -->
<template is="wexxoo" data="{{...wexxoo}}"/>
```

*然后通过`include`引入*

```html
<!-- Page文件 index.wxml 底部 -->
<include src="footer.wxml"/>
```

## API

#### WeToast()

构造函数，返回WeToast实例对象，该操作会在当前Page上创建一个名为`wetoast`的引用，在Page中可通过`this.wetoast`访问。通常在Page的`onLoad`中调用，可重复使用。

###### 示例

```javascript
// 创建可重复使用的WeToast实例，并附加到Page上，通过this.wetoast访问
new app.WeToast()
// 也可创建变量来保存
let mytoast = new app.WeToast()
```

#### WeToast.prototype.toast(Object)

控制toast的显示、隐藏，接收一个可选的对象作为配置参数。不提供参数时，表示隐藏toast。

###### Object参数说明：

| 参数          | 类型          | 必填  | 说明                         |
| ------------- | ------------- | ----- | ---------------------------- |
| img           | String		| 可选* | 提示的图片，网络地址或base64 |
| imgClassName  | String		| 否    | 自定义图片样式时使用的class  |
| imgMode       | String		| 否    | 参考小程序image组件mode属性  |
| title 		| String		| 可选* | 提示的内容                   |
| titleClassName| String		| 否    | 自定义内容样式时使用的class  |
| duration 		| Number		| 否    | 提示的持续时间，默认1500毫秒 |
| success 		| Function		| 否    | 提示即将隐藏时的回调函数     |
| fail 			| Function		| 否    | 调用过程抛出错误时的回调函数 |
| complete 		| Function		| 否    | 调用结束时的回调函数         |

*可选表示至少设置 `img` 或 `title` 中的一个*

###### img参数补充说明

提示的图片设置尺寸为55px * 55px，建议使用原始大小为110px * 110px的图片。使用图片时，优先选择base64形式，保证实时显示。

###### title参数补充说明

提示框的宽度设置了最小宽度为8.4em，最大宽度为屏幕的70%，超过时会换行。

###### duration参数补充说明

当`duration`设置为`0`时，将不自动隐藏提示层，直到下次再次调用`wetoast.toast()`，不传入配置项表示隐藏提示。

###### 回调函数参数补充说明：

`success`、`fail`、`complete`执行时均会回传配置参数`Object`。无论成功或失败，`complete`都会执行。

###### 示例

```javascript
// 只显示图标，不显示文字
wetoast.toast({
    img: 'https://raw.githubusercontent.com/kiinlam/wetoast/master/images/cross.png'
})
```

```javascript
// 只显示文字，不显示图标
wetoast.toast({
    title: 'WeToast'
})
```

```javascript
// 显示文字、图标，执行回调函数
wetoast.toast({
    img: 'https://raw.githubusercontent.com/kiinlam/wetoast/master/images/star.png',
    title: 'WeToast',
    success (data) {
        console.log(Date.now() + ': success')
    },
    fail (data) {
        console.log(Date.now() + ': fail')
    },
    complete (data) {
        console.log(Date.now() + ': complete')
    }
})
```

```javascript
// 自定义显示持续时间
wetoast.toast({
    title: 'WeToast',
    duration: 5000
})
```

## 问答

##### 问：为什么做这个插件

微信小程序提供的`showToast`API目前仅支持显示`success`、`loading`两种图标，不够用，且在某些场景下，最大值10秒也不够用。

在官方未提供更丰富配置的情况下，有必要在官方UI规范的框架下提供一套功能更实用的备选方案。

同时我也希望各开发者能够达成共识，在实现自身需求时，尽量以官方UI规范为指导，避免出现各种花样的弹层效果。

##### 问：是否会出现“串页”问题

此处“串页”是指上一页的代码在当前页执行。在navigate跳转的情况下，由于页面不是被关闭，因此代码还在执行，一些涉及全局的操作会被带入当前页。

在开发本插件的时候，充分考虑了这一点，采用实例化toast对象并附加到当前的Page对象上，在切换Page后仍然指向上一页的Page对象，不会出现“串页”问题。

## TODO

- 增加预定义ICON
- 增加可自定义动画功能

## License
The MIT License(http://opensource.org/licenses/MIT)

请自由地享受和参与开源

## 贡献

如果你有好的意见或建议，欢迎给我提issue或pull request。

