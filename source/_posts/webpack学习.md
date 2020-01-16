---
title: webpack
tags: [webpack]
categories: Web,打包工具
---

## webpack 基础篇

    ### 安装本地webpack

- webpack webpack-cli -D

​ [https://www.webpackjs.com/concepts/#%E6%8F%92%E4%BB%B6-plugins-](https://www.webpackjs.com/concepts/#插件-plugins-)

- entry

- output

- loader

- plugins

- mode

- resolve https://webpack.js.org/configuration/resolve/#root

  ```js
  resolve:{
    alias: { // 别名，  import a from './src/utils/a.js' 简写 import a from 'utils/a.js'
      utils: path.resolve(__dirname, 'src/utils/'),
      Templates: path.resolve(__dirname, 'src/templates/')
    }
  	extensions: ['.js', '.vue', '.css', '.json'] // 引入文件时候没有后缀名会按照这个次序去找
  }

  ```

### 开发服务器配置 webpack-dev-server

​ 原理：启动了一个 express 的静态服务

```js
//更多配置 https://webpack.js.org/configuration/dev-server/
devServer:{
	port:3000, //默认是8080
  progress:true,
  contentBase:'./build',  //以这个文件作为目录去启动静态服务
  proxy: { //接口的代理设置
    '/api': {
      target: 'http://localhost:3000',
        pathRewrite: {'^/api' : ''}
    }
  }
}
```

使用插件 htmlwebpackPlugin 去自动引用模版

### htmlwebpackPlugin

- https://webpack.js.org/plugins/html-webpack-plugin/#root

- 配置 ：https://github.com/jantimon/html-webpack-plugin#options
- 基本使用

```js
new HtmlWebpacklugin({
  template: "./src/index.html",
  filename: "index.html", //输出后的文件名
  minify: {
    // 在 mode:'production', 时候压缩html配置
    removeAttributeQuotes: true, //双引号
    collapseInlineTagWhitespace: true //压缩成一行
  }
});
```

### loader 非 js 文件的处理 , 高级 js 语法转化

#### css 处理

```js
//loder的解析

module:{ //  模块
  rules:[ //规则 css-loader 接续 @import这种语法
  	// style-loader 把css插入到head的标签中
  	//loader 特点: 功能单一； 用法 ：一个loader用字符串，多个[]；顺序：默认从右往左
			{
        test: /\.css$/,
        use: [
          { loader: "style-loader", options: { insertAt: "top" } }, //传对象这样写， inserAt css插入的位置
          "css-loader"
        ]
      }
  ]
}

// mini-css-extract-plugin 的单独打包
// https://webpack.js.org/plugins/mini-css-extract-plugin/#root
plugins: [
  new MiniCssExtractPlugin({
    filename: 'main.css',
  }),
],
module:{
	rules:[
    {
        test: /\.css$/,
        use: [
					MiniCssExtractPlugin.loader,
          'css-loader',
					'postcss-loader'//浏览器前缀 postcss-loader ,autoprefixer
        ]
    }
  ]
}

// 打包后css压缩，optimize-css-assets-webpack-plugin, js压缩是 mode :"production" //https://www.npmjs.com/package/optimize-css-assets-webpack-plugin
plugins:  [
    ...
		new optimizeCss({})
]
```

#### es6 转 es5

babel-loader

@babel/core : babel 的核心方法，可以调用 transform

@babel/preset-env ：如何转化 es6=》es5 （ 按需编译和按需打补丁）

```js
{
  test: /\.js$/,
    use: {
      loader: "babel-loader",
        options: {
          // 用babel-loader 把es6-es5
          presets: ["@babel/preset-env"]
        }
    }
  include:path.resolve(__dirname,'src'),
  exclude:/node_modules/
}
```

//其他插件可以去 babel 官网查看

@babel/plugin-transform-runtime：是对 Babel 编译过程中产生的 helper 方法进行重新利用(聚合)，以达到减少打包体积的目的。此外还有个作用是为了避免全局补丁污染，对打包过的 bunler 提供"沙箱"式的补丁。配合产线的 @babel/runtime （放生产包，安装不要加 —D）

polyfill(弃用)：core-js + regenerator

core-js：实现 JavaScript 标准运行库之一，它提供了从 ES3 ～ ES7+ 以及还处在提案阶段的 JavaScript 的实现

<a href="https://juejin.im/post/5cb9833b6fb9a068a84fe4d0#heading-27">babel 社区介绍</a>

#### esLint 代码规范引用

安装 eslint, aslant-loader

```js
rules: [
  {
    test: /\.js$/,
    use: {
      loader: "eslint-loader",
      options: {
        enforce: "pre" // loader默认从右到左，下到上， pre:提前执行， post：往后
      }
    }
  }
];
```

#### 图片处理

```js
// 三种 打包图片
// 1)在js中创建图片
import image from "./image.jpg"; //引入图片，返回一个新地址 （file-loader）为了可以根据图片大小把图片编译成base64 所以用url-loader
let img = new Image();
img.src = image;
document.body.appendChild(img);

//webpack.config中配置
{
 test: /\.(png|jpg|gif)$/,
   // 做一个限制，图片小于 多少k时候，用base64来转化，其他用真实图片
   // 否则用file-loader产生真实图片
   use: {
     loader: "url-loader",
       options: {
         limit: 10 * 1024
       }
   }
},


// 2）在css引入background（'url'） // css-loader会操作
background-image: url("./image.jpg");
// 3) <img src="./image.jpg" /img> //使用html-withimg-loader 注意 HtmlWebpacklugin 中的 minify配置就不能使用了,目前打包的时候 src 后面打包成对象了，还是有问题
{
 test: /\.html$/,
   use: "html-withimg-loader"
},

```

### 全局变量

1） expose-loader 暴露到 window 上

2)providePlugin 给每个人提供一个\$

