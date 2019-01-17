# 第一节：HTTP
## http请求
* GET
* POST
* PUT
* DELETE
* HEAD
	* 请求只把header返回，不返回body/content
	* 经常用于文件下载，在下载前先调用一下，获取需要下载的文件的属性

## 状态码
作用：对结果做出类型化描述（如“获取成功”、“内容未找到”）
* 1xx: 临时性消息
	* 100：分段请求时会返回
	* 101：判断是否支持http2.0
* 2xx: 通常表示成功
* 3xx: 重定向
	* 301：永久重定向到新链接
	* 302：临时重定向到新连接
	* 304：内容未改变，使用老内容
* 4xx: 客户端错误（请求端错误）
* 5xx: 服务器错误

## Header
作用： HTTP消息的元数据（metadata）
* Host:服务器主地址
* Content-Length：内容的长度
* Content-Type：内容的类型
	* text/html: html文本，用于浏览器页面响应
	* application/x-www-form-urlencoded: 普通表单，encoded URL格式
	* multipart/form-data: 多部分形式，一般用于传输包含二进制内容的多项内容（比如文件）
	* application/json: json形式，用于web api的响应或post/put请求
	* image/jpeg 或 application/zip等： 单个文件请求，比multipart省资源
*  Transfer-Encoding: chunked
	*  表示body长度无法确定，Content-length不能使用
	*  Body格式：
	```
    <length1>（长度）
    <data1>（内容）
    <length2>
    <data2>
    0

    (最后传输 0 + 换行（\n）表示内容结束)
    ```
* Location：重定向的目标url(status code为301、302)
* User-Agent:用户代理
* Range/Accept-Range:指定Body的内容范围（比如分段下载时会使用）
* Cookie/Set-Cookie:发送Cookie/设置Cookie
* Authorization:授权信息

#### 部分其他header
* Accept:客户端能接受的数据类型。如 text/html
* Accept-Charset:客户端接受的字符集。如utf-8
* Accept-Encoding:客户端接受的压缩编码类型。如gzip
* Content-Encoding:压缩类型。如gzip

## Cache
* cache与buffer的区别
	* cache是缓存，就是把已经加载过的东西存起来
	* buffer是缓冲，就是预加载，比如看视频时的预加载
