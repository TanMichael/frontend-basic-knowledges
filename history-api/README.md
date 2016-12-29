# History API 之无刷新改变URL

以前一直使用hash和hashchange来实现前端路由。而HTML5的History API看起来更优雅。

## window.history.pushState
它有三个参数：一个状态对象，一个标题（现在被忽略了），以及一个可选的URL地址。

* @状态对象：记录历史记录点的额外对象，可以为空
* @页面标题：目前所有浏览器都不支持
* @可选的url：浏览器不会检查url是否存在，只改变url，url必须同域，不能跨域。如果它是相对的，它一定是相对于当前的URL

> 请注意pushState()方法绝不会导致hashchange 事件被激活，即使新的URL和旧的只在hash上有区别。

## window.history.replaceState
和window.history.pushState类似，不同之处在于replaceState不会在window.history里新增历史记录点。当你为了响应用户的某些操作，而要更新当前历史记录条目的状态对象或URL时，使用replaceState()方法会特别合适。

## window.onpopstate
监听url路径的变化，点击浏览器的前进和后退都会被触发。并且可以获取存储在该历史记录点的状态对象，也就是上文说到的json对象（pushState的第一个参数）
> 值得注意的是：javascript脚本执行window.history.pushState和window.history.replaceState不会触发onpopstate事件。