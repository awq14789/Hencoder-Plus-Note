# 第三节：登录授权、TCP/IP、HTTPS

## 登录和授权
* Cookie
* Authorization

## Cookie
* 作用：
	* 会话管理：登录状态、购物车等
	* 个性化：用户偏好、主题等
	* Tracking：分析用户行为
* XSS(Cross-site scripting):HttpOnly
* XSRF(Cross-site request forgery):Referer

## Authorization
* Basic:
	* Authorization: Basic < Base64(username:password) >
* Bearer:
	* Authorization: Bearer < bearer token>
	* Oauth2
		* 流程：
		* A.com访问github.com
		* github返回auth_key给A.com
		* A.com把auth_key传给A.com的服务器
		* 服务器把auth_key和client_secret发给github
		* github验证成功后，把用户的token传给A的服务器

## TCP/IP协议簇
* 一般有4层：应用层、传输层、网络层、数据链路层
* 应用层：用户收发消息 HTTP、FTP
* 传输层：用于将消息拆包 TCP、UDP
* 网络层：用于寻址 IP
* 数据链路层：用于消息的传输 以太网、WIFI

## HTTPS
* 在HTTP之下增加的一个安全层，用于保障HTTP的加密传输
* 本质：在客户端和服务器之间协商出一套对称密钥，每次发送信息之前将内容加密，收到之后解密，达到内容的加密传输
* HTTPS连接：前4步都是TCP，只到最后一步才开始用到HTTP
	1. 客户端请求建立TLS连接
	2. 服务器发回证书
	3. 客户端验证服务器证书
	4. 客户端信任服务器后，和服务器协商对称密钥
	5. 使用对称密钥开始通信