---
title: esLint
---
esLint 介绍

	团队开发	，统一代码风格 

	命令介绍  https://eslint.org/docs/user-guide/command-line-interface

安装 & 初始化配置文件

全局安装

		1. npm install -g eslint

		2. eslint --init

		然后一系列的配置 

本地项目 

		1.  npm install eslint --save-dev

		2.  ./node_modules/.bin/eslint --init  (需要当前项目路径)

		然后一系列配置

配置

    #### eslintrc文件配置

	规则配置文件 ，  通过上面 init 方式配置的都是在项目最外层生成一个配置文件， （当然你也可以在package.json）

	官方文档 https://eslint.org/docs/user-guide/configuring

    {
        "env": {   // 指定环境
            "node": true,
            "commonjs": true,
            "es6": true
        },
        "globals": { //指定全局
            "var1": true,
            "var2": false
        }
        "plugins":{ //配置插件  先通过  npm install --save-dev eslint-plugin-react  最好在init前安装
            "eslint-plugin-react",
        }
        "extends": "eslint:recommended", //扩展配置文件
        "parserOptions": {  //指定解析器选项
            "ecmaVersion": 2016
        },
        "rules": { // 配置规则  https://eslint.org/docs/rules/
            "indent": [
                "error",
                "tab" 使用tab键
            ],
            "linebreak-style": [
                "error",
                "unix"
            ],
            "quotes": [ 
                "error",
                "single" 单引号
            ],
            "semi": [ 
                "error",
                "always" 使用分号
            ]
        }
    }



部分文件忽略检测

 在 eslintrc同目录下创建 .eslintignore 文件

    # /node_modules/* and /bower_components/* 这个是默认忽略的
    
    # Ignore built files except build/index.js  忽略build下的文件除了 index.js
    build/*
    !build/index.js
    # 忽略 dist目录下文件
    dist/*

手动检测代码，自动修复格式化

    eslint --ext src //手动检测src文件夹下的.js   --ext 默认 .js 
    eslint --ext .js .vue src //手动检测src下的 .js .vue 文件
    
    eslint --fix src//修复大部分的格式问题



结合webpack使用

	安装一个eslint-loader 的loader 

    module:{  
    	preLoaders: [ //安全起见用这个preLoaders, 如果用loaders,且使用了babel-loader，那babel-loader必须在eslint-loader之前配置
    			{ test: /\.js$/,  loader: "eslint-loader", exclude: /node_modules/}
    		],  	
    	}
    

git 代码提交前检测

	这个时候不得不提一下git hook了（https://git-scm.com/book/zh/v2/%E8%87%AA%E5%AE%9A%E4%B9%89-Git-Git-%E9%92%A9%E5%AD%90）	

	查看当前目录下的hook文件     cd .git/hooks && ls      

	然后看到 很多.sample 的文件，前面对应git 钩子一些操作， 而 .sample 后缀都是未启动状态，对应的钩子要生效，需要用shell脚本把.sample去掉，如何用js去掉，

	这里才是重点  (⚠️必须在git仓库添加之后)

1. 安装pre-commit   
       npm install pre-commit --save-dev
2. 配置package.json

    第一种//执行静态文件检查
    "scripts": {
        "lint": "eslint --ext .js --cache --fix src", //保证eslint在提交时会校验src目录下的js文件
      },
      "pre-commit": [
        "lint",
      ]
    第二种 //只校验提交代码
    "scripts": {
         "lint": "eslint src --ext .js --cache --fix",
         "pre-lint": "node check.js"//https://www.jianshu.com/p/072a96633479
    },
    "pre-commit": [
         "pre-lint",
    ]



制定自己的规则

https://eslint.org/docs/user-guide/command-line-interface



其他的配置

1. standardjs：https://standardjs.com/readme-zhcn.html     （集成度很好 ，包括git hook）
2. prettier：https://zhuanlan.zhihu.com/p/34188596

