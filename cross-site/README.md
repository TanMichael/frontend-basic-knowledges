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

### CORS
CORS背后的思想，就是使用自定义的HTTP头部让浏览器与服务器进行沟通，从而决定请求或响应是应该成功，还是应该失败。
通过服务器设置响应头“Access-Control-Allow-Origin”为当前域或“*”。

### JSONP
JSONP是JSON with padding的缩写。利用&lt;script&gt;标签的src属性不受同源策略限制的特性，通过创建一个&lt;script&gt;标签发起***get***请求，后端返回一个函数调用（之前已在全局注册）实现。

	function createNewRandomFun() {
		// return a random string for temp global function name
	}
	
	var tempName = createNewRandomFun();
	window[tempName] = function() {
		// logic to handle server data
		
		// clear itself
		delete window[tempName];
	}
	var oScript = document.createElement("script");
	oScript.src = "www.b.com/json?callback=" + tempName + "&param1=123";
	document.body.insertBefore(script, document.body.firstChild);

### 服务器代理
这个方案中心思想是在页面发送请求给本域服务器的一个代理脚本，代理脚本来完成对它域的请求，然后把结果返回给客户端。它的优点是可以完全模拟请求方法（无论GET还是POST），并把参数和客户端Cookies传递过去。
这个方案能完成Jsonp不能完成的***Post***请求，但是实施起来比Jsonp麻烦的多。

### document.domain
在同源策略中有一个例外，脚本可以设置 document.domain 的值为当前域的一个后缀，如果这样做的话，短的域将作为后续同源检测的依据。
例如，假设在http://store.b.com/dir/other.html 中的一个脚本执行了下列语句：

	document.domain = "b.com"

浏览器单独保存端口号。任何的赋值操作，包括document.domain = documen.domain都会以null值覆盖掉原来的端口号。因此b.com:8080页面的脚本不能仅通过设置document.domain = "b.com"就能与b.com通信。赋值时必须带上端口号，以确保端口号不会为null。

### postMessage
Html5引入的message API。 可以更方便、有效的解决：

1. 页面和其新打开的串口之间的数据传递
2. 多窗口之间的消息传递
3. 页面与嵌套的iframe之间的消息传递
4. 以上三个问题的跨域数据传递

API

	otherWindow.postMessage(message, targetOrigin, [transfer]);
	
Example

	
	// other window could "var popup = window.open(...popup details...);", iframe.contentWindow
	var popup = window.open(...popup details...);
	popup.postMessage("hello there!", "http://b.com");
	
	// in other window b.com
	window.addEventListener('message', function(event){
        // 通过origin属性判断消息来源地址
        if (event.origin == 'http://a.com') {
            alert(event.data);    // 弹出"hello there!"
            alert(event.source);  // 对a.com、index.html中window对象的引用
        }
    }, false);

### WebSocket
Native WebSockets are cross-domain by design. WebSocket默认不支持同源策略。
[but browsers will send an origin header that contains the hostname of the server that served the HTML with the JS that opened the WebSocket connection. A WebSocket server can then restrict access by checking origin.](http://stackoverflow.com/questions/23674199/why-is-there-no-same-origin-policy-for-websockets-why-can-i-connect-to-ws-loc#comment36372038_23674863)
