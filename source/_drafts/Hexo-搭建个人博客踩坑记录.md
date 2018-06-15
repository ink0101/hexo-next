---
title: Hexo 搭建个人博客踩坑记录
tags:
---

### 搭建博客遇到的坑

环境：

* mac
* hexo
* github


2. 修改Git用户名

本地电脑已经配置好了两个账户，其中账号A这是为全局账户，所以默认使用账号A，想要切换到账号B。
通过配置_config.yml文件，添加配置信息：

``` 
deploy:
  type: git
  repo: https://github.com/ink0101/ink0101.github.io.git
  branch: master
  name: ink0101
  email: cftt2016@gmail.com
```
注意：因为之前我已经配置了账号A，所以 .deploy_git 目录已经生成，因此需要删掉整个目录，再执行一次hexo d才行。
MAC环境下.deploy_git是隐藏的，直接执行 rm -rf .deploy_git 删除目录，然后hexo g d。注意这种方式下，之前的提交记录都会消失。

3. 提交Git子模块

fork + subtree 解决

4. 404页面设置

5. 评论系统

https://blog.csdn.net/qwerty200696/article/details/78836421

参考链接： 

[三分钟在GitHub上搭建个人博客](https://zhuanlan.zhihu.com/p/28321740)

[手把手教你用github pages搭建博客 最新版](https://www.jianshu.com/p/6fdb19aa4558)

[我的个人博客之旅：从jekyll到hexo](https://blog.csdn.net/u011475210/article/details/79023429)

[hexo用subtree同步主题](http://tidus.site/2016/01/29/hexo%E7%94%A8subtree%E5%90%8C%E6%AD%A5%E4%B8%BB%E9%A2%98/)

[hexo的next主题个性化教程:打造炫酷网站](http://shenzekun.cn/hexo%E7%9A%84next%E4%B8%BB%E9%A2%98%E4%B8%AA%E6%80%A7%E5%8C%96%E9%85%8D%E7%BD%AE%E6%95%99%E7%A8%8B.html)