---
layout: post
title: "Flash RSL bug"
date: 2013-12-11 21:50
comments: true
categories: Flash
---

###运行时共享库

在Flash中, 如果预先载入的包中包含了一个类为"com.greatxin.AClass", 而后来再载入一个类文件, 这个文件中也定义了相同命名空间相同的类型"com.greatxin.AClass", 那么后面的AClass就会被编译器忽略, 在后面的代码执行中都默认所有的"com.greatxin.AClass"都使用最先加载的那个. 这就是运行时共享库的原理. 因此我们可以把共享的资源先加载, 然后在后面使用这些库资源的文件中的相同文件, 就可以直接使用, 比如在库lib.swf中写了一个按钮ButtonSkin, 在BagUI中动态引用lib里的这个按钮, 在发布BagUI的时候bagUI.swf是不会把这个外部链接类打包到bagUI中去的, 这就导致如果你单独使用bagUI会因为找不到ButtonSkin的定义而报错, 而在程序中我们先加载lib, 就不会有这个问题. 
###制作动态链接库:

 1. 所有swf导出swc, 然后在项目中将这些swc全部设为外部引用而不编译到Main文件中, 这样的好处是我们在ide中可以看到代码提示. 坏处是使用了lib库中的文件无法命名, 命名后导出报错, 除非不使用, 而是在程序中自己调用lib资源然后手动和bag资源整合. 另外会出现很多提前访问到资源类定义而实际上资源并没加载时的运行时错误.
 2. 只导出swf, 载入后直接从载入swf的域中获取类的定义来使用, 这样就没有代码提示了.

其实这两种没啥区别, 但是都有相同的bug(如果使用动态链接库共享, 不使用共享而是各个文件各管各的然后在程序中使用swc自己整合的不算)
####BUG

在bagUI.fla中使用lib的链接库定义时(前提这个定义比如ButtonSkin必须在属性中设置为共享导出lib.swf), Main文件在运行时会以Main的目录为根目录搜索lib.swf文件, 如果没有该文件即便编译通过也会发现什么都没有, 即便你从其他位置加载了lib.swf文件, 然后再加载使用bagUI.swf也会发现, 什么都没有, new出来也是空空的在舞台上. 

解决这个问题, 就需要在Main的同级目录下放一个lib.swf文件, 这个文件不必必须是共享库的lib.swf, 而是随便一个空的lib.swf, 就好像这个在Main.swf同级目录下的lib.swf是个钥匙一样, 有了它才能让bagUI知道, 它是可以使用共享库的(虽然共享库其实是从其他目录下加载的lib.swf, 而不是跟Main.swf同级下的). 当然你也可以直接把Main.swf跟共享库lib.swf放同目录下, 不过按照一般的习惯来说, 我们还是不习惯把一大堆的资源文件跟Main文件放在同一个目录下. 

例子代码:https://github.com/daichangxin/AS3RSLTest