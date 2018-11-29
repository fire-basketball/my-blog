---
title: 用一个vue项目开启webpack的学习全记录
categories: "大前端"
tags: 
     - webpack
---



##  Day one: 一个最简单的webpack项目

我们新建一个TODO项目，初始化命令：

    npm init;

安装npm，然后会有一些warn指示，我们根据指示安装项目所需要的依赖，请根据提示安装

    npm i css-loader
    
然后创建src，新建一个app.vue，这是一个vue文件，内容很简单

    <template>
    <div id="text">
        {{text}}
    </div>
    </template>
    <script>
    export default {
        data(){
            return {
                text:'hello world'
            }
        }
    }
    </script>
    <style>
        #test{
            color:black;
        }
    </style>
    

新建webpack.config.js

    const path = require('path')
    module.exports = {
        entry: path.join(__dirname,'src/index.js'),
        output:{
            filename: 'bundle.js',
            path: path.join(__dirname,'dist')
        }
    }
    
包含入口文件main.js，和出口文件bundle.js；

此时定义入口文件index.js,配置一个vue对象，将内容挂载上去

    import Vue from 'vue'
    import App from './app.vue'
    
    const root = document.createElement('div')
    document.body.appendChild(root)
    
    new Vue({
        render:(h) => h(App)
    }).$mount(root)   
    
入口文件定义好后，我们可以在package.json中加一行脚本，指定在该项目中webpack打包，而不是全局打包

    "scripts": {
        "test": "echo \"Error: no test specified\" && exit 1",
        "build": "webpack --config webpack.config.js"  //这里
      }
     
此时我们可以打包试试  ==npm run build==

哈哈，报错，大家看看报错

    ERROR in ./src/app.vue 1:0
    Module parse failed: Unexpected token (1:0)
    You may need an appropriate loader to handle this file type.

我们需要安装一个loader区处理vue类型的文件，然后配合我学习的视屏里是这样配置的

    const path = require('path')
    module.exports = {
        entry: path.join(__dirname,'src/index.js'),
        output:{
            filename: 'bundle.js',
            path: path.join(__dirname,'dist')
        },
        module:{
            rules:[
                {
                    test: /\.vue$/,
                    loader: 'vue-loader',
                }
            ]
        }
    }

我试了之后还是不行，很鸡肋，不知道视频中的webpack是哪个版本，然后百度了一个，现在的版本是要加一个插件VueLoaderPlugin的，在查找问题的过程中自作主张加了一个严格模式的规定，然后，看
    
    'use strict'
    const path = require('path')
    const VueLoaderPlugin = require('vue-loader/lib/plugin')  //这里
    module.exports = {
        entry: path.join(__dirname,'src/index.js'),
        output:{
            filename: 'bundle.js',
            path: path.join(__dirname,'dist')
        },
        plugins:[   //这里
            new VueLoaderPlugin()
        ],
        module:{
            rules:[
                {
                    test: /\.vue$/,
                    loader: 'vue-loader',
                }
            ]
        }
    }
    
我本以为这样好了，但是，继续报错

    [vue-loader] vue-template-compiler must be installed as a peer dependency, or a compatible compiler implementation must be passed via options.
    
很生气，在一开始的warn中，视频中是有这个依赖提示的，但是我没有就没安装，现在来了，总归是要装的

    npm i vue-template-compiler
    
继续，烧香祈祷，呵呵

    ERROR in ./src/app.vue?vue&type=style&index=0&lang=css& 18:0
    Module parse failed: Unexpected token (18:0)
    You may need an appropriate loader to handle this file type.
    |
    |
    > .test{
    |     color:black;
    | }

继续报错，保持微笑，这里提示的意思应该是样式需要loader来处理它，矫情，于是百度了一下，加了这个：
    
    'use strict'
    const path = require('path')
    const VueLoaderPlugin = require('vue-loader/lib/plugin')
    module.exports = {
        entry: path.join(__dirname,'src/index.js'),
        output:{
            filename: 'bundle.js',
            path: path.join(__dirname,'dist')
        },
        plugins:[
            new VueLoaderPlugin()
        ],
        module:{
            rules:[
                {
                    test: /\.vue$/,
                    loader: 'vue-loader',
                },
                {
                    test: /\.css$/,             //这里
                    use:['vue-style-loader','css-loader']
                }
            ]
        }
    }


最后我终于成功了，看到一片绿的时候，简直想哭啊！！！
呼呼

最终就生成了一个dist/bundle.js文件啊，感觉自己好棒棒！！

好，第一个课程完美，希望大家跟着视频学习的时候有耐心一点，因为老师和你的环境和用的版本毕竟不一样，仔细研究一下报错，耐心解决，感觉在这个过程里收获比较大。哈哈！

    

