# WxAutoImage

## 来源
[微信小程序开发论坛](http://weappdev.com/)
垂直微信小程序开发交流论坛

## 有话说

如果你已经看过一段时间的微信小程序后，你会发现，小程序中的图片虽然给了很多种`mode`，但是在你使用过程中，会出现很尴尬的问题。
比如： 图片很高，但是在显示的时候，只能看到小的缩略图，且如不指定宽高，则在默认渲染中，则宽高都为320，240.

在视觉设计上会有一定的麻烦。

在实际开发中，也有很多人问过我这个问题，今天说一下单图片的适配，多图片我目前还没有找到合适的处理方案，等有了再说。

## 通过image的`bindload`来计算自适应宽高

> 微信小程序中image暴漏的函数比较少，但是`bindload`十分有用。

**官方说明： 当图片载入完毕时，发布到 AppService 的事件名，事件对象event.detail = {height:'图片高度px', width:'图片宽度px'}**

⚠️： 这里的`height:'图片高度px', width:'图片宽度px'`都是原始宽高，这就给了我们可以计算的余地


* 思路
 + 获取图片原始宽高
 + 获取系统屏宽
 + 以原始宽与系统屏宽对比大小，以大者为基准缩放

* 实现函数
> 我在主要函数中，加入了少量注释。

```
/***
 * wxAutoImageCal 计算宽高
 * 
 * 参数 e: iamge load函数的取得的值
 * 返回计算后的图片宽高
 * {
 *  imageWidth: 100px;
 *  imageHeight: 100px;
 * }
 */
function wxAutoImageCal(e){
    console.dir(e);
    //获取图片的原始长宽
    var originalWidth = e.detail.width;
    var originalHeight = e.detail.height;
    var windowWidth = 0,windowHeight = 0;
    var autoWidth = 0,autoHeight = 0;
    var results= {};
    wx.getSystemInfo({
      success: function(res) {
        console.dir(res);
        windowWidth = res.windowWidth;
        windowHeight = res.windowHeight;
        //判断按照那种方式进行缩放
        console.log("windowWidth"+windowWidth);
        if(originalWidth > windowWidth){//在图片width大于手机屏幕width时候
          autoWidth = windowWidth;
          console.log("autoWidth"+autoWidth);
          autoHeight = (autoWidth*originalHeight)/originalWidth;
          console.log("autoHeight"+autoHeight);
          results.imageWidth = autoWidth;
          results.imageheight = autoHeight;
        }else{//否则展示原来的数据
          results.imageWidth = originalWidth;
          results.imageheight = originalHeight;
        }
      }
    })

    return results;

  }
```

## 使用

* 1.在image属性绑定函数和动态style

```
<image bindload="cusImageLoad" src="https://dn-cnode.qbox.me/FjfPMJYFAbyV1-OM-CcCC5Wk2tmY" style="width:{{imageWidth}}px;height:{{imageheight}}px"/>
```

* 2.在load函数中引入计算

```
//index.js
//获取应用实例
var WxAutoImage = require('../../WxAutoImage/WxAutoImage.js');
var app = getApp()
Page({
  data: {
    
  },
  onLoad: function () {
    console.log('onLoad')
    var that = this
  },
  cusImageLoad: function (e){
    var that = this;
    //这里看你在wxml中绑定的数据格式 单独取出自己绑定即可
    that.setData(WxAutoImage.wxAutoImageCal(e));
  }
})

```

## 问题

目前这种操作比较负责，暂时没有想到多图片的解决方案。

希望有更好的解决方案的朋友指正批评。

## 来源
[微信小程序开发论坛](http://weappdev.com/)
垂直微信小程序开发交流论坛