场景：在模块中不用引入 jqury 包就可以使用\$

webpack 配置：

```js
const webpack = require("webpack");
new webpack.ProvidePlugin({
  $: "jquery"
});
```

3. 引入不打包 cdn 在 html 中引入

### 文件分类

在 mode:"production"，其他不要加

1. 图片

```js
{
  test: /\.(png|jpg|gif)$/,
    // 做一个限制，图片小于 多少k时候，用base64来转化
    // 否则用file-loader产生真实图片
    use: {
      loader: "url-loader",
        options: {
          limit: 10 * 1024,
            outputPath:'img/' //打包路径
        }
    }
},
```

2） css

```js
new miniCssExtractPlugiin({
      filename: "css/main.css"
    }),
```

3. cdn 资源引入

```js
output: {
    path: path.resolve(__dirname, "dist"),
    filename: "output[hash:8].js", //使用hash值  "output[hash:8].js"
    publicPath:'http://www.pszh.com', //给打包后的js,css,image图片都加上cdn路径
  },


 // 如果只想给图片添加
  {
    test: /\.(png|jpg|gif)$/,
      // 做一个限制，图片小于 多少k时候，用base64来转化
      // 否则用file-loader产生真实图片
      use: {
        loader: "url-loader",
          options: {
            limit: 10 * 1024,
              outputPath:'img/', //打包路径
              publicPath:'http://www.pszh.com',
          }
      }
  },
```

## 配置篇

### 打包多页应用

```js
 entry: {
    home: "./src/home.js",
    other: "./src/other.js"
  },
  output: {
    //[name]表示变量 home ,other. entry的key值
    filename: "[name].js",
    path: path.resolve(__dirname, "dist")
  },

   //打包出两个html文件
   plugins: [
      new htmlwebpackPlugin({
        template: "./src/index.html",
        filename: "home.html",
        //为了打包的时候只打包home.js进去
        chunks: ["home"]
      }),
      new htmlwebpackPlugin({
        template: "./src/index.html",
        filename: "other.html",
        //为了打包的时候把home.js，other.js都打进去
        chunks: ["home", "other"]
      })
  ]
```

### 配置 source-map

​ 源码映射

```js
 //1)源码映射， 单独生成一个.map文件，出错会定位到报错的行和列   文件大且全
  //devtool: "source-map",
  // 2) 不会生产单独文件，可以显示行和列
  devtool: "eval-source-map",
  //3) 不会产生列，但是是一个单独映射文件
  // devtool: "cheap-module-source-map", //产生后保留起来
  // // 4) 不会生成文件， 继承后可以在文件中，不会产生列
  // devtool: "cheap-module-eval-source-map",
```

### watch 用法

​ 修改代码，实时打包。编译

​ webpack-dev-server：默认 watch 开启，为 true，

