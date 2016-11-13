# 跨域问题
## 起因
浏览器的同源策略，当前域名下的js只能访问同域。
### 同源定义
如果两个页面拥有相同的协议（protocol），端口（如果指定）和域名（domain，包括主域名和子域名），那么这两个页面就属于同一个源（origin）
### 举例

URL | 是否同源 | 原因
------------ | ------------- | ------------
http://www.a.com/index.html | Yes  | 
http://www.a.com/test/abc.html | Yes  | 
https://www.a.com/index.html | No | 协议不同
http://www.a.com:81/index.html | No | 端口不同
http://news.a.com/index.html | No | 子域名不同

## 解决方案
* CORS
* JSONP
* 服务器代理
* 设置document.domain(主域相同的情况)
* postMessage
* webSocket