---
title: vue —— 如何监听路由传递过来的参数
categories: "大前端"
tags: 
     - vue
---

for example

``` bash
created(){
    this.id = this.$route.query.id
}
```

id为路由传过来的参数，如果要实现实时监听参数的值并且在id发生改变时，同时通过id加载页面数据，只需要对路由进行监听即可！

``` bash
watch(){
    $route(){
        this.id = this.$route.query.id
    },
    id(){
        this.init()  //页面初始化函数，如果id发生改变，将会调用初始化函数
    }
}
```

The ending!