```js
watch: true,
watchOptions: {
  poll: 1000, // 每秒检测多少次改变
  aggregateTimeout: 500, // 防抖 毫秒之后在执行
  ignored: /node_modules/ //忽略文件
},
```

### 小插件应用

#### clean-webpack-plugin :清空之前的打包目录文件

```js
let { CleanWebpackPlugin } = require("clean-webpack-plugin");
new CleanWebpackPlugin();
// new CleanWebpackPlugin("./dist")
```

#### copy-webpack-plugin ：指定目录下的文件打包到 dist 文件下 ，如 public

```js
let copyWebpackPlugin = require("copy-webpack-plugin");
new copyWebpackPlugin([{ from: "./public", to: "./" }]); //把public目录中的文件copy到dist下，
// new copyWebpackPlugin([{ from: "./public", to: "./public" }]) //把public目录中的文件copy到dist下的public文件夹下，
```

### 跨域问题

```js
// 1）
devServer: {
 proxy: {
   '/api': {
     target: "http://localhost:3000", // 遇到 ‘/api’的代理到这个host上
     pathRewrite: { "/api": "" } //重写，把请求路径中的/api=> ''
   }
 }

  // //2） mock数据的 webpack-server-dev就是一个express服务，所以
    // before(app) {
    //   app.get("/user", (req, res) => {
    //     res.json({ name: "change All" });
    //   });
    // }
}


// 3)通过在express node服务上直接启动webpack, 端口用的就是webpack的端口  express的中间件  webpack-dev-middleware



```

## 优化篇

1. noparse :打包时候不用在继续查找依赖看他是否有依赖

   ```js
   module.exports = {
     //...
     module: {
       noParse: /jquery|lodash/
     }
   };
   ```

2) rules:中 exclude,include

   ```js
   module.exports = {
     //...
     rules: [
       {
         test: /\.js$/,
         exclude: /node_modules/,
         include: path.resolve("src")
       }
     ]
   };
   ```

3) IgnorePlugin :导入模块中多余模块的剔除

   ```js
   //如moment引入了很多语言包，先去掉所有的语言包
   plugins:[
   	new webpack.IgnorePlugin(/\.\/locale/, /moment/);
   ]

   //然后在使用的地方要手动引入语言
   import 'moment/locale/zh-cn';
   moment.locale('zh-cn');
   ```

4. dllPlugin ==》 DllReferencePlugin: 拆分

5. happypack 多线程打包 //项目小不要用，

   ```js
   let happypack = require('happypack')
   //...
   module:{
     rules:[
         //...
         {
           test: /\.js$/,
           include: path.resolve(__dirname, "src"),
           exclude: /node_modules/,
           use: 'happypack/loader?id=js'
         },
       	{
           test: /\.css$/,
           use: 'happypack/loader?id=css'
         }

     ]
   }
   plugins:[
     new happypack({
       id:'js',
   		use:[
         {
           loader:'babel-loader',
           options:{
              // 用babel-loader 把es6-es5
               presets: ["@babel/preset-env"]
           }
         }
       ]
     })
     new happypack({
       id:'css',
   		use:[
         {
           loader:'style-loader,css-loader',
         }
       ]
     })
     //...
   ]
   ```

6. 抽离公共代码

   ```
   optimization:{
   	splitChunks:{ // 分割代码块
   		cacheGroups:{ // 缓存组

   			common:{ //公共的模块 的抽离
   				chunks:'initial',// 开始入口的时候抽离 ，
   				minSize:1, // 文件大小大于多少字节抽离，
   				minChunks:3 // 使用次数大于多少时候抽离
   			},

   			vendor:{ //第三方模块的抽离
   				priority:1, // 提升优先级（公用模块应用了第三方东西）
   				test:/node_modules/, // 把你抽离出来
   				chunks:'initial',
   				minSize:1,
   				minChunks:3
   			}
   		}
   	}
   }
   ```

7) webpack 自带优化

   1. 用 import 在生产模式下，会自动去掉没用的代码， tree-shaking: 把没用的代码，自动剔除 require 引入不行

   2. scope hosting 作用域提升 ：在 webpack 中自动把一些代码简化了

      ```js
      let a = 1;
      let b = 2;
      let c = a + b; //打包时候会直接c =3,上面的a，b去掉了
      ```

## 懒加载

```js
//es6草案中的语法
import("./utils//test.js").then(data => {
  console.log(data.default);
});
```
