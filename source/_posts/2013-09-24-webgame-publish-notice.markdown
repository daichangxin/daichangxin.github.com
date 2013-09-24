---
layout: post
title: "网页游戏平台对接事项"
date: 2013-09-24 14:28
comments: true
categories: [webgame, 网页游戏, 平台对接]
---
之前没有负责过网页游戏跟游戏平台的对接, 这次公司游戏发布所以这里记录一下对接的过程, 服务端的我只能说个大概的流程, 主要还是说客户端部分.

##客户端页面编写

这个页面的代码大部分是可以通用的, 一般有以下几个功能:

###1 传入客户端需要的数据

客户端一般需要服务器的地址和端口用来连接服务器, 玩家的id用来登入游戏服务器, 充值入口, 静态资源的地址等

###2 嵌入一个游戏壳子的swf

用来加载主程序文件, 毕竟主程序文件是按照版本号加载的, 壳子文件是永远随机参数加载

###3 浏览器尺寸等一些函数

当浏览器尺寸放生变化时游戏内的ui和面板的位置可能会根据需要发生变化, 这就需要用js脚本来获取浏览器的尺寸和变化函数传递给swf. 其他还有禁用网页滑动, 网页关闭时弹出的游戏内容未完成任务提示等.
<!-- more -->
### 例子:
```html
<?php
$user = $_GET["username"];
$server = $_GET["serverId"];
$rand = rand();
?>

<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="utf-8"/>
	<meta http-equiv="content-type" content="text/html;charset=utf-8" />
	<meta name="description" content="" />
	
	<script src="http://static.xxx.com/js/swfobject.js"></script>
	<script src="http://static.xxx.com/js/jquery-1.9.1.min.js"></script>
	<script>
		var flashvars = {
			user:"<?php echo $user ?>", //用户id
			server:"s" + "<?php echo $server ?>" + ".game.xxxx.com",
			port:"6666",
			payURL:"http://apiport.xxx.com/redirect/pay?game=234&sid=" + "<?php echo $server ?>", //充值地址, 跟serverid对应
			resource:"http://static.xxx.com/"//资源地址
		};
		var params = {
			menu: "false",
			scale: "noScale",
			allowFullscreen: "true",
			allowScriptAccess: "always",
			bgcolor: "",
			wmode: "direct" // can cause issues with FP settings & webcam
		};
		var attributes = {
			id:"Sango"
		};
		swfobject.embedSWF(
			"http://static.xxx.com/GameLoader.swf?" + "<?php echo $rand ?>", 
			"altContent", "1250", "650", "10.0.0", 
			"http://static.xxx.com/expressInstall.swf", 
			flashvars, params, attributes);

		$(window).resize(sendToSWFResize);
		function sendToSWFResize()
		{
			var windowW = $(window).width();
			var windowH = $(window).height();
			if (navigator.appName.indexOf("Microsoft") != -1)
			{
				window["Sango"].onWindowResize(windowW, windowH);
			}
			else
			{
				document["Sango"].onWindowResize(windowW, windowH);
			}
		}
	</script>
	<style>
		html, body { height:100%; overflow:hidden; }
		body { margin:0; background:#000;}
	</style>
</head>
<body>
	<table border="0" width="100%" height="100%">
		<tr><td align="center" valign="middle">
		<div id="altContent">
			<h1>Sango</h1>
			<p><a href="http://www.adobe.com/go/getflashplayer">Get Adobe Flash player</a></p>
		</div>
	<td><tr>
	</table>
</body>
</html>
```

##服务器需要做的工作

###1 充值接口

需要根据平台的api将冲入的rmb转换为游戏币.
###2 用户接入接口

在跳转到游戏界面时要根据已登录玩家的id编写一个webservice来获取玩家的基本信息, 注册到游戏服务器.

##客户端对接平台页面

一般网页游戏平台页面都是固定的框架, 整个页面的内容都是平台的广告, 边栏标题内容等, 在内容的地方会预留一个iFrame, 这个就是要嵌入游戏开发商提供的游戏真实地址, 在嵌入这个游戏地址的时候会用GET的方式传入一些游戏中必须的参数, 比如刚才说的需要的玩家id, 服务器地址, 充值地址等. 这部分需要和平台商量去提供. 一般嵌入类似
```
<iframe id="ifm" scrolling="auto" frameborder="0" marginwidth="0" marginheight="0" height="100%" width="100%" src="http://yourgameplatform.com/api/login.php?account=zhangsan" border='0'>
</iframe>
```
所以在游戏开发商提供的游戏页面里只需要通过$GET[""]方法就可以拿到需要的数据然后进行下一步操作, 比如写入flashvars中, 好传递给swf.

##跨域的问题

这个很常见, 毕竟一般游戏开发商提供资源服务器, 而平台只提供游戏服务器, 所以页面在连接socket时会出现swf跨域不能连接的情况. 这个时候需要服务器另外写一个程序, 这个程序可以一劳永逸写一份丢在游戏服务器上, 它的功能就是接收到了flash发来的843端口连接就直接丢一个crossdomain的策略文件过去, 这样就解决了跨域而无法连接的问题, 跟游戏服务器完全没有任何关系.

对接大概就这些了, 还有一些注意的地方: 资源服务器要加cdn, 资源不能和游戏服务器放一起, 不然游戏服务器带宽会被资源下载卡的不能连接.