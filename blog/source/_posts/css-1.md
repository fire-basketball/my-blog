---
title: css -- flex布局实现自适应居中 & 让最后一行左对齐
categories: 大前端
tags:
      -css
---

关于flex布局，详情请见阮一峰老师的博客，在这里不细说。

在我们使用flex布局是，为了自适应文本居中，我们会使用这个属性

    .box {
        justify-content: space-between | space-around;
    }

在加了这个属性后，会衍生出一个问题：最后一行的内容也自适应居中，就像这样

![image](/images/1.jpeg)

最后一行我们希望他相左对齐并且保持垂直对齐，要怎么做呢，有一个巧妙的方法，即在这些元素后面加一些空元素撑起来

      <ul class="box">
        <li v-for="(item, index) in data" :key="index"></li>
        <li v-for="n in 7"></li>
      </ul>

效果图如下

![image](/images/2.jpg)

就酱紫~