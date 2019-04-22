---
title: Node爬虫
date: 2019-04-22 16:31:49
tags:
    - node
    - 爬虫
---

####  什么是爬虫
爬虫，实际上就是通过相应的技术，抓取页面上特定的信息。

#### 目标
爬某社区小姐姐照片

#### 代码编写
1. 通过`request`来进行页面信息的获取，通过`cheerio`模块对DOM的分析，在使用内置`fs`模块将照片存到本地。
```
npm init -y
npm install request cheerio --save
```
2. 引入需要的依赖
```
const request = require('request')
const cheerio = require('cheerio');
const fs = require('fs');
```
3. 首先生成一个文件夹，用来存放照片
```
fs.exists('dist', res => {
    if (!res) fs.mkdirSync('dist');
})
```
4. 在定义一个方法 用来保存图片
```
function saveFile(url) {
    if (!url) return
    request(url).pipe(fs.createWriteStream('./dist/' + Date.now() + '.png'));
}
```
5. 页面分析 f12打开控制台 根据解构编写代码 ，放上最终代码
```
const request = require('request')
const cheerio = require('cheerio');
const fs = require('fs');
const webUrl = 'https://bbs.hupu.com/selfie-type3'
// 创建一个生成目录 用来存放小姐姐照片
fs.exists('dist', res => {
    if (!res) fs.mkdirSync('dist');
})
// 写一个方法 用来保存图片
function saveFile(url) {
    if (!url) return
    request(url).pipe(fs.createWriteStream('./dist/' + Date.now() + '.png'));
}
for (var i = 0; i < 5; i++) {
    request(webUrl + '-' + i, (error, response, body) => {
        let $ = cheerio.load(body)
        var linkArr = $(".titlelink.box .truetit");
        for (var i = 1; i < linkArr.length; i++) {
            request('https://bbs.hupu.com' + $(linkArr[i]).attr('href'), (error, response, body) => {
                let $ = cheerio.load(body);
                var img = $('.quote-content img');
                for (var j = 0; j < img.length; j++) {
                    saveFile($(img[j]).attr('src'))
                }
            })
        }
    })
}
```
6. 附上最终截图
{% asset_img 截图.jpg 截图 %}