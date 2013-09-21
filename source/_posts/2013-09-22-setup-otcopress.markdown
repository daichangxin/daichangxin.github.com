---
layout: post
title: "配置otcopress博客"
date: 2013-09-22 02:12
comments: true
categories: [otcopress, github]
---
这个配置的过程真是漫长, 特别对于我这个windows加ruby盲的人来说. 起先我用Jellkey来配了一个博客的, 后来感觉好麻烦就不用了, 也就是没坚持去用, 后来转去了点点网, 至少它有代码高亮效果还不错, 可还是想自己倒腾一个正儿八经的技术博客出来, 专门贴一些我在工作中遇到的问题和记录, 毕竟好记性不如烂笔头, 闲话不多说, 记录这次配置otcopress博客的过程.
###安装Git工具
参考官方的配置教程:http://octopress.org/docs/setup/
### 安装ruby
选择1.9.3的版本(官方推荐的, 其他版本没尝试), 按照官方教程一步步安装, 到ruby安装开发组件的时候需要注意一下:

> - 在http://rubyinstaller.org/downloads 中下载Devkit4.5.2版本安装 
> - 通过git bash进入本地的Devkit目录下, 执行安装代码: 
{% codeblock [安装Devkit] %}
ruby dk.rb init
ruby dk.rb install
gem install rdiscount --platform=ruby
{% endcodeblock %}
> - 安装成功后再cd到octopress目录下执行下一步bundle安装命令(在官方教程上都有) 

<!-- more -->
### 安装bundle
可能会遇到rake说版本不是需要的版本的情况, 网上教程一大堆, 而我是直接执行删除命令:
{% codeblock [卸载rake] %}
gem uninstall rake
{% endcodeblock %}
看到不是指定的版本的删掉就成功了, 当然这里还有其他的方法:<http://stackoverflow.com/questions/6080040/you-have-already-activated-rake-0-9-0-but-your-gemfile-requires-rake-0-8-7>
### 中文解码的问题
网上搜了一堆关于ruby语言转码的解决方法, 可是我追踪进去看时原生的代码里本来就是utf-8解码的, 再后来看到一个帖子说cmd/powershell都是默认ansic解码的, 我就尝试用git-bash来调用"rake generate"命令试试, 结果成功了(博客文章是用EditPlus改为了utf-8格式). 对了我还设置了环境变量, 不知道这个是不是也起到了作用:
{% img center images/setup-otcopress/1.png %}
### 代码高亮的问题
我按照网上的教程设置了之后一直提示说无法解析语言, 于是我索性把codeblock里的lang属性去掉正常了, 高亮的代码是:
{% include_code setup-otcopress/embedCode.js %}
当然, 网上还提到一堆其他的代码高亮的方式, 不管了有这个先用着.
### 查看博客
作为配置文件的_config.yml, 每次修改后重新生成需要调用一次命令"rake generate", 单纯博客网站看效果的化使用命令"rake preview"即可用地址http://localhost:4000/ 看到. prew之后写了新博客什么的就不用了, 直接刷新页面就会看到.
### 创建post命令
```
"rake new_post["you blog adress name"]"
```
这个用英文名, 只是地址而已, 默认文章标题也是这个, 更改标题的化就用文本编辑器打开这个博客然后在开头的title属性改掉就可以.
###8 嵌入图片的代码
{% include_code setup-otcopress/embedImage.js %}
center可以替换为left/right, 图片在页面的位置对齐参数. 这里的图片是放在source/images/setup-otcopress目录下, setup-otcopress目录是我自己创建的, 方便管理图片.

Ps:这个语法真是折腾死我了, 看来还是要用习惯才行. 一个不错的在线编辑器: http://benweet.github.io/stackedit/