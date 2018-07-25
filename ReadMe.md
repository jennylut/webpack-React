# webpack4.0搭建React环境
### 1.创建一个新目录
 npm init -y   //初始化
### 2.引入webpack
yarn add webpack --save-dev
### 3.还需要安装webpack-cli,作为一个单独的包引入：
yarn add webpack-cli --save-dev  
现在打开package.json,并添加一个build（构建）脚本：  
"scripts":{
	"build": "webpack"
}  
执行命令：yarn  build  
//从webpack4.0开始，不再必须定义额entry point(入口点)：它将默认为./src/index.js,[官方文档](http://webpack.css88.com/concepts/entry-points.html)  
### 4.创建./src/index.js  
console.log('hello world')  
然后执行命令：yarn  build  
查看/dist/main.js中的内容  

说明：在 webpack 4 中，既不必须定义 entry point(入口点) ，也不必须定义 output file(输出文件)。  
webpack 的主要优势在于 code splitting(代码拆分）。 但请相信我，使用零配置工具可以加快速度。  
所以这是第一条新闻：webpack 4 不是必须要有配置文件。  
它将查找 ./src/index.js 作为默认入口点。 而且，它会在 ./dist/main.js 中输出模块包。

## webpack4:production(生产)和development(开发)模式

#### 一个典型的项目一般有  
+ 用于开发的配置文件，用于定义webpack dev server 和其他东西
+ 用于生产配置文件，用于定义UglifyJSPlugin,souremaps等

*打开package.json并填充script部分，如下：
```
"scripts":{
	"dev": "webpack --mode development"
	"build": "webpack --mode production"
}
```
+ 现在尝试运行 yarn  dev,可以在/dist/main.js,没有压缩，
+尝试运行 yarn  build,可以在/dist/main.js,代码压缩