##  Day2: 常用的loader配置

webpack可以加载前端所能用到的所有静态资源，配置loader,具体有很多

    {
        test: /\.css$/,
        use:['vue-style-loader','css-loader']
    }
    
这是昨天为了解决报错加的配置，这里为什么使用use,而不是loader.

因为对于样式有不同的处理方式，因此使用use,采用数组的格式加载不同的laoder,此处style-loader用来处理html中的style样式，而css-loader主要用来处理css样式文件。
    
    
     {
         test:/\.(gif|jpg|jpeg|png|svg)$/,
         use:[
             {
                 loader:'url-loader',
                 options:{
                     limit: 1024,
                     name: '[name]-thm.[ext]'
                 }
             }
         ]
     }
loader是可以配置一些选项的，使用options来配置
图片文件的处理，url-loader将图片转换为base64代码，写在js内容里面，而不用生成一个文件，这对于小一点的图片文件是很友好的，这里限制了图片的大小，url-loader依赖于file-loadaer,它判断文件大小如果小于1024，就执行转译。可通过name自行配置图片编译后的名称后缀。

配置后我们需要安装一下用到的依赖
    
    npm i url-loader file-loader
    
在配置好了处理样式和图片的loader之后，我们创建assets文件夹，放入一些图片和一个样式文件，在index.js中导入

    import './assets/style/base.css'
    import './assets/img/hh.jpg'
    
然后打包，我们可以看一下打包后生成的文件，哈哈

生成的图片果然带上了'-thm'后缀，样式也在bundle.js中。


css预处理器——模块化写css,为了实现css预处理，我们配置styl(就是一种写法很随便的,和less,sass差不多的css预处理器)

    {
        test: /\.styl/,
        use:[
            'style-loader',
            'css-loader',
            'stylus-loader'
        ]
    }

然后我们可以添加一个styl文件，并且将它导入在index.js中

不要忘了安装loader,stylus-loader依赖于stylus，都要装，不然会报错

    npm i sytle-loader stylus-loader stylus
    
再次打包，我们写的styl样式就被写好放进bundle.js中啦。

未来还有很多loader等着我们去发现。多积累。

## Day3 webpack-dev-server 的配置和使用

webpack-dev-server 是webpack的一个包，==专门用在开发环境==，首先我们进行安装

    npm i webpack-dev-server

然后我们做一些配置去适应webpack-dev-server的开发模式

首先我们在package.json中增加一行dev命令
    
    "scripts": {
        "test": "echo \"Error: no test specified\" && exit 1",
        "build": "cross-env NODE_ENV=production webpack --config webpack.config.js",
        "dev": "cross-env NODE_ENV=development webpack-dev-server --config webpack.config.js"
    }
    
这里使用了cross-env，为了解决跨平台的问题，在windows中我们可能要  set NODE_ENV=development，所以在安装了cross-env之后，我们就可以采用以上写法。

然后我们回到webpack.config.js，在其中作出如下改变

1.定义一个isDev来接收运行时的NODE_ENV值，判断是处于开发环境还是生产环境，

2.在原来的配置加增加一些配置，新增两个插件

    new webpack.DefinePlugin({
         'process.env':{
             NODE_ENV: isDev ? '"development"':'"production"'
         }
    }),
    new HTMLPlugin()
    
需要进行引入

    const HTMLPlugin = require('html-webpack-plugin')
    const webpack = require('webpack')
    
and

    npm i html-webpack-plugin

说实话这个具体干什么的我忘了，后期补

3.如果是开发环境，我们需要去添加一些配置项

    if(isDev){
        config.devtool = '#cheap-module-eval-source-map'
        config.devServer = {
            port: 8000,
            host: '0.0.0.0',
            overlay:{
                errors:true
            },
            open: true,
            hot: true
        }
        config.plugins.push(
            new webpack.HotModuleReplacementPlugin(),
            new webpack.NoEmitOnErrorsPlugin()
        )
    }
    
我们可以看到有端口号，host地址，其中----》overlay用于处理一些异常，open项目运行成功会自动打开窗口，但如果webpack配置被更改，也会弹出新窗口，酌情使用。

hot局部更新（我理解的），就是我做什么修改，会之刷新那一部分代码，而不是整个项目刷新；

为了使用hot,我们push了两个插件HotModuleReplacementPlugin、NoEmitOnErrorsPlugin

为了优化还是干嘛的，我们还加了一行devtool

以上内容虽然跑起来了，但是还是很懵逼啊，后期查阅资料再补充知识点吧！

微笑~


