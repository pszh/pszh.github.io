---
title: Node 常用命令 与版本控制
tags: [node,npm]
categories: Web
---
### Node 常用命令

清除 node_modules 文件 ：rm -rvf node_modules/

安装模块  

1. 全局安装 npm install -g n   （全局的 npm install -g n@..）
2. 安装到项目中的depend :  npm install express 
3. 安装到项目devD  : npm install express --save-dev

卸载模块：npm uninstall express

更新模块： npm update express

清本地缓存 ：npm cache clear

查看全局安装模块 ：npm list -g

### node 版本控制 n

安装 

 1. 通过npm安装 npm install -g n

	2. 通过源代码

    ```
    $ git clone https://github.com/visionmedia/n.git
    $ cd n
    $ make install
    如果需要配置环境变量 在最后一条命令前加 PREFIX前缀
    $ PREFIX=$HOME make install #将n安装到~/bin/n
    ```

#### 使用和安装node

1. en安装制定版本   n 0.8.17    

2. 卸载  sudo n - 0.8.17

3. 切换版本 

     输入`n`来选择已经安装的node版本，或者通过`^C`取消选择 

   ```
   $ n
       0.8.17
     ο 0.10.26
       0.11.12
   ```

4. 查看所有可安装版本 node ls   (ls是list缩写)