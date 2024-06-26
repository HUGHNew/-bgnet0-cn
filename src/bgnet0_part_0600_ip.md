# 因特网协议（IP）

该协议负责在因特网上路由数据包，类似于邮局负责在邮件网络中路由传递信件。与邮局系统一样，Internet上的数据必须标有源地址和目的地址，即 _IP地址_。

IP地址是一个字节序列，它唯一地标识因特网上的每台计算机。

## 术语
* **主机** - “计算机”的另一个称呼。

## 两个主要的版本

IP协议两个主要版本: 版本4和版本6。IP协议版本4指的是 "IPv4" 或者简称 "IP"，IP协议版本6通常会显示的标记为“IPv6”。

你可以从如下的两个例子中瞥见IP地址的模式:
* 版本4的IP地址形如:192.168.1.3
* 版本6的IP地址形如:fe80::c2b6:f9ff:fe7e:4b4
主要区别在于组成地址空间的字节数。IPv4为每个地址4字节，IPv6为每个地址16字节。

## 子网

每个IP地址都可以被分割成两个部分，第一部分的用来标示独立的网络，第二部分用来标示该网络中独立的主机（例如，计算机）。这些单独的网络被称为 _子网_，它们可以支持的主机数量取决于在地址中为识别该子网上的主机保留了多少位。

举一个人造的但是非因特网中真实使用的例子，我们用8位表示一个“地址”，假设其中前六位是网络编号，而后两位是主机编号。按照这个逻辑，一个地址可能看起来如下所示:

``` {.default}
00010111
```

该地址被分割称为两个部分（因为我们定义了前6位是网络编号）:

``` {.default}
Network  Host
-------  ----
000101   11
```

所以该地址表示网络5（二进制`101` ），主机3（二进制`11`），一个地址中网络号永远都在主机号的前面。

注意，如果只用2位表示机器，那么在这个子网中一共最多只能有4个主机编号，分别是0，1，2，3（或者用二进制表示`00`, `01`, `10`, 和 `11`）。如果按照IP协议的定义，那么真正能用的只有两个主机编号，因为全0的主机编号和全1的主机编号是保留编号。

下一章，我们将会查看两个具体的IPv4和IPv6的例子，这一章最重要的知识就是每一个地址都会分成网络和主机两个部分，网络部分在主机部分之前。

## IP层额外协议

在IP协议的同一层，还有一些协同IP协议工作的其它协议。

* **ICMP**: 互联网控制消息协议, 这是一种用于在IP节点之间交流IP控制元数据的协议。
  
* **IPSec**: 网际安全协议, 负责加密，认证等功能，一般和VPN(虚拟专用网)一起使用。

用户通常会的使用`ping`这个程序利用ICMP协议，该命令使用ICMP协议的“回声请求”和“回声应答”消息。而`traceroute`程序则利用了ICMP的“超时”消息来找到数据包是如何被路由的。

## 私有网络

私有网络是指那些藏在路由器之后的，并不拥有全球唯一IP地址的机器（但是，它们在各自的局域网中有自己独立的地址。）。该技术是通过一种叫做NAT(网络地址转换协议)来实现的，我们将会在后面的章节详细介绍。目前，让我们先假设我们接触到的所有地址都是全球唯一的。

## 静态地址，动态地址以及DHCP协议

如果你有一个需要被访问的网站，或者一个你不时要去通过SSH连接的服务器，你需要一个 _静态IP_，这表示你需要一个被分配的全局唯一的IP地址并且它不会发生改变。这个很像门牌号，如果你需要别人能找到你的家，你的门牌号是不能随意更改的。

不过由于IPv4地址的数量是有限的，静态IP是很昂贵的。通常，网络服务提供者会有一系列的带有子网的IP地址并且会 _动态_ 分配给需要的用户。这意味着，当你重启你的调制解调器的时候，你的所有终端可能会被分配到完全不同的IP地址。（除非你花费了价钱去买了一个静态IP。）

事实上，当你的笔记本电脑连上WiFi时，基本上你获得的都是一个动态IP地址，你的计算机会连上局域网，并且广播一个数据包说:"你好，我在这里！有没有人可以告诉我我的IP地址是什么？最好是能带有更多的一些元信息。"这是一个合理的过程，因为大部分情况下，不会有程序会尝试连接你笔记本电脑上的服务器程序，通常，都是笔记本电脑去连接其它服务器程序。

那么，这其中详细的原理是什么？这就要说到DHCP协议了，通常在局域网上有一个专用的服务器，上面会运行一个符合DHCP（_动态主机配置协议_）协议的程序。DHCP服务器会负责记录在局域网中，哪些IP地址已经被分配并且正在被使用，哪些还未被使用，处于空闲状态。它会找到一个未被使用的IP地址，并且使用DHCP回复消息告诉你的笔记本电脑新的IP地址是什么，并且还会附带局域网上的一些其它你的电脑接入需要的信息。（比如，局域网掩码，等等。）

如果你家中有WiFi，那么大概率你已经有了一个DHCP服务器，大部分从你的网络提供商买来的路由器都已经设置好了DHCP，这使得你的笔记本电脑可以从你的局域网中获取到相应的IP地址。

## 思考题

* IPv6地址的总数量是IPv4地址的多少倍？
  
* 应用层程序通常会有自己的加密算法的实现（比如，ssh或者带有HTTPs的网页浏览器）。思考下，使用网络层的IPSec加密而不用在应用层加密的好处和坏处都有什么？

* 如果一个子网保留5位作为主机标示符，那么该子网中可以支持多少主机？别忘了全0地址和全1地址都是保留地址。

* 拥有一个静态IP地址的好处是什么？它和DNS的关系又是什么？
