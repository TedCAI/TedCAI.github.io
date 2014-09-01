---
layout: post
title: html深入学习(ajax,浏览器兼容性等)
category: 前端
description: ajax,浏览器兼容性等
tags: ["html","ajax"]
---

### 左右侧导航， 顶部导航 ： frame
html布局：
例子http://www.w3school.com.cn/html/html_layout.asp
div应用： display block/none

css中height:100%无效的解决方案
在前端设计中常常会遇到这样的问题：如何让容器高度100%填充整个body。大家都知道简单的给div定义一个height：100%是没有效果的，其实不然。下面我们来分析一下原因：CSS属性是有继承性的，而百分比都又是相对的，那么height：100%就是相当于容器父级而言的。所以并不是这个高度100%没有效果，只是没有达到我们预期的效果，我们理解上的错误。
理解了高度100%的意义，剩下的就简单了，给body一个100%高度即可。

html, body {height: 100%;}
#container {height: 100%;}

### 兼容性问题：

1. 问题：ie7在onfocus()函数里显示不了this.type='password';？使得输入密码框的password不显示星号而显示字符
原因：IE下不支持修改input 的type属性

可以用两个input 一个是text 另一个是password之间的显示与隐藏来实现 这个功能

例子：

```
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN" "http://www.w3.org/TR/html4/strict.dtd"> 
<html> 
<head> 
<meta http-equiv="Content-Type" content="text/html; charset=utf-8" /> 
<title>hehe</title> 

</head> 
<style type="text/css"> 
</style> 
<body> 
<input name="" type="text" value="password" id="tx" style="width:150px;color:#777;"/> 
<input name="" type="password" style="display:none;width:150px;color:#777;" id="pwd" /> 
<script type="text/javascript"> 
var tx = document.getElementById_x_x_x_x_x_x_x_x_x_x_x("tx"), pwd = document.getElementById_x_x_x_x_x_x_x_x_x_x_x("pwd"); 
tx.onfocus = function () { 
	if (this.value == "password") 
	{
		this.style.display = "none"; 
		pwd.style.display = ""; 
		pwd.value = ""; 
		pwd.focus(); 
	}
} 
pwd.onblur = function () { 
	if (this.value == "") 
	{
		this.style.display = "none"; 
		tx.style.display = ""; 
		tx.value = "password"; 
	}
} 
</script> 
</body> 
</html> 
```

2. 新问题：<script>必须放在body里面，放head里不成功

放在head中的JS代码会在页面加载完成之前就读取，而放在body中的JS代码，会在整个页面加载完成之后读取
解决方法：直接在tag里加onfocus，onblur再读取函数吧

HTML里“”中间再加”“不可用，需要加‘’才行？

### jquery基于CGI修改密码：

```
   function submit_changepw()
       {           
         if ($("#newpw").val()== $("#verify_newpw").val()){
                       $("#changepw_alert").hide();
                     $("#changepw_form").attr("action").replace(/^http:/, 'https:');
                  $.post("/cgi-bin/login.cgi",$("#changepw_form").serialize(), 
                           function(retData){
                                       if (retData.status == "Error"){
                                          alert("Error: " + retData.ErrStr);
                                       } 
                                      else if (retData.status == "Ok") {
                                               alert("Change password successfully!");
                                          $('.input').val("");
                                     }
                                        else {
                                           alert("Unknown Error!");
                                 }
                                },"json");
               }
                else{
                    $("#changepw_alert").show();
                     $("#newpw").val(""); 
                   $("#verify_newpw").val("");
              }
      };
```	  
	  
### jquery清空所有input文本框： $('.input').val("");

### button表单提交：<button class="e-btn e-icon" name="LOGIN" type="submit"><span><i class="e-icon e-history e-next"></i></span></button>

### IE7无法获得cookie，firefox和chrome没问题
IE 与FireFox 对Cookie时间的不同理解：
对于IE而言，Cookie的有效时间段是理解为客户端的时间与服务器端时间的间隔，即如果Cookie的时间设置为time()+300，那么在 IE里，Cookie有效的条件是：客户端时间（验证Cookie时间）－服务器端时间（定义Cookie的时间）<300(秒)；所以这种情况下，当我们网站上的验证码脚本被执行时，Cookie就已经过期了，所以验证码失效。
而对于FireFox而言，Cookie的有效时间完全由服务器端的时间决定，以上面的假设为例，即Cookie有效的条件是：服务器端时间T2 （验证Cookie时间）－服务器端时间T1（定义Cookie的时间）<300(秒)。所以在FireFox里，当打开系统登录页时，我输入用户名、密码及验证码的时间肯定不会超过300秒，自然就可以正常登录。Firefox的cookie不依赖于服务器的时间，可见，FireFox是充分考虑到了服务器端与客户端的时差问题。
 
在.38测试时，本地时间为May.20 服务器时间为Mar.27，时间完全不同，cookie肯定过期无法获得
 
### IE cookie 失效情况总结

以前做过的一个应用，因为需要所以改变成跨域的需求。调试完毕之后再火狐下面提交一切ok，但是后来发现在ie下面竟然cookie是失效的

搜索了一些资料。总结了cookie失效的一些原因

1:
开始以为是时区的问题，因为在火狐下面的cookie失效机制和ie不一样，火狐是看服务器的时间-服务器建立时间~~ ie呢是客户端时间-服务器建立cookie时间

所以当服务器和客户端时区设置不一样的时候就会发生cookie设置失效的问题。
2:
还有一种可能，就是setcookie设置不对导致的cookie失效，就是在不添加 path和domain时候，有的时候ie的cookie是失效的，反正我不是这种情况

3：
最后我总结我的环境，是跨域请求的，会不会是jsonp不能设置cookie的，果然是跨域情况下一切皆有可能啊，找好关键字，马上答案就出来了。ie会把跨域设置的cookie禁止掉。所以
在服务端加上一个header(‘P3P: CP=”CAO PSA OUR”‘);
重新试一下。一切ok了。。感谢谷歌，感谢百度


### button上传文件

```
<td width="200px">File path</td>

<td  width="170px">

<span class="e-input"  style="width:170px;">

<input type="text" id="browse_textfield" /> 

</span>

</td>

<td  width="70px">

<button class="e-btn" onclick="document.getElementById_x_x('SWDL_FilePath').click()">

<span>Browse..</span>

</button>

</td>

<td>

<input id="SWDL_FilePath" name="FilePath" type="file" 

onchange="document.getElementById_x_x('browse_textfield').value=this.value"  style="display:none">

</td>

<iframe id="SWDL_Frame" name="SWDL_Frame" style="display:none"></iframe>

```

 

 


### ajax timeout例子的理解：是不是说只要timeout了数据没post成功就执行error函数？

```
$.ajax({

type:"POST",

contentType:"application/json",

url:"../ws/MyService.asmx/test",

data:'{"email":"'+email+'"}',

timeout: 30000,//超时时间：30秒

dataType:'json',

error:function(XMLHttpRequest, textStatus, errorThrown){

//TODO: 处理status， http status code，超时 408// 注意：如果发生了错误，错误信息（第二个参数）除了得到null之外，还可能//是"timeout", "error", "notmodified" 和 "parsererror"。}, 

        success:function(result) {

// TODO: check result}
```
