~~~mysql
SELECT *,FROM_UNIXTIME(create_time,"%Y-%m-%d %H:%i:%s") FROM hx_shop_order WHERE uid=10000

SELECT *,FROM_UNIXTIME(create_time) FROM hx_shop_order WHERE uid=10000
~~~



~~~mysql

~~~

