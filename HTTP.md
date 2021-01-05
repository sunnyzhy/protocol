# HTTP
## keep-alive
在早期的 HTTP/1.0 中，每次 http 请求都要创建一个连接，而创建连接的过程需要消耗资源和时间，为了减少资源消耗，缩短响应时间，就需要重用连接。在后来的 HTTP/1.0 中以及 HTTP/1.1 中，引入了重用连接的机制，就是在 http 请求头中加入 **Connection: keep-alive** 来告诉对方这个请求响应完成后不要关闭，下一次还用这个请求继续交流。协议规定 HTTP/1.0 如果想要保持长连接，需要在请求头中加上 Connection: keep-alive ，而 HTTP/1.1 默认是支持长连接的，有没有这个请求头都行。

当然了，协议是这样规定的，至于支不支持还得看服务器（比如 tomcat ）和客户端（比如浏览器）的具体实现。在实践过程中发现谷歌浏览器使用 HTTP/1.1 协议时请求头中总会带上 Connection: keep-alive ，另外通过 httpclient 使用 HTTP/1.0 协议去请求 tomcat 时，即使带上 Connection: keep-alive 请求头也保持不了长连接。如果 HTTP/1.1 版本的 http 请求报文不希望使用长连接，则要在请求头中加上 Connection: close ，接收到这个请求头的对端服务就会主动关闭连接。

但是 http 长连接会一直保持吗？肯定是不会的。一般服务端都会设置 keep-alive 超时时间。超过指定的时间间隔，服务端就会主动关闭连接。同时服务端还会设置一个参数叫最大请求数，比如当最大请求数是 300 时，只要请求次数超过 300 次，即使还没到超时时间，服务端也会主动关闭连接。

## Transfer-Encoding
最新的 HTTP 规范里，只定义了一种传输编码：Transfer-Encoding: chunked（分块编码）。

Transfer-Encoding 不建议在请求头中使用，因为无法知道服务端能否解析这个请求头，而应该在响应头中使用，因为客户端浏览器都能解析这个响应头。

## Content-Length
Content-Length 在请求方法为 GET 的时候不能使用，在请求方法为 POST 的时候需要使用，同时也常常出现在响应头中。

## 状态码
||类别|描述|
|--|--|--|
|1xx|Information|指示信息--表示请求已接收，继续处理|
|2xx|Success|成功--表示请求已被成功接收、理解、接受|
|3xx|Redirection|重定向--要完成请求必须进行更进一步的操作|
|4xx|Client Error|客户端错误--请求有语法错误或请求无法实现|
|5xx|Server Error|服务器端错误--服务器未能实现合法的请求|