## Day4 vue2核心知识介绍

  #### API重点

  1.数据绑定

  2.vue文件开发方式

  3.render方法

      template -> js中的render方法
      
      render(){
          createElement()创建节点
      }
      
  4.生命周期方法

  5.computed

  ps:vue的api重点不止这些，这些也不能说是最重要的，后期会总结一下vue的重点，希望大家多   指正！

  #### vue的jsx写法以及postcss

  针对todo项目做一些前期的优化工作，对于css做一些配置处理

      npm i post-css-loader autoprefixer babel-loader babel-core

  autoprefixer ---- 样式浏览器兼容的前缀自动添加

  增加.babelrc和postcss.config.js配置文件
      
  postcss.config.js   

      const autoprefixer = require('autoprefixer')

      module.exports = {
          plugins:[
              autoprefixer()
          ]
      }

  .babelrc   

      {
          "presets": [
              "env"
          ],
          "plugins":[
              "transform-vue-jsx"
          ]
      }
      
  顾名思义===将vue文件转换成jsx代码
      
  不要忘了进行配置

      npm i babel-preset-env babel-plugin-transform-vue-jsx
      
  然后在webpack里面进行配置

      {
          test: /\.jsx$/,
          loader: 'babel-loader'
      },
      {
          test: /\.styl/,
          use:[
              'style-loader',
              'css-loader',
              {       //这里，进行添加
                  loader:'postcss-loader',
                  options: {
                      sourceMap: true
                  }
              },
              'stylus-loader',
          ]
      }
      
  然后运行一下项目，完美！



## Day5  todo项目的开发

技术重点：vuex

功能点：

- 新建note 
- 编辑note
- 清理note（即放入回收站）
- 标记重要note

项目中使用的一些有关vue的知识点

- vuex 
    
    这里不具体说他是怎么用的，网上可以找到很详细的教程。我在入门的时候，一直很奇怪这个状态管理到底是用来干嘛，到底有什么作用。所以刚接触web端vue项目的时候，基本不会用它。但是用了以后真的发现vuex如果写的清晰是完全可以存储数据的，比如在一个项目里如果有一些信息是在不同的地方都要用到的，而且在不同的地方都要改变，那么与其在不同的地方声明调用获取，还不如一次性声明，定义一个mutation去更改它的状态，而将所有异步的操作都放在action里面，与服务端交互。

    这个项目的vuex文件我只写了一个因为很简单：一个当前激活的笔记activenote，和一个存储所有笔记的数组，以及一个记录分类栏的变量。而对笔记的操作将通过mutation中更新。
    
    这个时候我们无需费心去考虑兄弟组件的交互，只要我们对vuex中的值进行监听(computed)，那么当state改变时，页面也会自动渲染。
    
-  vuex-persistedstate
    
    vuex虽然好用，但是一刷新就会被清除，所以在我原本的项目中，一般会使用vue-cookie进行存储。而vuex-persistedstate则很好的解决了这个问题，这个插件内部对数据进行了localstorage的存储，因此就不需要我们进行存储了。撒花~

- 一个布局的积累
    
    一列宽度固定，一列宽度自适应（项目中左边两列都固定可看做一列）

    我的解决方法：
        
        <div class="parent">
          <div class="left">
            <p>left</p>
          </div>
          <div class="right">
            <p>right</p>
            <p>right</p>
          </div>
        </div>
        
        <style>
          .parent {
            display: flex;
          }
          .left {
            width: 100px;
            margin-left: 20px;
          }
          .right {
            flex: 1;
          }
        </style>
        
另外的一些解决方法：

    <div class="parent">
      <div class="left">
        <p>left</p>
      </div>
      <div class="right">
        <p>right</p>
        <p>right</p>
      </div>
    </div>
    
    <style>
      .left {
        float: left;
        width: 100px;
      }
      .right {
        margin-left: 100px
        /*间距可再加入 margin-left */
      }
    </style>

-----

    <div class="parent">
      <div class="left">
        <p>left</p>
      </div>
      <div class="right">
        <p>right</p>
        <p>right</p>
      </div>
    </div>
    
    <style>
      .left {
        float: left;
        width: 100px;
      }
      .right {
        overflow: hidden;
      }
    </style>

----
    
    <div class="parent">
      <div class="left">
        <p>left</p>
      </div>
      <div class="right">
        <p>right</p>
        <p>right</p>
      </div>
    </div>
    
    <style>
      .parent {
        display: table;
        width: 100%;
        table-layout: fixed;
      }
      .left {
        display: table-cell;
        width: 100px;
      }
      .right {
        display: table-cell;
        /*宽度为剩余宽度*/
      }
    </style>

- vue中点击阻止冒泡事件产生

    点击子元素阻止父元素的点击事件 ---》 @click.stop
    



