---
title: vue —— 如何强制刷新子组件
categories: "大前端"
tags: 
     - vue
---

在一个父组件中引用一个子组件，当子组件已经渲染出一些数据时，在不刷新父组件的前提下重新渲染子组件，这个问题我之前想了很多方法，然后昨天突然看到了一个巧妙有很搞笑的方法，哈哈，贴出来分享一下~

首先，使用一个变量控制子组件的渲染, show = true

``` bash
<parent>
    <child v-if="show"></child>
</parent>
```

重点来了，当我们想要强制刷新子组件时，只需要
``` bash
this.show = false
this.$nextTick(function() {
    this.show1 = true;
});
```

我惊呆了😳 ！！！这波操作就是完美的转换了一下show的布尔类型，重新把dom渲染了一下！！！

然后，就成功了！！然后就没有然后了~~~~

哈哈，祝大家and我自己七夕大雨劫快乐！

The ending!
