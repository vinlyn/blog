---
title: Webpack配置详解
date: 2017-03-09 14:26:58
categories: [教程, webpack]
tags: [webpack]
---
### 写在前面
webpack是目前非常火的模块化打包工具，网上有非常多的教程，但是很多都是教大家怎么配置出一个可以打包的配置文件，并没有具体说明每个配置作用，就造成，配置文件可以跑了，却依然莫名其妙，所以今天就来说说webpack具体每个配置项的作用。此篇文章介绍以webpack2为主。
### 什么是webpack
Webpack是一个强力的模块打包器。所谓包(bundle)就是一个JavaScript文件，它把一堆资源(assets)合并在一起，以便它们可以在同一个文件请求中发回给客户端。 包中可以包含JavaScript、CSS样式、HTML以及很多其它类型的文件。其实就是根据模块的依赖关系进行静态分析，然后把这些模块按照执行的规则生成对应的静态资源。在webpack的概念里，万物都可以当成模块来使用，不只是js，如图片，css等也可以。如图
<img src="/images/webpack/webpack.png" style="width: 100%;">
### webpack安装
首先要安装node，然后安装webpack。
```
npm install webpack --save-dev
```
### 配置项
#### context
基础目录，绝对路径，只针对入口起点(entry point)和加载器(loader)。
在entry和loader中就可以以context为绝对路径。
```js
var path = require('path');
module.exports = {
  context: path.resolve(__dirname, 'app'),
  //如果没有配置context，则该配置为相对路径的话，entry按照我的目录结构应为./app/entry.js。
  entry: './entry.js', 
  output: {
    filename: 'bundle.js',
    path: path.resolve(__dirname, 'dist')
  }
};
```
#### entry
起点或是应用程序的起点入口。
string | [string] | object { key: string | [string] }
string 单个文件路径，打包到一个文件里面。
[string] 多个文件路径，一起打包到一个文件里面。
object { key: string | [string] } 多个文件路径，打包出多个文件，并且打包的文件会与key来命名。
<img src="/images/webpack/webpack-entry.png" style="width: 100%;">
#### output
webpack 如何去输出、以及在哪里输出你的「bundle、asset 和其他你所打包或使用 webpack 载入的任何内容」。即出口，处理打包后的文件，如放在什么位置，取什么名字等。下面将列出几个常用的，并附上打包后的结果图。[详情可见官方Api](https://doc.webpack-china.org/configuration/output/)
##### output.filename
对于单个入口，即entry为string或[string]
```js
filename: 'bundle.js' // 如不命名，默认为main.js
```
对于多个入口，即entry为object { key: string | [string] } 
```js
// 默认值[name].js，
// hash和chunkhash区别：hash相当于，整个模块的hash,如果在打包的文件中有一个文件改变，那么这个hash也会改变，
// 且全局唯一，而chunkhash只有在自己的块有改动过才会改变，所以如果分成很多个模块打包的话，一般使用chunkhash。
// chunk id：webpack会为每个引用的模块生成一个ID, 这个id就是chunk id。
filename: "[name].bundle.js" // 使用入口名称，即key
filename: "[id].bundle.js"   // 使用内部chunk id, 
filename: "[name].[hash].bundle.js" // 添加唯一的hash
filename: "[chunkhash].bundle.js" // 基于每个chunk的内容生成的hash
```
<img src="/images/webpack/webpack-output1.png" style="width: 100%;">
<img src="/images/webpack/webpack-output2.png" style="width: 100%;">
补充：[hash和chunkhash的区别详见](http://www.cnblogs.com/ihardcoder/p/5623411.html)
##### output.path
生成文件放置的路径（绝对路径）。如上图例子，就是配置在根目录/dist文件下，所以生成的文件都是在dist目录下
##### output.publicPath
此项就是配置当你资源保存在CDN，或者别的地方时，使用publicPath来配置cdn的地址或者外部地址。
##### output.chunkFileName
未被列在entry中，却又需要被打包出来的文件命名配置。什么场景需要呢？在按需加载（异步）模块的时候，这样的文件是没有被列在entry中的，如使用CommonJS的方式异步加载模块：
```js
require.ensure(["./entry2.js"], function(require) { 
    var a = require("./entry2.js"); 
}, 'entry2');
```
异步加载的模块是要以文件形式加载哦，所以这时生成 的文件名是以chunkname配置的，生成出的文件名就是entry2.js。如图
<img src="/images/webpack/webpack-output3.png" style="width: 100%;">
##### out.library
此选项是将webpack打包的脚本变成类库，如果你需要打包成类库，请用此选项。
##### output.libraryTarget
此选项是导出为类库时，类库的格式，导出方式为如下几种。
"var" - 导出为一个变量：var Library = xxx（默认）
"this" - 导出为 this 的一个属性：this["Library"] = xxx
"commonjs" - 导出为 exports 的一个属性：exports["Library"] = xxx
"commonjs2" - 通过 module.exports：module.exports = xxx 导出
"amd" - 导出为 AMD（可选命名 - 通过 library 选项设置名称）
"umd" - 导出为 AMD，CommonJS2 或者导出为 root 的属性
#### Module
模块，在模块化变成中，开发者将程序分解成离散功能块，即我们在程序中import,require进来的东西。
##### module.noParse
防止webpack解析哪些任何与给定正则表达式相匹配的文件。忽略的文件不应该被import require,define或者任何其他导入机制调用。忽略大型文件筐文件可以提高系统的构建性能。
##### module.rules
创建模块时，匹配请求的规则数组。这些规则能够修改模块的创建方式。这些规则能够对模块(module)应用加载器(loader)，或者修改解析器(parser)。
##### rule
规则，配置不同的条件和loader，通过条件匹配不同类型的模块，再通过配置相关的loader对模块进行编译或者说转换。为什么要使用loader，因为webpack本身只能打包js文件，对于其他资源文件如css，图片等是没有办法加载的，所以需要对应的loader将资源加载进来。从配置上也可以看得出来，它主要作用于单个文件。作用如css经过postcss转换成浏览器识别的css，jsx经过babel预编译成js，ES6预编译成ES5等。[更多loader...](https://webpack.js.org/loaders/)
该属性有两个主要要素：
##### rule条件
即匹配的模块规则。比如正则、路径等。
字符串：匹配输入必须以提供的字符串开始。目录绝对路径或文件绝对路径。
正则表达式：test 输入值。
函数：调用输入的函数，必须返回一个真值(truthy value)以匹配。
条件数组：至少一个匹配条件。
对象：匹配所有属性。每个属性都有一个定义行为。
{ test: Condition }：匹配条件。约定了提供一个正则或正则数组，但不是强制的。
{ include: Condition }：匹配条件。约定了提供一个字符串或字符串数组，但不是强制的。
{ exclude: Condition }：不能匹配条件。约定了提供一个字符串或字符串数组，但不是强制的。
{ and: [Condition] }：匹配所有条件
{ or: [Condition] }：匹配任何条件
{ not: Condition }：不能匹配条件
##### rule.use
配置编译模块的loader和一些相关配置。
必须要有一个loader，声明该加载器是使用的哪个loader。
可以有一个options属性为字符串或对象。值可以传递到loader中，将其理解为loader 选项配置。
有可能也存在一个query的属性，因为它是options的别名。
##### demo
<img src="/images/webpack/webpack-module.png" style="width: 100%;">
上述例子，test匹配css文件，use配置了相关的loader，在entry1.js中使用import './entry1.css'引入模块，webpack编译时，css文件会经过配置loader的编译，最后webpack将编译后的css打包到build-entry1.js中。如图所示，js中有css的代码。使用插件可以将css分离出来，下面将会介绍。
#### Plugins
插件，用于扩展webpack的功能，作用如UglifyJsPlugin压缩，extract-text-webpack-plugin提取css到单独的文件中等。plugins与loader的对比，在于loader是主要用于针对文件的转化，而plugin可以做更多的事情，是对webpack的功能的扩展，而不仅局限于资源的加载。[更多插件...](https://webpack.js.org/plugins/)
<img src="/images/webpack/webpack-plugins.png" style="width: 100%;">
示例说明，用extract-text-webpack-plugin将css单独提取出来。
#### devtool
sourceMap：浏览器运行的文件跟源文件是不一致的，所以在调错的时候，需要记录原始代码和生成的代码行数间对应的关系，才能更好的调试，所以sourceMap应运而生。devtool就是为了配置sourceMap的形式。webpack的devtool有七种形式，具体区别比较模糊。详细区分点击[这里](http://www.cnblogs.com/hhhyaaon/p/5657469.html)
#### stats
该选项能让你准确地控制显示哪些包的信息。打包的时候显示打包的相关信息。以下两个配置不同的打包信息。
```js
 stats: {
  assets: false,
  hash: false,
  timings: false,
  version: false,
},
```
<img src="/images/webpack/webpack-stats1.png" style="width: 100%;">
```js
 stats: {
  assets: true,
  hash: true,
  timings: true,
  version: true,
},
```
<img src="/images/webpack/webpack-stats2.png" style="width: 100%;">
#### resolve
设置模块如何被解析。主要讲常用的两个：
##### resolve.extensions
extensions:自动解析确定的扩展, [‘.js’,’.jsx’]可以让我们直接写import ‘a’而不用写import ‘a.js’。
##### resolve.alias
alias: 别名, {common：path.resolve(__dirname, ‘src/common/')}这样我们就可以在任何地方用import common/utils了，不用再去写import ‘../../../common/utils’。
#### target
webpack 能够为多种环境或 target 构建编译，所以我们配置文件没有，因为默认是web，列举几个：
>node：编译为类 Node.js 环境可用（使用 Node.js require 加载 chunk）
webworker：编译成一个 WebWorker
async-node： 编译为类 Node.js 环境可用（使用 fs 和 vm 异步加载分块）

#### externals
外部扩展，webpack 中的 externals 配置提供了「不从 bundle 中引用依赖」的方式。解决的是，所创建的 bundle 依赖于那些存在于用户环境(consumer environment)中的依赖。 官方解释有点复杂，其实简单来说，就是在这里定义的东西，将不再打包到包里面去了，直接在界面上使用script标签引进就可以了，相当于可以在界面上除了module.js外，还可以加载其他的一些library，提高页面的加载效率。如react、reactDOM、jquery等。
#### 总结
上面是webpack配置中主要的几种，在了解了webpack每项配置项的作用后，配置的时候就会显得得心应手，而不至于莫名其妙。[示例地址](https://github.com/vinlyn/webpack-test)
以上如有错误遗漏，望更正补充。








