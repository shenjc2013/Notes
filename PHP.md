##### PHP常用知识

###### 1.1 IP地址查询

**国内免费**

1、淘宝，不稳定，有次数限制，经常404

~~~
http://ip.taobao.com/service/getIpInfo.php?ip={IP地址}
~~~

2、搜狐，当能访问当前IP，没什么作用

~~~
http://pv.sohu.com/cityjson?ie=utf-8
~~~

3、百度

~~~php
https://sp0.baidu.com/8aQDcjqpAAV3otqbppnN2DJv/api.php?query={IP地址}&resource_id=6006&oe=UTF-8

/**返回格式*/
{"status":"0","t":"","set_cache_time":"","data":[{"location":"广东省广州市 电信","titlecont":"IP地址查询","origip":"61.140.178.112","origipquery"....
~~~

4、126

~~~php
http://ip.ws.126.net/ipquery?ip={IP地址}

/*返回格式$ipInfo = 'var lo="广东省", lc="广州市"; var localAddress={city:"广州市", province:"广东省"}';*/

$ipInfo = httpGet('http://ip.ws.126.net/ipquery?ip=' . $ip);
$ipInfo = iconv("gb2312", "utf-8//IGNORE",$ipInfo);
$ipInfo = str_replace('city', '"city"', $ipInfo);
$ipInfo = str_replace('province', '"province"', $ipInfo);

//处理成json格式
preg_match_all("/(?:\{)(.*)(?:\})/i", $ipInfo, $match);
$ipInfo = $match[0][0];
$ipInfo = json_decode($ipInfo);
if ($ipInfo->city && $ipInfo->province) {
	$country = '中国'; $province = $ipInfo->province; $city = $ipInfo->city;
}
~~~

5、太平洋

~~~php
http://whois.pconline.com.cn/?ip={IP地址}
内容太多，再框框里有其他接口请求

http://whois.pconline.com.cn/ipJson.jsp?ip={IP地址}
/** 返回格式 */
if(window.IPCallBack) {IPCallBack({"ip":"61.140.178.112","pro":"广东省","proCode":"440000","city":"广州市"...
~~~



**国际免费**

~~~php
http://ip-api.com/json/　　# 国际化英文显示

http://ip-api.com/json/?lang=zh-CN　　# 中文显示

http://ip-api.com/json/{IP地址}?lang=zh-CN　　# 查询某个ip的信息

/** 返回格式 */
{"status":"success","country":"中国","countryCode":"CN","region":"GD","regionName":"广东","city":"广州",...
~~~



###### 1.2 Phpstorm

> phpstorm软件，代码右键 deployment 灰色，不可点击

~~~php
#参考文章
# https://blog.csdn.net/u013249853/article/details/89525486
~~~

解决办法：

<img src="H:\笔记本\PHP.assets\image-20200702104351733.png" alt="image-20200702104351733" style="zoom:90%;float:left;" />



###### 1.3 Composer镜像源

~~~php
配置阿里云composer镜像
https://developer.aliyun.com/composer
~~~

