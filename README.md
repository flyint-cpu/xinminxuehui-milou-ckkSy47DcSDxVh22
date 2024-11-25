
# 问题描述


在抓取到网络包之后，如何来获取TLS信息呢？比如使用的是是么加密套件呢？


因为在应用层面，获取的错误信息非常简单：



> An TLS 1\.2 connection request was received from a remote client application, but non of the cipher suites supported by the client application are supported by the server. The connection request has failed.
> 
> 
> 从远程客户端应用程序收到 TLS 1\.2 连接请求，但服务器不支持客户端应用程序支持的任何密码套件。连接请求失败。


**所以，我们如何从客户端抓取的网络包中分析出 TLS 所所以用的 Cipher Suites信息呢？**


 


# 问题解答


通过咨询M365 Copilot，得到了想要的答案：



> 在Wireshark中分析SSL相关信息（例如使用的加密套件）可以通过以下步骤进行：
> 
> 
>  1\. **启动Wireshark并抓包：**首先，启动Wireshark并开始抓取网络流量。确保你正在捕获包含SSL/TLS流量的网络接口。 2\. **过滤SSL/TLS流量：**在Wireshark的过滤器栏中输入ssl或tls，以便只显示SSL/TLS流量。这将帮助你更容易找到相关的数据包。 3\. **找到SSL握手包：**在过滤后的流量中，找到SSL握手包。通常，这些包包括Client Hello和Server Hello消息。你可以通过查看协议列中的Client Hello和Server Hello来识别这些包。 4\. **查看加密套件：**
> 
> 
> * Client Hello：双击Client Hello包，在详细信息窗格中展开Secure Sockets Layer或Transport Layer Security部分。你会看到一个名为Cipher Suites的字段，其中列出了客户端支持的加密套件。
> * Server Hello：双击Server Hello包，在详细信息窗格中展开Secure Sockets Layer或Transport Layer Security部分。你会看到一个名为Cipher Suite的字段，其中列出了服务器选择的加密套件。
> 
> 
>  5\. **解密SSL/TLS流量（可选）**：如果你有服务器的私钥，可以在Wireshark中配置解密SSL/TLS流量。步骤如下：
> 
> 
> * 选择Edit \> Preferences。
> * 在Preferences窗口中展开Protocols，然后选择SSL。
> * 在RSA keys list字段中，输入服务器的IP地址、端口、协议和私钥文件路径。
> 
> 
> 通过这些步骤，你可以在Wireshark中分析SSL相关信息并查看使用的加密套件。


 


# 实际演示


根据以上的解答，实际实验去查看两个数据包：一个是正常建立TLS连接，一个是无法建立TLS连接。


#### 第一步：根据目的IP地址过滤网络包


打开 .pcapng 后缀名的网络包，根据目的IP地址进行过滤 ( 如：ip.addr \=\= *5\.13\.9\.7* ）


#### 第二步：查看TCP请求中的Client Hello 信息


Transport Layer Security \-\-\> TLSv1\.2 Record Layer \-\-\> Handshake Protocol:Client Hello \-\-\> Cipher Suites



> Cipher Suites (12 suites)
> 
> 
>  Cipher Suite: TLS\_RSA\_WITH\_AES\_128\_CBC\_SHA (0x002f) Cipher Suite: TLS\_RSA\_WITH\_AES\_256\_CBC\_SHA (0x0035\) Cipher Suite: TLS\_RSA\_WITH\_RC4\_128\_SHA (0x0005\) Cipher Suite: TLS\_RSA\_WITH\_3DES\_EDE\_CBC\_SHA (0x000a) Cipher Suite: TLS\_ECDHE\_RSA\_WITH\_AES\_128\_CBC\_SHA (0xc013\) Cipher Suite: TLS\_ECDHE\_RSA\_WITH\_AES\_256\_CBC\_SHA (0xc014\) Cipher Suite: TLS\_ECDHE\_ECDSA\_WITH\_AES\_128\_CBC\_SHA (0xc009\) Cipher Suite: TLS\_ECDHE\_ECDSA\_WITH\_AES\_256\_CBC\_SHA (0xc00a) Cipher Suite: TLS\_DHE\_DSS\_WITH\_AES\_128\_CBC\_SHA (0x0032\) Cipher Suite: TLS\_DHE\_DSS\_WITH\_AES\_256\_CBC\_SHA (0x0038\) Cipher Suite: TLS\_DHE\_DSS\_WITH\_3DES\_EDE\_CBC\_SHA (0x0013\) Cipher Suite: TLS\_RSA\_WITH\_RC4\_128\_MD5 (0x0004\)


#### 第三步：查看TCP请求中的Server Hello 信息


Transport Layer Security \-\-\> TLSv1\.2 Record Layer \-\-\> Handshake Protocol:Server Hello \-\-\> Cipher Suites


##### \[正常建立连接]



> Cipher Suite: TLS\_ECDHE\_RSA\_WITH\_AES\_128\_CBC\_SHA (0xc013\)


![](https://img2024.cnblogs.com/blog/2127802/202411/2127802-20241124115550778-689213122.png)


##### \[不能正常建立连接]


在客户端发送Client Hell后，服务端返回的Server Hello包中，



> Transport Layer Security
> 
> 
>  TLSv1\.2 Record Layer: Alert (Level: Fatal, Description: Protocol Version) Content Type: Alert (21\) Version: TLS 1\.2 (0x0303\) Length: 2 Alert Message Level: Fatal (2\) Description: Protocol Version (70\)


![](https://img2024.cnblogs.com/blog/2127802/202411/2127802-20241124115226554-419457846.png)


 


#### **演示动画：**


![](https://img2024.cnblogs.com/blog/2127802/202411/2127802-20241124114147359-1259624839.gif)


 


# 参考资料


Wireshark: [https://www.wireshark.org/](https://github.com)


 


 本博客参考[楚门加速器](https://chuanggeye.com)。转载请注明出处！
