---
title: '爬取无线电探空仪数据（基于IDL8.2）'
date: 2017-03-08
permalink: /posts/2017/03/2017-03-08-Radiosonde-data-Crawler/
tags:
  - IDL
  - Radiosonde
excerpt_separator: <!--more-->
---

利用IDL8.2自带的IDLnetURL对象实现url访问，并爬取wyoming网址上的无线电探空仪数据！！！
<!--more-->

### 网页请求的基本过程（[HTTP](http://baike.baidu.com/link?url=_WzaPg2C1ZKewGzk2k2ksPoOwI5wRvb8RHBUSEP7HX0WGLJv_fiMZWPn2aLFmC3eestT17hDUlEPTYIsAdLMQq)协议）
在开始之前我们应该了解我们通过浏览器进行网页访问的基本过程，HTTP协议（HyperText Transfer Protocol）是互联网上目前应用最广泛的网络协议，所有的**WWW**文件都必须遵守这一协议。设计的最初目的是为了提供一种发布和接受HTML页面的方法。通俗地解释HTTP传输协议就是客户端（如我们自己电脑的浏览器）发送一个请求，请求里面包含请求的方法、URL、协议版本、以及请求修饰符、客户信息和内容的类似于MIME的信息结构，服务器接受到这个请求，并做出相应的响应，通过连接向客户端传输相关请求数据。  
因此如果我们想要爬取无线电探空仪的数据，我们就需要知道数据所在服务器的URL和相关的请求格式，在得到请求的数据后，我们还需要对数据进行提取，得到我们感兴趣的数据段。

#### 服务器和相关请求格式

怀俄明大学大气科学研究所提供了一个全球无线电探空仪数据的展示平台[这里](http://weather.uwyo.edu/upperair/sounding.html)，通过这个平台我们可以获取全球所有气象站点的无线电探空仪数据。
<div align=center>
  ![website for radiosonde data]({{site.url}}/images/wyoming_sounding.png)
</div>

通过搜索之后跳转的链接，我们很容易知道它的请求格式为:  
<small>
  http://weather.uwyo.edu/cgi-bin/sounding?region=naconf&TYPE=TEXT%3ALIST&YEAR=2017&MONTH=03&FROM=0800&TO=0800&STNM=72672
</small>>

这个请求方式的组成为：<small>  
http://(协议)+  
weather.uwyo.edu(服务器地址)+  
/cgi-bin/sounding?(文件地址)+  
region=naconf(所在洲)+&+  
TYPE=TEXT%3ALIST(请求的文件类型)+&+  
YEAR=2017(年份)+&+  
MONTH=03(月份)+&+  
FROM=0800(起始时间：日期+UTC小时)+&+  
TO=0800(截止时间：日期+UTC小时)+&+  
STNM=72672(站点号)  
</small>

根据这个请求格式我们很容易定义我们需要查询的数据。

#### 服务器响应和返回结果的解析

首先IDL提供了进行网页访问请求的对象[IDLnetURL](http://www.harrisgeospatial.com/docs/IDLnetURL.html)，它支持HTTP和FTP传输协议。我们很容易把自己定义的请求格式通过这个对象发送给服务器并得到相关的访问请求，代码如下

	oURL = Obj_New('IDLnetURL')
    content = oURL->Get(/STRING_ARRAY, URL=URL)

返回的content是一串html文本，里面包含着我们请求的数据

```
<HTML>
<TITLE>University of Wyoming - Radiosonde Data</TITLE>
<LINK REL="StyleSheet" HREF="/resources/select.css" TYPE="text/css">
<BODY BGCOLOR="white">
<H2>57494 ZHHH Wuhan Observations at 00Z 08 Mar 2017</H2>
<PRE>(这里开始)**************
-----------------------------------------------------------------------------
   PRES   HGHT   TEMP   DWPT   RELH   MIXR   DRCT   SKNT   THTA   THTE   THTV
    hPa     m      C      C      %    g/kg    deg   knot     K      K      K 
-----------------------------------------------------------------------------
 1020.0     23    2.8    2.4     97   4.48     25      2  274.4  286.6  275.1
 1000.0    188    8.8    0.8     57   4.07     40      6  281.9  293.5  282.6
  950.2    610    8.1  -15.6     17   1.20     60      4  285.4  289.2  285.6
...
   20.0  26310  -49.5                                     683.9         683.9
   (这里结束)**************
</PRE><H3>Station information and sounding indices</H3><PRE>
...
Precipitable water [mm] for entire sounding: 5.50
</PRE>
<P>Description of the 
<A HREF="/upperair/columns.html">data columns</A>
or <A HREF="/upperair/indices.html">sounding indices</A>.

<P>
<FORM>
...
</SCRIPT>
</BODY> 
</HTML>

```

我们需要从请求返回的html文本中提取我们感兴趣的无线电探空仪数据，通过观察文本我们很容易发现，无线电探空仪的数据是包含在

```
<pre>
	........
</pre>
```
之中的，因此我们可以通过字符匹配找到起始行和终止行从而提取无线电探空仪数据，代码如下：
<body>
  {% highlight ruby %}
 
    lineStart = (Where(Stregex(content, '<PRE>') NE -1))[0] + 1L
    lineEnd = (Where(Stregex(content, '</PRE>') NE -1))[0] - 1L   
    RSData = content[lineStart+4L:lineEnd] 
  {% endhighlight %}
</body>

至此我们就已经完成了无线电探空仪数据的提取，源代码请点击**[这里](https://github.com/ZPYin/Radiosonde)**

注：
源代码中的一些调用函数可以联系作者本人，邮箱：yzp528172875@gmail.com