如果服务器没有明确定义缓存控制，不同的客户端可能会有不同的缓存机制。
如果你希望用户每次获取最新数据可以这样设置 
meta tag ：
<meta http-equiv="cache-control" content="max-age=0" />

<meta http-equiv="cache-control" content="no-cache" />

<meta http-equiv="expires" content="0" />

<meta http-equiv="expires" content="Tue, 01 Jan 1980 1:00:00 GMT" />

<meta http-equiv="pragma" content="no-cache" />

设置meta tag只对html页面有效，更好的方法是在 http 响应头中设置缓存控制：

Cache-Control: no-cache, no-store, must-revalidate

Pragma: no-cache

Expires: 0

php设置 http 响应头的代码：

header("Cache-Control: no-cache, no-store, must-revalidate"); // HTTP 1.1.

header("Pragma: no-cache"); // HTTP 1.0.

header("Expires: 0"); // Proxies.

使用缓存有利于加快页面加载速度和减少资源消耗，你应该根据具体情况调整缓存策略。

