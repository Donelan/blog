---
title: Vue底层实现原理概述
date: 2019-04-18 17:40:00
tag: Vue
---
#### 实现原理概述

这是前言提到的文章里的代码，一段典型的体现了`Vue`特点的代码：

```
<div id="mvvm-app">
    <input type="text" v-model="word">
    <p>{{word}}</p>
    <button v-on:click="sayHi">change model</button> //点击这个button，word的值会发生改变
</div>

<script src="./js/observer.js"></script>
<script src="./js/watcher.js"></script>
<script src="./js/compile.js"></script>
<script src="./js/mvvm.js"></script>
<script>
    var vm = new MVVM({
        el: '#mvvm-app',
        data: {
            word: 'Hello World!'
        },
        methods: {
            sayHi: function() {
                this.word = 'Hi, everybody!';
            }
        }
    });
</script>
```

####  Observer

`Observer`的核心是通过`Obeject.defineProperty()`来监听数据的变动，这个函数内部可以定义`setter`和`getter`，每当数据发生变化，就会触发`setter`。这时候`Observer`就要通知订阅者，订阅者就是`Watcher`。

#### Watcher

`Watcher`订阅者作为`Observer`和`Compile`之间通信的桥梁，主要做的事情是：
1. 在自身实例化时往属性订阅器(dep)里面添加自己
2. 自身必须有一个`update()`方法
3. 待属性变动`dep.notice()`通知时，能调用自身的`update()`方法，并触发`Compile`中绑定的回调

#### Compile

`Compile`主要做的事情是解析模板指令，将模板中的变量替换成数据，然后初始化渲染页面视图，并将每个指令对应的节点绑定更新函数，添加监听数据的订阅者，一旦数据有变动，收到通知，更新视图。

#### 总结

关于每部分具体的代码实现，可以参阅  [剖析Vue原理&实现双向绑定MVVM](https://segmentfault.com/a/1190000006599500) 这篇文章，这篇文章写得非常好
