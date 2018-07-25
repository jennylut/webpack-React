# webpack4.0介绍以及搭建React环境
### 1.创建一个新目录
 ```
 npm init -y   //初始化
 ```
### 2.引入webpack
```
yarn add webpack --save-dev
```
### 3.还需要安装webpack-cli,作为一个单独的包引入：
yarn add webpack-cli --save-dev  
现在打开package.json,并添加一个build（构建）脚本：  
```
"scripts":{
	"build": "webpack"
} 
``` 
执行命令：yarn  build 

说明：从webpack4.0开始，不再必须定义额entry point(入口点)：它将默认为./src/index.js,[webpack官方文档](http://webpack.css88.com/concepts/entry-points.html)  
### 4.创建./src/index.js  
`console.log('hello world') ` 
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

* 打开package.json并填充script部分，如下：
```
"scripts":{
	"dev": "webpack --mode development"
	"build": "webpack --mode production"
}
```
+ 现在尝试运行 yarn  dev,可以在/dist/main.js,没有压缩，
+ 尝试运行 yarn  build,可以在/dist/main.js,代码压缩

## webpack4:用Babel转译ES6

+ webpack不知道如何转换但是有loader(加载器)：将他们视为转译器
+ babel-loader 是一个webpack的loader(加载器)，用于将ES6以及以上版本转译至ES5
+使用loader，需要安装一堆依赖
` yarn add babel-core babel-loader babel-preset-env --save-dev`
+ 接下来，通过在项目文件夹（根目录）创建.babelrc的文件来配置Babel:
```
{
    "presets": [
        "env"
    ]
}
```
webpack4零配置的概念适用于：  
+ entry point(入口点) 默认为 ./src/index.js
+ output(输出) 默认为 ./dist/main.js
+ production(生产) 和 development(开发) 模式 （无需为生产和开发环境创建2个单独的配置）

## webpack 4：通过配置文件使用 babel-loader

+ 创建一个名为 webpack.config.js 的新文件并配置 loader(加载器) ：
```
module.exports = {
  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: {
          loader: "babel-loader"
        }
      }
    ]
  }
};
```
+ 接下来打开./src/index.js 写一些ES6的代码：
```
const arr = [1, 2, 3];
const iAmJavascriptES6 = () => console.log(...arr);
window.iAmJavascriptES6 = iAmJavascriptES6;
```
然后 yarn build 查看一下./dist/main.js来查看转换后的代码。

## 为 React 项目配置 webpack 4

+ 安装React
```
yarn add react react-dom --save-dev
```
+ 接下来添加 babel-preset-react:
```
yarn add babel-preset-react --save-dev
```
+ 在.babelrc中配置预设：
```
{
  "presets": ["env", "react"]
}
```
+ 还可以配置 babel-loader 来读取 .jsx 文件。 如果你的 React 组件 正在使用 jsx 扩展，这将非常有用。
```
module.exports = {
  module: {
    rules: [
      {
        test: /\.(js|jsx)$/,
        exclude: /node_modules/,
        use: {
          loader: "babel-loader"
        }
      }
    ]
  }
};
```
+ 要测试一下你可以在 ./src/App.js 中创建一个测试的 React 组件：
```
import React from "react";
import ReactDOM from "react-dom";
const App = () => {
  return (
    <div>
      <p>Hello React!</p>
    </div>
  );
};
export default App;
ReactDOM.render(<App />, document.getElementById("app"));
```
+ 接下来在 ./src/index.js 中 import(导入) 组件：
```
import App from "./App";
```
然后再次运行 yarn build,可以查看./dist/目录下的内容

## 接下来配置HTML webpack 插件

+ webpack 需要两个额外的组件来处理HTML：html-webpack-plugin 和 html-loader。

```
yarn add npm i html-webpack-plugin html-loader --save-dev

```
+ 然后更新webpack配置：
```
const HtmlWebPackPlugin = require("html-webpack-plugin");
module.exports = {
  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: {
          loader: "babel-loader"
        }
      },
      {
        test: /\.html$/,
        use: [
          {
            loader: "html-loader",
            options: { minimize: true }
          }
        ]
      }
    ]
  },
  plugins: [
    new HtmlWebPackPlugin({
      template: "./src/index.html",  //指向可以自定义
      filename: "./index.html"
    })
  ]
};
```
+ 接下来在 ./src/index.html 中创建一个 HTML 文件：
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="utf-8">
    <title>webpack 4 quickstart</title>
</head>
<body>
    <div id="app">
    </div>
</body>
</html>
```
然后再次执行yarn build 查看./dist文件夹，可以看到生成的html文件，可以直接在浏览器中打开./dist/index.html

## webpack 4：打包css文件
+ 安装mini-css-extract-plugin依赖和css-loader
```
yarn add mini-css-extract-plugin css-loader --save-dev
```
+ 接下来在./src/index.css创建一个css 文件
```
body{
	background: red;
}
```
+ 配置插件和 loader(加载器)：
```
const HtmlWebPackPlugin = require("html-webpack-plugin");
const MiniCssExtractPlugin = require("mini-css-extract-plugin");
module.exports = {
  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: {
          loader: "babel-loader"
        }
      },
      {
        test: /\.html$/,
        use: [
          {
            loader: "html-loader",
            options: { minimize: true }
          }
        ]
      },
      {
        test: /\.css$/,
        use: [MiniCssExtractPlugin.loader, "css-loader"]
      }
    ]
  },
  plugins: [
    new HtmlWebPackPlugin({
      template: "./src/index.html",
      filename: "./index.html"
    }),
    new MiniCssExtractPlugin({
      filename: "[name].css",
      chunkFilename: "[id].css"
    })
  ]
};
```
+ 最后在入口文件./src/index.js 导入index.css文件
```
import App from './app';
import style from './index.css';
```
然后再次执行 yarn build ,浏览器打开./dist/index.html文件

## 配置webpack dev server

+ 使用 webpack 配置开发服务器只需一分钟。
+ 配置好的 webpack dev server 将在浏览器中启动您的应用程序。
+ 每次更改文件时，它都会自动刷新浏览器的窗口。
+ 要设置 webpack dev server，请安装包：
```
yarn add webpack-dev-server --save-dev
```
+ 接下来打开 package.json 并调整 scripts ，如下所示：
```
"scripts": {
    "build": "webpack --mode production",
    "start": "webpack-dev-server --mode development --open"
  },
```
保存，执行yarn start 看到 webpack dev server 在浏览器中启动你的应用程序。端口默认（8080）端口号设置在文件/node_modules/.bin/webpack-dev-server,82行代码const DEFAULT_PORT = 8080;



