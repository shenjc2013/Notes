~~~mysql
SELECT *,FROM_UNIXTIME(create_time,"%Y-%m-%d %H:%i:%s") FROM hx_shop_order WHERE uid=10000

SELECT *,FROM_UNIXTIME(create_time) FROM hx_shop_order WHERE uid=10000
~~~



~~~php
LIKE 关键字
小%   ”小“开头，而后面可以是任意字符；//这可用到索引
%小   “小”结尾，前面任意字符；		//全部记录搜索

%小%  表示以上两种的复合表达式；	  //全部记录搜索
~~~

