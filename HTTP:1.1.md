[HTTP/1.0 RFC 1945](https://tools.ietf.org/html/rfc1945)
[HTTP/1.1(old) RFC 2616](https://tools.ietf.org/html/rfc2616)
[HTTP/1.1(new) 7230-7235](https://tools.ietf.org/html/rfc7230)。


## 7230 Message Syntax and Routing

**URL 和 URI 的区别**

* URI（Uniform Resource Identifier）统一资源定位符，包括文件、服务等
* URL (Uniform Resource Location) 资源所处位置

中间媒介：代理，网关，通道
Intermediaries: Proxy, Gateway, tunnel

HTTP 建立在 TCP/IP 之上，HTTPS 基本上就是比 HTTP 多了个 TLS。

HTTP 1.0 多数情况下，一个连接只能处理一次请求响应；HTTP 1.1 则有可能一个连接处理多个请求响应。

**BNF**：backus naur form [巴科斯诺尔范式]

HTTP 的版本号遵循："<major>.<minor>" 格式。

HTTP 请求和响应唯一的区别就在于开始行（start-line）的不同，请求以 “request-line” 开始，响应以 “status-line” 开始。


规格建议请求行的最小支持长度设置为：8000，单位 octets


头部字段名和冒号之间不能有空格，冒号后可以跟可选的空格，再跟字段值，以方便阅读。

`Transfer-Encoding: gzip, chunked` 表明消息体通过 gzip 压缩并且分块传输。

[**`Transfer-Encoding` 和 `Content-Encoding` 的异同**](http://blog.csdn.net/swt369/article/details/77847896).

* Transfer-Encoding：用于指定传输报文主体时使用的编码方式，属于逐跳首部，即只在两个节点间有效。
* Content-Encoding：用于指定报文主体已经采用的编码方式，属于端到端首部，即在整个传输过程中有效。

**Content-Length**: 如果服务器响应中设置了 Transfer-Encoding 头字段，则不能再设置 Content-Length 头字段。

> Page 29-33 详细描述了 `content-length` 头字段的使用 case

**Transfer-Encoding** 可选取值有

* "chunked"
* "compress"
* "deflate"
* "gzip"
* "transfer-extension"

request-target = origin-form
/ absolute-form                   
/ authority-form                   
/ asterisk-form

hop-by-hop: 逐跳
end-to-end: 端到端

`keep-alive` 来自 HTTP/1.0，表示当前连接是持久连接，而 HTTP/1.1 的连接默认是持久连接，除非显式通过 `connection: close` 关闭。

后向兼容：（backwards compatibility）

本季涉及到的首部子字段

* Connection
* Content-Length
* Host
* TE
* Trailer
* Transfer-Encoding
* Upgrade
* Via
* Close

Encoding considerations:  only "7bit", "8bit", or "binary" are permitted

IESG: 互联网工程指导委员会(Internet Engineering Steering Group)

CRLF: 回车换行（carriage return/line feed）


当前阅读进度[Page 69]


