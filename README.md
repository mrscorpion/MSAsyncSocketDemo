# 网络编程: TCP/IP、SOCKET、HTTP/HTTPS 
------
![对应关系](http://cc.cocimg.com/api/uploads/20160601/1464766993189763.jpg)

> 网络七层
>> - 媒体层: 网络工程师所研究的对象
    - 物理层
    - 数据链路层 **【ARP \ RARP】**
    - 网络层 **【IP】**
- 主机层: 用户所面向和关心的内容
    - 传输层 **【TCP \ UDP】**
    - 会话层
    - 表示层
    - 应用层 **【HTTP \ FTP \ SMTP \ DNS】**
- 拓展: 通信的基石`套接字`**【Socket】**
    - socket: 网络通信过程中`端点`的抽象表示

---
> TCP/IP五层模型
>> - 应用层
- 传输层
- 网络层
- 数据链路层
- 物理层
    + 中继器、集线器、双绞线工作在物理层；
    + 网桥（现已很少使用）、以太网交换机（二层交换机）、网卡（其实网卡是一半工作在物理层、一半工作在数据链路层）在数据链路层；
    + 路由器、三层交换机在网络层；
    + 四层交换机、也有工作在四层的路由器工作在传输层。

TCP/IP协议中的应用层处理七层模型中的第五层、第六层和第七层的功能。TCP/IP协议中的传输层并不能总是保证在传输层可靠地传输数据包，而七层模型可以做到。TCP/IP协议还提供一项名为UDP（用户数据报协议）的选择。UDP不能保证可靠的数据包传输。

`TCP是一种流模式的协议，UDP是一种数据报模式的协议`

- TCP：面向连接、传输可靠(保证数据正确性,保证数据顺序)、用于传输大量数据(流模式)、速度慢，建立连接需要开销较多(时间，系统资源)。
- UDP：面向非连接、传输不可靠、用于传输少量数据(数据包模式)、速度快。

---
### IP、TCP、HTTP区别联系
- 三者本质上无可比性
- HTTP协议基于TCP连接
    + TCP/IP是传输层协议，主要解决数据如何在网络中传输;
    > 传输数据，可以只使用传输层（TCP/IP），但是没有应用层，便无法识别数据内容。如果要使得传输的数据有意义，必须使用应用层协议(HTTP、FTP、TELNET等)，也可以自己定义应用层协议。
    + HTTP是应用层协议，主要解决如何包装数据。
    >> WEB使用HTTP作应用层协议，以封装HTTP文本信息，然后使用TCP/IP做传输层协议将它发送到网络上。


![Socket的位置](http://cc.cocimg.com/api/uploads/20160601/1464767037800627.jpg)

### Socket
    socket是对TCP/IP协议的封装，socket本身并不是协议，而是一个调用接口（API），通过socket我们才能使用TCP/IP协议。
    Socket描述了一个IP、端口对。它简化了程序员的操作，知道对方的IP以及PORT就可以给对方发送消息，再由服务器端来处理发送的这些消息。

## Http 和 Socket连接区别
#### TCP连接
要想明白Socket连接，先要明白TCP连接。手机能够使用联网功能是因为手机底层实现了TCP/IP协议，可以使手机终端通过无线网络建立TCP连接。TCP协议可以对上层网络提供接口，使上层网络数据的传输建立在“无差别”的网络之上。

###### 建立起一个TCP连接需要经过“三次握手”
- 第一次握手：客户端发送syn包(syn=j)到服务器，并进入SYN_SEND状态，等待服务器确认；
- 第二次握手：服务器收到syn包，必须确认客户的SYN（ack=j+1），同时自己也发送一个SYN包（syn=k），即SYN+ACK包，此时服务器进入SYN_RECV状态；
- 第三次握手：客户端收到服务器的SYN＋ACK包，向服务器发送确认包ACK(ack=k+1)，此包发送完毕，客户端和服务器进入ESTABLISHED状态，完成三次握手。

> * 握手过程中传送的包里不包含数据，三次握手完毕后，客户端与服务器才正式开始传送数据。
* 理想状态下，TCP连接一旦建立，在通信双方中的任何一方主动关闭连接之前，TCP连接都将被一直保持下去。
* 断开连接时服务器和客户端均可以主动发起断开TCP连接的请求，断开过程需要经过“四次握手”。

![](http://cc.cocimg.com/api/uploads/20160601/1464767075917893.png)

###### 断开一个TCP连接需要经过“四次握手”
TCP连接的拆除需要发送四个包，因此称为四次握手(four-way handshake)。在socket编程中，任何一方执行close()操作即可产生握手（有地方称为“挥手”）操作。

![TCP连接的拆除](http://cc.cocimg.com/api/uploads/20160601/1464767137254402.jpg)

###### 连接“三次握手”和断开“四次握手”的区别
> 之所以有“三次握手”和“四次握手”的区别，是因为连接时当Server端收到Client端的SYN连接请求报文后，可以直接发送SYN+ACK报文。其中ACK报文是用来应答的，SYN报文是用来同步的。但是关闭连接时，当Server端收到FIN报文时，很可能并不会立即关闭SOCKET，所以只能先回复一个ACK报文，告诉Client端，”你发的FIN报文我收到了”。只有等到我Server端所有的报文都发送完了，我才能发送FIN报文，因此不能一起发送。故需要四步握手。

#### HTTP连接
HTTP协议即超文本传送协议(HypertextTransferProtocol )是Web联网的基础，也是手机联网常用的协议之一，HTTP协议是建立在TCP协议之上的一种应用。
> HTTP连接最显著的特点是: 
>> 客户端发送的每次请求都需要服务器回送响应，在请求结束后，会主动释放连接。从建立连接到关闭连接的过程称为“一次连接”。

- 在HTTP 1.0中，客户端的每次请求都要求建立一次单独的连接，在处理完本次请求后，就自动释放连接。
- 在HTTP 1.1中则可以在一次连接中处理多个请求，并且多个请求可以重叠进行，不需要等待一个请求结束后再发送下一个请求。
> 由于HTTP在每次请求结束后都会`主动释放连接`，因此`HTTP连接是一种“短连接”`，要保持客户端程序的在线状态，需要不断地向服务器发起连接请求。
>> 通常做法是即使不需要获得任何数据，客户端也保持每隔一段固定的时间向服务器发送一次“保持连接”的请求，服务器在收到该请求后对客户端进行回复，表明知道客户端“在线”。若服务器长时间无法收到客户端的请求，则认为客户端“下线”，若客户端长时间无法收到服务器的回复，则认为网络已经断开。

### HTTPS
HTTPS（Hyper Text Transfer Protocol over Secure Socket Layer），是以安全为目标的HTTP通道，是HTTP的安全版。 在HTTP下加入SSL层，HTTPS的安全基础是SSL，因此加密的详细内容就需要SSL。 HTTPS存在不同于HTTP的默认端口及一个加密/身份验证层（在HTTP与TCP之间）。HTTP协议以明文方式发送内容，不提供任何方式的数据加密，如果攻击者截取了Web浏览器和网站服务器之间的传输报文，就可以直接读懂其中的信息，因此HTTP协议不适合传输一些敏感信息。

###### HTTP和HTTPS的区别
- https协议需要到ca申请证书；http是超文本传输协议，信息是明文传输，https则是具有安全性的ssl加密传输协议；
- http和https使用的是完全不同的连接方式，用的端口也不一样，前者是80，后者是443；
- http的连接很简单，是无状态的，HTTPS协议是由SSL+HTTP协议构建的可进行加密传输、身份认证的网络协议。
![HTTPS](http://cc.cocimg.com/api/uploads/20160601/1464767167242387.jpeg)

---
![Socket使用](http://cc.cocimg.com/api/uploads/20160601/1464766627371148.jpg)
#### Socket原理
###### socket(套接字)概念

    套接字（socket）是通信的基石，是支持TCP/IP协议的网络通信的基本操作单元。它是网络通信过程中**端点**的抽象表示，包含进行网络通信必须的五种信息：连接使用的协议，本地主机的IP地址，本地进程的协议端口，远地主机的IP地址，远地进程的协议端口。

> 应用层通过传输层进行数据通信时，TCP会遇到同时为多个应用程序进程提供并发服务的问题。多个TCP连接或多个应用程序进程可能需要通过同一个TCP协议端口传输数据。
>> `为了区别不同的应用程序进程和连接`，许多计算机操作系统为应用程序与TCP／IP协议交互提供了套接字(Socket)接口。应用层可以和传输层通过Socket接口，区分来自不同应用程序进程或网络连接的通信，实现数据传输的并发服务。

###### 建立socket连接
    建立Socket连接至少需要一对套接字，其中一个运行于客户端，称为ClientSocket，另一个运行于服务器端，称为ServerSocket。
    每一个应用或者说服务，都有一个端口。比如DNS的53端口，http的80端口。我们能由DNS请求到查询信息，是因为DNS服务器时时刻刻都在监听53端口，当收到我们的查询请求以后，就能够返回我们想要的IP信息。

###### 套接字之间的连接过程分为三个步骤：
- (1)服务器监听: `服务端利用Socket监听端口`
> 服务器端套接字并不定位具体的客户端套接字，而是处于等待连接的状态，实时监控网络状态，等待客户端的连接请求。

- (2)客户端请求: `客户端发起连接`
> 指客户端的套接字提出连接请求，要连接的目标是服务器端的套接字。为此，客户端的套接字必须首先描述它要连接的服务器的套接字，指出服务器端套接字的地址和端口号，然后就向服务器端套接字提出连接请求。

- (3)连接确认: `服务端返回信息，建立连接，开始通信`
> 当服务器端套接字监听到或者说接收到客户端套接字的连接请求时，就响应客户端套接字的请求，建立一个新的线程，把服务器端套接字的描述发给客户端，一旦客户端确认了此描述，双方就正式建立连接。而服务器端套接字继续处于监听状态，继续接收其他客户端套接字的连接请求。

- 断开：`客户端，服务端断开连接`

###### SOCKET连接与TCP连接

创建Socket连接时，可以指定使用的传输层协议，Socket可以支持不同的传输层协议（TCP或UDP），当使用TCP协议进行连接时，该Socket连接就是一个TCP连接；当使用UDP协议进行连接时，该Socket连接就是一个UDP连接。

###### SOCKET连接与HTTP连接
- Socket连接
由于通常情况下Socket连接就是TCP连接，因此Socket连接一旦建立，通信双方即可开始相互发送数据内容，直到双方连接断开。
> 但在实际网络应用中，客户端到服务器之间的通信往往需要穿越多个中间节点，例如路由器、网关、防火墙等，大部分防火墙默认会关闭长时间处于非活跃状态的连接而导致Socket连接断连，因此需要通过轮询告诉网络，该连接处于活跃状态。
- HTTP连接
而HTTP连接使用的是“请求—响应”的方式，不仅在请求时需要先建立连接，而且需要客户端向服务器发出请求后，服务器端才能回复数据。
> 很多情况下，需要服务器端主动向客户端推送数据，保持客户端与服务器数据的实时与同步。此时若双方建立的是Socket连接，服务器就可以直接将数据传送给客户端；若双方建立的是HTTP连接，则服务器需要等到客户端发送一次请求后才能将数据传回给客户端，因此，客户端定时向服务器端发送连接请求，不仅可以保持在线，同时也是在“询问”服务器是否有新的数据，如果有就将数据传给客户端。

---
### socket使用

    socket使用的库函数
    
- 1.创建套接字
    + Socket(af,type,protocol)//建立地址和套接字的联系
    + bind(sockid, local addr, addrlen)//服务器端侦听客户端的请求
    + listen( Sockid ,quenlen)// 建立服务器/客户端的连接 (面向连接TCP）
- 2.客户端请求连接
    + Connect(sockid, destaddr, addrlen)//服务器端等待从编号为Sockid的Socket上接收客户连接请求
    + newsockid=accept(Sockid，Clientaddr, paddrlen)//发送/接收数据
- 3.面向连接：
    + send(sockid, buff, bufflen)
    + recv()
- 4.面向无连接：
    + sendto(sockid,buff,…,addrlen)
    + recvfrom()
- 5.释放套接字
    + close(socked)

客户端代码
```
//需要导入<arpa inet.h="">，<netdb.h>
- (void)test
{
    NSString * host =@"123.33.33.1";
    NSNumber * port = @1233;
    // 创建 socket
    int socketFileDescriptor = socket(AF_INET, SOCK_STREAM, 0);
    if (-1 == socketFileDescriptor) {
        NSLog(@"创建失败");
        return;
    }
    // 获取 IP 地址
    struct hostent * remoteHostEnt = gethostbyname([host UTF8String]);
    if (NULL == remoteHostEnt) {
        close(socketFileDescriptor);
        NSLog(@"%@",@"无法解析服务器的主机名");
        return;
    }
    struct in_addr * remoteInAddr = (struct in_addr *)remoteHostEnt->h_addr_list[0];
    // 设置 socket 参数
    struct sockaddr_in socketParameters;
    socketParameters.sin_family = AF_INET;
    socketParameters.sin_addr = *remoteInAddr;
    socketParameters.sin_port = htons([port intValue]);
    // 连接 socket
    int ret = connect(socketFileDescriptor, (struct sockaddr *) &socketParameters, sizeof(socketParameters));
    if (-1 == ret) {
        close(socketFileDescriptor);
        NSLog(@"连接失败");
        return;
    }
    NSLog(@"连接成功");
}</netdb.h></arpa>
```
> 因为是C语言的，所以看起来不是很方便，一般开发中都会使用比较简单的方法: `CocoaAsyncSocket`

#### iOS Socket开发
在iOS中以NSStream(流)来发送和接收数据,可以设置流的代理，对流状态的变化做出相应的动作(连接建立，接收到数据，连接关闭）。

- NSStream：数据流的父类，用于定义抽象特性，例如：打开、关闭代理，NSStream继承自CFStream(CoreFoundation)
- NSInputStream：NSStream的子类，用于读取输入
- NSOutputStream：NSSTream的子类，用于写输出。

> iOS的socket实现是特别简单的，可以使用用github的开源类库cocoaasyncsocket简化开发，cocoaasyncsocket是支持tcp和ump的

#### 示例 `CocoaAsyncSocket`
- CocoaPods
```
pod 'CocoaAsyncSocket' 
```
- 单例
`MSSocket.h`
```
#import <Foundation/Foundation.h>
#import <GCDAsyncSocket.h>

@interface MSSocket : NSObject
@property (nonatomic, strong) GCDAsyncSocket *mySocket;

// 创建一个单例
+ (MSSocket *)defaultSocket;
@end
```
`MSSocket.m`
```
#import "MSSocket.h"

@implementation MSSocket
+ (MSSocket *)defaultSocket
{
    static MSSocket *socket = nil;
    static dispatch_once_t onceToken;
    dispatch_once(&onceToken, ^{
        socket = [[self alloc] init];
    });
    return socket;
}
@end
```

- 服务器端代码 
`ServerVC.h`
```
#import <UIKit/UIKit.h>
#import "MSSocket.h"

@interface ServerVC : UIViewController<GCDAsyncSocketDelegate>
@property (weak, nonatomic) IBOutlet UITextField *portTF;   // 端口号
@property (weak, nonatomic) IBOutlet UITextField *content;
@property (weak, nonatomic) IBOutlet UITextView *message;
@property (nonatomic, strong) GCDAsyncSocket *clientSocket; // 为客户端生成的socket
@property (nonatomic, strong) GCDAsyncSocket *serverSocket; // 服务器socket
@end
```
`ServerVC.m`
```
#import "ServerVC.h"

@interface ServerVC ()
@end

@implementation ServerVC
#pragma mark - VIEW LIFE CYCLE
- (void)viewDidLoad
{
    [super viewDidLoad];
}

#pragma mark - ACTIONS
/**
 监听

 @param sender 监听按钮
 */
- (IBAction)listen:(id)sender
{
    // 1. 创建服务器socket
    self.serverSocket = [[GCDAsyncSocket alloc] initWithDelegate:self delegateQueue:dispatch_get_main_queue()];
    // 2. 开放哪些端口
    NSError *error = nil;
    BOOL result = [self.serverSocket acceptOnPort:self.portTF.text.integerValue error:&error];
    // 3. 判断端口号是否开放成功
    if (result) {
        [self addText:@"端口开放成功"];
    } else {
        [self addText:@"端口开放失败"];
    }
}
// textView填写内容
- (void)addText:(NSString *)text
{
    self.message.text = [self.message.text stringByAppendingFormat:@"%@\n", text];
}

/**
 发送消息

 @param sender 发送按钮
 */
- (IBAction)sendMessage:(id)sender
{
    NSData *data = [self.content.text dataUsingEncoding:NSUTF8StringEncoding];
    [self.clientSocket writeData:data withTimeout:-1 tag:0];
    MSSocket *socket = [MSSocket defaultSocket];
    [socket.mySocket readDataWithTimeout:-1 tag:0];
}

/**
 接收数据

 @param sender 接收
 */
- (IBAction)receiveMessage:(id)sender
{
    [self.clientSocket readDataWithTimeout:-1 tag:0];
}


#pragma mark - GCDAsyncSocketDelegate
/**
 当客户端链接服务器端的socket, 为客户端单生成一个socket

 @param sock socket
 @param newSocket 为客户端单生成一个新socket
 */
- (void)socket:(GCDAsyncSocket *)sock didAcceptNewSocket:(GCDAsyncSocket *)newSocket
{
    [self addText:@"链接成功"];
    // IP: newSocket.connectedHost
    // 端口号: newSocket.connectedPort
    [self addText:[NSString stringWithFormat:@"链接地址:%@", newSocket.connectedHost]];
    [self addText:[NSString stringWithFormat:@"端口号:%hu", newSocket.connectedPort]];
    // short: %hd
    // unsigned short: %hu
    
    // 存储新的端口号
    self.clientSocket = newSocket;
}

/**
 读取数据

 @param sock socket
 @param data 接收到的数据
 @param tag tag
 */
- (void)socket:(GCDAsyncSocket *)sock didReadData:(NSData *)data withTag:(long)tag
{
    NSString *message = [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding];
    [self addText:message];
}


- (void)didReceiveMemoryWarning
{
    [super didReceiveMemoryWarning];
}
@end
```

- 客户端代码 
`ClientVC.h`
```
#import <UIKit/UIKit.h>
#import "MSSocket.h"

@interface ClientVC : UIViewController<GCDAsyncSocketDelegate>
@property (weak, nonatomic) IBOutlet UITextField *addressTF;
@property (weak, nonatomic) IBOutlet UITextField *portTF;
@property (weak, nonatomic) IBOutlet UITextField *message;
@property (weak, nonatomic) IBOutlet UITextView *content;

@property (nonatomic, strong) GCDAsyncSocket *socket;
@end
```
`ClientVC.m`
```
#import "ClientVC.h"

@implementation ClientVC
#pragma mark - VIEW LIFE CYCLE
- (void)viewDidLoad
{
    [super viewDidLoad];
}

#pragma mark - ACTIONS
/**
 和服务器进行链接
 @discussion 与服务器通过三次握手建立连接
 @param sender 连接按钮
 */
- (IBAction)connect:(UIButton *)sender
{
    // 1. 创建一个socket对象
    self.socket = [[GCDAsyncSocket alloc] initWithDelegate:self delegateQueue:dispatch_get_main_queue()];
    
    // 2. 与服务器的socket链接起来
    NSError *error = nil;
    BOOL result = [self.socket connectToHost:self.addressTF.text onPort:self.portTF.text.integerValue error:&error];
    
    // 3. 判断链接是否成功
    if (result) {
        [self addText:@"客户端链接服务器成功"];
    } else {
        [self addText:[NSString stringWithFormat:@"客户端链接服务器失败:%@", error]];
    }
}

/**
 接收数据

 @param sender 接收按钮
 */
- (IBAction)receiveMassage:(UIButton *)sender
{
    [self.socket readDataWithTimeout:-1 tag:0];
}

/**
 发送消息

 @param sender 发送按钮
 */
- (IBAction)sendMassage:(UIButton *)sender
{
    [self.socket writeData:[self.message.text dataUsingEncoding:NSUTF8StringEncoding] withTimeout:-1 tag:0];
}

/**
 内容区显示消息

 @param text 内容
 */
- (void)addText:(NSString *)text
{
    self.content.text = [self.content.text stringByAppendingFormat:@"%@\n", text];
}

#pragma mark - GCDAsyncSocketDelegate
/**
 连接成功
 @discussion 客户端链接服务器端成功, 客户端获取地址和端口号
 @param sock socket
 @param host IP
 @param port 端口号
 */
- (void)socket:(GCDAsyncSocket *)sock didConnectToHost:(NSString *)host port:(uint16_t)port
{
    NSLog(@"%s",__func__);
    [self addText:[NSString stringWithFormat:@"链接服务器%@", host]];
    MSSocket *socket = [MSSocket defaultSocket];
    socket.mySocket = self.socket;
}

/**
 断开连接
 
 @param sock socket
 @param err 错误信息
 */
- (void)socketDidDisconnect:(GCDAsyncSocket *)sock withError:(NSError *)err
{
    if (err) {
        NSLog(@"连接失败");
    }else{
        NSLog(@"正常断开");
    }
}

/**
 读取数据
 @discussion 客户端已经获取到内容
 @param sock socket
 @param data 数据
 @param tag tag
 */
- (void)socket:(GCDAsyncSocket *)sock didReadData:(NSData *)data withTag:(long)tag
{
    NSString *content = [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding];
    [self addText:content];
}

/**
 数据发送成功

 @param sock socket
 @param tag tag
 */
- (void)socket:(GCDAsyncSocket *)sock didWriteDataWithTag:(long)tag
{
    NSLog(@"%s",__func__);
    //发送完数据手动读取，-1不设置超时
    [sock readDataWithTimeout:-1 tag:tag];
}

- (void)didReceiveMemoryWarning
{
    [super didReceiveMemoryWarning];
}
@end
```
- [Demo下载](https://github.com/mrscorpion/MSAsyncSocketDemo)

![演示图](http://www.2cto.com/uploadfile/Collfiles/20151229/2015122909193754.gif)


---
##### iOS Socket使用总结
如果需要在项目中像QQ微信一样做到即时通讯，必须使用socket通讯。
ios原生的socket用起来不是很直观，所以一般使用AsyncSocket第三方库，其对socket的封装比较好，只是好像没有带外传输（out—of-band） 如果服务器需要发送带外数据，可能得想下别的办法。
> 即时通讯最大的特点就是实时性，基本感觉不到延时或是掉线，所以必须对socket的连接进行监视与检测，在断线时进行重新连接，如果用户退出登录，要将socket手动关闭，否则对服务器会造成一定的负荷。
一般来说，一个用户（对于ios来说也就是我们的项目中）只能有一个正在连接的socket，所以这个socket变量必须是全局的，这里可以考虑使用单例或是AppDelegate进行数据共享，本文使用单例。如果对一个已经连接的socket对象再次进行连接操作，会抛出异常（不可对已经连接的socket进行连接）程序崩溃，所以在连接socket之前要对socket对象的连接状态进行判断
使用socket进行即时通讯还有一个必须的操作，即对服务器发送心跳包，每隔一段时间对服务器发送长连接指令（指令不唯一，由服务器端指定，包括使用socket发送消息，发送的数据和格式都是由服务器指定），如果没有收到服务器的返回消息，AsyncSocket会得到失去连接的消息，我们可以在失去连接的回调方法里进行重新连接。



---
来源：网络整理
### 参考
- 原理篇
    - [iOS开发网络篇—Socket编程](http://www.cnblogs.com/hissia/p/5687769.html)
    - [关于iOS socket都在这里了](http://www.cocoachina.com/ios/20160602/16572.html)
    - [IOS　Socket使用大全](http://blog.csdn.net/ch_soft/article/details/7369705/)
- 实践篇
    - [CocoaAsyncSocket](https://github.com/robbiehanson/CocoaAsyncSocket)
    - [iOS学习之Socket使用简明教程－ AsyncSocket](https://my.oschina.net/joanfen/blog/287238)
    - [iOS中 HTTP/Socket/TCP/IP通信协议详解](http://www.2cto.com/kf/201512/455635.html)
