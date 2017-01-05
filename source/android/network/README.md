# Network

网络库对比

* OkHttp3
  * 支持``SPDY,http2,websocket``等多种协议，多个请求共享``socket``,连接池，磁盘缓存，透明压缩，支持``java nio(以块的形式读写，缓冲区)``,只能使用``http协议``缓存。经常与``Retrofit2``配合使用，``Retrofit2``能够支持各种适配器来解析响应
  * 
* Volley
  * 小而精致，高度可定制，但不支持同步请求，不能``post``大数据，因此不能用来上传文件

* async-http: 底层使用的``HttpClient``，但从``android6.0``之后，系统已经不推荐使用