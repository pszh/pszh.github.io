### 新建页面，新建博文
    1.新建博文
        a. 在source/_posts 文件下放入你的.md 文件，注意title 格式
        
            ---
            title: 个人博客搭建详解（Windows和Mac通用版） # 这是标题
            tags: [eslint,laa]  
            categories:   这里写的分类会自动汇集到 categories 页面上，分类可以多级  
            - 实用技术 # 一级分类
            - 个人博客 # 二级分类 
            ---

    2.新建页面
        a.    hexo new "博客文章文件名"
    
###模版修改发布github
    1.切换本地master 分支
    2.合并源码  git merge source-code
    3.hexo clean && hexo g && hexo d      // g- generate ; d-deploy

### 当前模版使用配置 说明

    https://github.com/yscoder/hexo-theme-indigo/wiki/%E9%85%8D%E7%BD%AE

### github Pages + hexo 搭建个人博客
    http://www.apkbus.com/blog-35555-76924.html
    
    http://threehao.com/2016/08/22/Github%20Pages%20+%20Hexo/
