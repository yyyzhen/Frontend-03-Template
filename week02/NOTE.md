# 浏览器工作原理一
## 流程

![浏览器渲染流程](../image/browser%20reanding%20process.png)
1. URL
2. HTTP
3. DOM
4. DOM with CSS
5. DOM with position
6. Bitmap

## 状态机

有限状态机
- 每一个状态都是一个机器
  - 在每一个机器里，我们可以做计算、存储、输出......
  - 所有的这些机器接受的输入是一致的
  - 状态机的每一个机器本身没有状态，如果我们用函数来表示的话，它应该是纯函数（无副作用）
- 每一个机器知道下一个状态
  - 每个机器都很确定的下一个状态（Moore)
  - 每个机器根据输入决定下一个状态(Mealy)

JS中的有限状态机（Mealy）
```js
// 每个函数是一个状态
function state(input) {
  // 在函数中，可以自由编写代码，处理每个状态的逻辑
  return next // 返回值座位下一个状态
}
// 以下是调用
while(input) {
  // 获取输入
  state = state(input) // 把状态机的返回值座位下一个状态
}
```
## HTTP请求

### ISO-OSI七层网络模型
<table>
  <tr><th>模型</th><th>可对应概念</th></tr>
  <tr align="center"><td>应用</td><td rowspan="3">HTTP</td></tr>
  <tr align="center"><td>表示</td></tr>
  <tr align="center"><td>会话</td></tr>
  <tr align="center"><td>传输</td><td>TCP</td></tr>
  <tr align="center"><td>网络</td><td>Internet</td></tr>
  <tr align="center"><td>数据链路</td><td rowspan="2">4G/5G/Wi-Fi</td></tr>
  <tr align="center"><td>物理</td></tr>
</table>

#### TCP、IP
- 流：传输数据的概念
- 端口：网卡根据端口来分配接收到的数据包
- require('net')：node 中对应的包
- 包：传输数据的一种单位
- IP地址
- libnet/libpcap：C++中用于分发/捕获IP包的库

#### 数据结构
- response
  - status line: 响应状态栏
  - headers：响应头
    - headersName
    - headersValue
  - body：响应主体，不同语言的后端返回的 body 不一样，在 node 中是 chunk body
```
HTTP/1.1 200 OK\r\nContent-Type: text/html\r\nDate: Fri, 21 Aug 2020 06:43:38 GMT\r\nConnection: keep-alive\r\nTransfer-Encoding: chunked\r\n\r\nd\r\n Hello World\n\r\n0\r\n\r\n
```
<table>
  <tr><th>示例</th><th>结构解析</th><th>所属结构</th></tr>
  <tr>
    <td>HTTP/1.1 200 OK\r\n</td>
    <td>{HTTP协议}<br>{状态码}<br>{回车换行}</td>
    <td>statue line</td>
  </tr>
  <tr>
    <td>Content-Type: text/html\r\n</td>
    <td rowspan="4">{headerName}<br>{冒号空格}<br>{headerValue}<br>{回车换行}</td>
    <td rowspan="4">headers</td>
  </tr>
  <tr>
    <td>Date: Fri, 21 Aug 2020 06:43:38 GMT\r\n</td>
  </tr>
  <tr>
    <td>Connection: keep-alive\r\n</td>
  </tr>
  <tr>
    <td>Transfer-Encoding: chunked\r\n</td>
  </tr>
  <tr>
    <td>d\r\n</td>
    <td>{16进制chunk长度}<br>{回车换行}</td>
    <td rowspan="4">chunk body</td>
  </tr>
  <tr>
    <td> Hello World\n\r\n</td>
    <td>{chunk内容}<br>{回车换行}</td>
  </tr>
  <tr>
    <td>0\r\n\r\n</td>
    <td>{下一个16进制chunk长度}<br>{回车换行}<br>{回车换行}</td>
  </tr>
  
</table>

#### HTTP 请求实现

**第一步 HTTP 请求**
- 设计一个HTTP请求类
- content-type 是一个必要的字段，要有默认值
- body 是 KV 格式
- 不同的 content-type 影响 body 的格式

**第二步 send 函数**
- 在 Request 的构造器中收集必要的信息
- 设计一个 send 函数，把请求真实发送到服务器
- send 函数应该是异步的，所以返回 Promise

**第三步 发送请求**
- 设计支持已有的 connection 或者自己新建 connection
- 收到数据传递给 parser
- 根据 parser 状态 resolve Promise

**第四步 ResponseParser**
- Response 必须分段构造，所以我们要用一个 RenponseParser 来 “装配”
- ResponseParser 分段处理 ResponseText，我们用状态机来分析文本的结构

**第五步 BodyParser**
- Response 的 body 可能根据 Content-Type 有不同的结构，因此我们会采用子 Parser 的结构来解决问题
- 以 TrunkedBodyParser 为例，我们同样用状态机来处理 body 的格式