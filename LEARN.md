## 网络流程

网络模型相关数据结构初始化调用堆栈：
~~~stack
aeApiCreate ae_epoll.c:40
aeCreateEventLoop ae.c:84
initServer server.c:3184
main server.c:6315
~~~

事件循环流程调用堆栈：
~~~stack
anetTcpAccept anet.c:515
acceptTcpHandler networking.c:1114
aeProcessEvents ae.c:437
aeMain ae.c:497
main server.c:6404
~~~


添加accept处理器调用堆栈：
~~~stack
aeCreateFileEvent ae.c:180
createSocketAcceptHandler server.c:3014
initServer server.c:3321
main server.c:6348
~~~


添加客户端连接read/write处理器调用堆栈：
~~~stack
aeApiAddEvent ae_epoll.c:82
aeCreateFileEvent ae.c:189
connSocketSetReadHandler connection.c:245
connSetReadHandler connection.h:166
createClient networking.c:122
acceptCommonHandler networking.c:1082
acceptTcpHandler networking.c:1140 // 监听socket处理器
aeProcessEvents ae.c:458
aeMain ae.c:521
main server.c:6437
~~~
