## 响应状态码

### 基本介绍

状态码由三位整数组成，表示服务器尽可能理解和完成客户端请求时的响应状态。

HTTP 状态码是可扩展的。客户端不需要识别所有已声明的状态码，当然如果能够做到这一点就更完美了。但是呢，客户端必须识别状态码的任何一个基本类别，也就是三位整数的第一位（用以表明状态码基本类型）。对于不能识别的状态码，则按照 x00 来对待（x 保持对应基本类型不变），此外，不能识别的状态码对应的数据也是不允许缓存的。

例如，如果客户端接收到一个不能识别的 471 状态码，则客户端就可以假定是请求数据错误，并将该状态码当做 400 状态码来对待。通常情况下，服务器端响应会包含对应状态的解释信息。

状态码的第一位数字用来确定响应类型。另外两位数字暂时还未进行分类。目前为止，第一位数字有5种取值：

* 1xx (信息)：请求已收到，需要请求者继续执行操作
* 2xx (成功)：请求已成功收到理解并接受
* 3xx (跳转)：需要进一步的操作以完成请求
* 4xx (客户端错误)：客户端请求包含语法错误或请求无法完成
* 5xx (服务器错误)：服务器在处理请求的过程中发生了错误

### 状态码一览表

已知状态码分别在 [RFC 7231 第六章](https://tools.ietf.org/html/rfc7231#section-6)、[RFC 7232 第四章](https://tools.ietf.org/html/rfc7232#section-4)、[RFC 7233 第四章](https://tools.ietf.org/html/rfc7233#section-4)以及 [RFC 7235 第三章](https://tools.ietf.org/html/rfc7235#section-3) 中进行了定义。下表中的 `描述短语`(reason phrases) 列只是建议值，在不影响协议的前提下，可以使用本地的同等描述替换。

部分状态码对应的数据是可以缓存（比如：200, 203, 204, 206, 300, 301, 404, 405, 410, 414, 和 501）并再次使用的（在一定[缓存规则控制](https://tools.ietf.org/html/rfc7234)下）；剩余的状态码对应响应默认都是不可缓存的。

| 状态码        | 描述短语 | 文档位置           |
| ------------- | :-------- |:-------------|
| 100      | Continue | [RFC 7231 6.2.1](https://tools.ietf.org/html/rfc7231#section-6.2.1) |
| 101 | Switching Protocols | [RFC 7231 6.2.2](https://tools.ietf.org/html/rfc7231#section-6.2.2) |
| 200 | OK | [RFC 7231 6.3.1](https://tools.ietf.org/html/rfc7231#section-6.3.1) |
| 201 | Created | [RFC 7231 6.3.2](https://tools.ietf.org/html/rfc7231#section-6.3.2) |
| 202 | Accepted | [RFC 7231 6.3.3](https://tools.ietf.org/html/rfc7231#section-6.3.3) |
| 203 | Non-Authoritative Information | [RFC 7231 6.3.4](https://tools.ietf.org/html/rfc7231#section-6.3.4) |
| 204 | No Content | [RFC 7231 6.3.5](https://tools.ietf.org/html/rfc7231#section-6.3.5) |
| 205 | Reset Content | [RFC 7231 6.3.6](https://tools.ietf.org/html/rfc7231#section-6.3.6) |
| 206 | Partial Content | [RFC 7233 4.1](https://tools.ietf.org/html/rfc7233#section-4.1) |
| 300 | Multiple Choices | [RFC 7231 6.4.1](https://tools.ietf.org/html/rfc7231#section-6.4.1) |
| 301 | Moved Permanently | [RFC 7231 6.4.2](https://tools.ietf.org/html/rfc7231#section-6.4.2) |
| 302 | Found | [RFC 7231 6.4.3](https://tools.ietf.org/html/rfc7231#section-6.4.3) |
| 303 | See Other | [RFC 7231 6.4.4](https://tools.ietf.org/html/rfc7231#section-6.4.4) |
| 304 | Not Modified | [RFC 7232 4.1](https://tools.ietf.org/html/rfc7232#section-4.1) |
| 305 | Use Proxy | [RFC 7231 6.4.5](https://tools.ietf.org/html/rfc7231#section-6.4.5) |
| 307 | Temporary Redirect | [RFC 7231 6.4.](https://tools.ietf.org/html/rfc7231#section-6.4.7) |
| 400 | Bad Request | [RFC 7231 6.5.1](https://tools.ietf.org/html/rfc7231#section-6.5.1) |
| 401 | Unauthorized | [RFC 7235 3.1](https://tools.ietf.org/html/rfc7235#section-3.1) |
| 402 | Payment Required | [RFC 7231 6.5.2](https://tools.ietf.org/html/rfc7231#section-6.5.2) |
| 403 | Forbidden | [RFC 7231 6.5.3](https://tools.ietf.org/html/rfc7231#section-6.5.3) |
| 404 | Not Found | [RFC 7231 6.5.4](https://tools.ietf.org/html/rfc7231#section-6.5.4) |
| 405 | Method Not Allowed | [RFC 7231 6.5.5](https://tools.ietf.org/html/rfc7231#section-6.5.5) |
| 406 | Not Acceptable | [RFC 7231 6.5.6](https://tools.ietf.org/html/rfc7231#section-6.5.6) |
| 407 | Proxy Authentication Required | [RFC 7235 3.2](https://tools.ietf.org/html/rfc7235#section-3.2) |
| 408 | Request Timeout | [RFC 7231 6.5.7](https://tools.ietf.org/html/rfc7231#section-6.5.7) |
| 409 | Conflict | [RFC 7231 6.5.8](https://tools.ietf.org/html/rfc7231#section-6.5.8) |
| 410 | Gone | [RFC 7231 6.5.9](https://tools.ietf.org/html/rfc7231#section-6.5.9) |
| 411 | Length Required | [RFC 7231 6.5.10](https://tools.ietf.org/html/rfc7231#section-6.5.10) |
| 412 | Precondition Failed  | [RFC 7232 4.2](https://tools.ietf.org/html/rfc7232#section-4.2) |
| 413 | Payload Too Large | [RFC 7231 6.5.11](https://tools.ietf.org/html/rfc7231#section-6.5.11) |
| 414 | URI Too Long | [RFC 7231 6.5.12](https://tools.ietf.org/html/rfc7231#section-6.5.12) |
| 415 | Unsupported Media Type | [RFC 7231 6.5.13](https://tools.ietf.org/html/rfc7231#section-6.5.13) |
| 416 | Range Not Satisfiable | [RFC 7233 4.4](https://tools.ietf.org/html/rfc7233#section-4.4) |
| 417 | Expectation Failed | [RFC 7231 6.5.14](https://tools.ietf.org/html/rfc7231#section-6.5.14) |
| 426 | Upgrade Required | [RFC 7231](https://tools.ietf.org/html/rfc7231#section-6.5.15) |
| 500 | Internal Server Error | [RFC 7231 6.6.1](https://tools.ietf.org/html/rfc7231#section-6.6.1) |
| 501 | Not Implemented | [RFC 7231 6.6.2](https://tools.ietf.org/html/rfc7231#section-6.6.2) |
| 502 | Bad Gateway | [RFC 7231 6.6.3](https://tools.ietf.org/html/rfc7231#section-6.6.3) |
| 503 | Service Unavailable | [RFC 7231 6.6.4](https://tools.ietf.org/html/rfc7231#section-6.6.4) |
| 504 | Gateway Timeout | [RFC 7231 6.6.5](https://tools.ietf.org/html/rfc7231#section-6.6.5) |
| 505 | HTTP Version Not Supported | [RFC 7231 6.6.6](https://tools.ietf.org/html/rfc7231#section-6.6.6) |

注意，当前列表并没有详尽的列出所有状态码（其他规格里定义的扩展状态码不包含在此列表）。完整的状态码列表是由 IANA 维护的。[详见 RFC 7231 8.2](https://tools.ietf.org/html/rfc7231#section-8.2)。

### 详细解释

#### 1xx 信息（Informational）

1xx（信息）类状态码表示本次响应处于通信连接临时响应阶段，或者请求操作处于完成并发送最终响应之前的前置阶段。状态行（status-line）后的第一个空行用来结束本次响应（空行是头部信息段落结束信号）。由于 HTTP/1.0 没有规定任何 1xx 类型的状态码，所以当客户端的协议类型是 HTTP/1.0 时，服务器不允许返回任何 1xx 类型的状态码。

即使客户端不期望接收 1xx 类型的状态码，也必须能够解析一个或更多 1xx 类型的状态码，用以接收最终响应之前的前置信息。用户代理则可以选择忽略某些非期望的 1xx 类型状态码。

代理服务器必须继续推送 1xx 类型的响应，除非代理服务器本身请求生成 1xx 类型的响应。比如：当代理服务器转发一个请求时，添加了元信息 "Expect: 100-continue"，则此时可以不继续转发原始的 100 响应。

| 状态码        | 描述短语 | 描述           |
| ------------- | :-------- |:-------------|
| 100      | Continue | 继续请求。100 状态码表示初始化的请求数据已被接收，且仍未被拒绝。当服务器完全接收到请求数据之后将会尝试去发送一个最终响应给客户端。<br><br> 当一个请求包含 `100-continue` 的请求头信息时，100 类型的响应则表明服务器期望继续接收请求的负载体（paypload body），详情参照 [RFC 7231 5.1.1](https://tools.ietf.org/html/rfc7231#section-5.1.1) 。<br><br> 如果请求没有包含任何期望 `100-continue` 的头信息，则客户端可以简单抛弃接收到的 100 过渡响应即可。 |
| 101 | Switching Protocols | 101 状态码表明服务器理解并将继续遵守客户端升级协议的请求，通过升级头部元信息([RFC 7230 6.7](https://tools.ietf.org/html/rfc7230#section-6.7))来更换在此连接上使用的应用程序协议。服务器必须在响应中生成一个升级头信息，表明在发送空行结束 101 响应之后将立即切换到目标协议。<br><br>     服务器同意升级协议的前提是：目标协议是有利的（更好、更新）。比如：升级到较新的 HTTP 协议相比旧的协议而言就是有利的；或者切换到一个实时且同步的协议以传送利用此类特性的资源。|

#### 2xx 成功（Successful）

2xx 状态码表示客户端请求已被服务器成功接收、理解并接受。

| 状态码        | 描述短语 | 描述           |
| ------------- | :------- |:-------------|
| 200      | OK | 成功。200 状态码表示请求成功。200 响应的内容取决于请求的方法。本规格文件定义的请求方法对应的响应负载含义总结如下：<br><br> `GET`，目标资源的表示<br>`HEAD`，同 GET，但是没有表示数据。<br> POST，表示请求操作的状态或从请求操作中获得的结果<br> PUT, DELETE，操作状态的表示<br> OPTIONS 通信选项的表示<br> TRACE，由终端服务器接收的请求消息的表示<br><br> |
| 201 | Created | 创建成功。返回创建的资源 URI 或 资源 URI 列表（如果操作不能即时完成，则应该返回 202）。该状态码可能返回 Etag 响应头，表明当前创建的资源标志，用于客户端唯一识别。 |
| 202 | Accepted | 请求被接受。正在进行处理，但是处理还未完成。该状态码可能用在批量耗时的请求中，表明已开始执行请求的耗时任务，但是执行结果不能立刻返回。 |
| 203 | Non-Authoritative Information | 非授权信息。该状态码下返回的元信息并不一定是来自目标服务器，有可能是目标服务器给出的原始数据的子集或者超集。 |
| 204 | No Content | 无响应内容。服务器正确处理了请求，但是不必返回任何 message-body。该状态码主要用来处理输入而不引起客户端当前活动文档视图的任何修改。 |
| 205 | Reset Content | 重置内容。服务器已正确处理请求，用户代理应该重置发起请求的文档视图。响应不能包含任何实体（entity）。 |
| 206 | Partial Content | 部分内容。GET 请求的部分资源已被服务器正确处理。 |



参考链接

* https://tools.ietf.org/html/rfc7231
* http://www.runoob.com/http/http-status-codes.html
* https://juejin.im/entry/5794c6118ac247005f266b58


