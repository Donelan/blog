---
title: webpack搭建vue项目环境
date: 2019-04-17 20:00:00
tags: webpack
---
# webpack-vue
#### 0.项目初始化

npm init -y

#### 1.安装 webpack 

分为全局安装和项目内安装
```
    npm install webpack -g
    npm install webpack --save-dev
``` 

#### 2.配置webpack.config.js文件
```
     const path = require('path');

     module.exports = {
         entry: './Script/main.js', //项目入口文件
         output:{                    //输出编译后文件地址及文件名
             path: path.resolve(__dirname, 'dist'),
             filename: 'js/bundle.js'
         }
     };
```          
命令行里面执行 webpack 命令即可看到编译后的文件

#### 3安装webpack-html-plugin
npm install html-webpack-plugin --save-dev
```
    const HtmlWebpackPlugin = require('html-webpack-plugin');
    ...
    plugins:[
        ...
        new HtmlWebpackPlugin({
            title:'react 学习',
            inject:'body',
            filename:'index.html',
            template:path.resolve(__dirname, "index.html")
        }),
        ...
    ]
```          

再次执行webpack命令可看到多了一个index.html文件
这个文件是根据模板生成的并自动引入打包生成的js文件
运行打包后的index.html即可看到效果。

#### 4.安装Vue

```
   npm install vue -save
```
修改main.js：
```
    import Vue from  'vue';

    var MainCtrl = new Vue({
        el:'#main',
        data:{
            message:'Hello world'
        }
    })
```
修改index.html:
```
   <div id="main"
      <h3{{message}}</h3
   </div   
```
执行webpack打包运行index.html(打包的文件)报错，经查在webpack.config.js里面配置：
```
    ...
    resolve: { alias: { 'vue': 'vue/dist/vue.js' } }  //vue可以换成@
```
再次运行即可看到效果

#### 5.安装webpack-dev-server热更新

```
    npm install webpack-dev-server -g
    npm install webpack-dev-server --save-dev
    npm install vue-hot-reload-api --save-dev
```
 配置webpack.config.js
```
    ...
    devServer: {
        historyApiFallback: true,
    },
    ...
```  
配置package.json里面命令
```
    "start":"webpack-dev-server --hot --inline --progress --open"
```
执行 npm start 浏览器自动打开页面，更改文件后即可看到页面实时更新

 
#### 6.安装babel

在使用.vue文件之前先要安装babel(将es6语法转化为es5)
```
    npm install babel-core babel-loader babel-plugin-transform-runtime --save-dev 
    npm install babel-preset-stage-0 babel-runtime babel-preset-es2015 --save-dev 
```
项目根目录新建.babelrc文件、配置：
```
    {
    "presets": ["es2015", "stage-0"],
    "plugins": ["transform-runtime"]
    }
```

#### 7.安装vue-loader处理.vue的文件 

安装loader 处理.css,.vue文件
```
npm install css-loader style-loader vue-loader vue-html-loader --save-dev
```
配置webpack.config.js
```
   ...
   module:{
       rules: [
           {test: /\.js$/,loader: 'babel-loader',exclude: /node_modules/},
           {test: /\.vue$/,loader: 'vue-loader'}]
        },
    //vue: {loaders: {js: 'babel'}}
   ...
```
配置完运行报错：Cannot find module 'vue-template-compiler'
安装vue-template-compiler   //跑不起来可能是balel版本不对  https://blog.csdn.net/zr15829039341/article/details/86553652
```
npm install vue-template-compiler --save-dev
```


 修改index.html:
 ```
   <body
    <div id="main"
       <app</app   
    </div
   </body
 ```


新建src/index.vue:
```
  <template
    <div class="message"{{ msg }}</div
  </template

    <script
        export default {
        data () {
            return {
            msg: 'Hello from vue-loader!'
            }
        }
        }
    </script

    <style
        .message {
        color: blue;
        }
    </style
```


修改main.js
```
    ...
    import App from './src/index.vue';

    new Vue({
        el: '#main',
        components: { App }
    })
```

#### 8.使用less

安装:
```
    npm install less less-loader --save-dev
```
使用：
```
<style lang='less'
   .articleWrap{
     .articleTop{
       color:red;
     }
   }
</style
```


#### 9区分生产环境与开发环境

使用cross-env：设置开发环境
安装：npm install cross-env --save-dev
配置命令：
```
    "start": "cross-env NODE_ENV=development webpack-dev-server --hot --inline --progress --open",
    "build":"cross-env NODE_ENV=production webpack"
```
修改webpack配置：
```
   if (process.env.NODE_ENV === 'production') {
    config.plugins = (config.plugins || []).concat([
        new webpack.DefinePlugin({
            'process.env': {
                'NODE_ENV': JSON.stringify('production'),
            },
            IS_PRODUCTION: true
        }),
        /*new webpack.optimize.UglifyJsPlugin({
            compress: {warnings: false},
            sourceMap: false
        }),*/
    ]);
}
else {
    config.plugins = (config.plugins || []).concat([
        new webpack.DefinePlugin({
            'process.env':
            {
                'NODE_ENV': JSON.stringify('development'),
            },
            IS_PRODUCTION: false
        }),
    ]);
}
```
在程序里面通过：process.env.NODE_ENV 获取当前环境变量
 
