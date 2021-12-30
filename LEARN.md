## 网络模型关键调用堆栈

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
createClient networking.c:122 // conn已经有了type属性
acceptCommonHandler networking.c:1082
acceptTcpHandler networking.c:1140 // 监听socket处理器
aeProcessEvents ae.c:458
aeMain ae.c:521
main server.c:6437
~~~


创建conn对象调用堆栈：
~~~stack
connCreateSocket connection.c:79
connCreateAcceptedSocket connection.c:96
acceptTcpHandler networking.c:1146
aeProcessEvents ae.c:458
aeMain ae.c:521
main server.c:6437
~~~


## 关键结构和流程定义
事件循环结构:
~~~c
aeEventLoop:
aeFileEvent[maxClient+other] events; /* 已经注册的事件,fd的值是作为索引下标 */
aeFiredEvent[maxClient+other] fired; /* 触发事件 */


// 已经注册事件
typedef struct aeFileEvent {
    int mask; /* 注册的事件：读或者写 */
    aeFileProc *rfileProc; /* 读事件发生时回调 */
    aeFileProc *wfileProc; /* 写事件发生时回调 */
    void *clientData; /* 客户端附加数据 */
} aeFileEvent;


// 触发事件
typedef struct aeFiredEvent {
    int fd; // 触发事件的文件描述符
    int mask; // 触发的事件类型：读或者写
} aeFiredEvent;


// 添加注册事件,将指定的fd加入到事件监听中
int aeCreateFileEvent(aeEventLoop *eventLoop, int fd, int mask,
        aeFileProc *proc, void *clientData);


// 事件循环,等待事件触发,执行事件数组
int aeProcessEvents(aeEventLoop *eventLoop, int flags);


// 设置firedEvent,事件来到之后设置fired数组(触发事件数组)
static int aeApiPoll(aeEventLoop *eventLoop, struct timeval *tvp);


// 监听连接Handler
void acceptTcpHandler(aeEventLoop *el, int fd, void *privdata, int mask);


// 监听命令到来Handler
void readQueryFromClient(connection *conn);


// 多IO线程处理解析参数和写出结果事件,在void initThreadedIO(void)处初始化
void *IOThreadMain(void *myid);

~~~

