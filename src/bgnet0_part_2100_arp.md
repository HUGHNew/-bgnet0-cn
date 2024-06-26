# ARP: 地址解析协议

作为一台网络中联网的计算机，我们有一个实际的问题。我们想通过局域网将数据从一台计算机发送到另外一个相同的子网。

这是我们需要按顺序构造因特网数据帧的过程:

* 我们将要发送的数据和它的长度
* 我们的源MAC地址
* 对方目的地的MAC地址
  
而我们目前知道的却只有:

* 我们将要发送的数据和它的长度
* 我们的源MAC地址
* 我们的源IP地址
* 对方的IP地址
  
这里我们缺少了什么？即使我们知道了对方电脑的IP地址， _我们也不知道其MAC地址_。 那么我们怎么样才能在没有MAC地址的情况下建立一个以太网帧？这肯定是不可以的，我们需要用某种方式拿到它。

> 再一次，在本节中，我们在谈论在局域网中发送数据，也就是本地以太网。而不是通过IP链接的
> 因特网。最重要的是，局域网中的两台电脑是通过相同的物理层网络链接的。
> Again, for this section we're talking about sending on the LAN, the
> local Ethernet. Not over the Internet with IP. This is between two
> computers on the same Physical Layer network.

本节的所有内容都是关于ARP,地址解析协议。这是个如何一台计算怎么将其他计算机的IP地址映射到它的MAC地址的方法。

## 以太网广播帧

在开始之前，我们需要一些背景知识。回想一下前面介绍过的，网络硬件监听专门针对它的以太网帧，这样其他MAC地址目的的以太网帧将被忽略。

> 旁注: 这些非本人MAC地址的将被忽略，除非将网卡被设置为[_混杂模式_](https://en.wikipedia.org/wiki/Promiscuous_mode),
> 该模式会接收局域网中 **所有** 流量并且将其转发给操作系统。

但是这也有一种例外: _广播数据帧_。这是一个目标MAC地址为`ff:ff:ff:ff:ff:ff`的帧。局域网中的所有设备都将接收到该帧。

我们将在ARP中使用它。

## ARP--地址解析协议

在最开始的情况下，我们有目的地的IP地址，但没有它的MAC地址。而我们需要它的MAC地址。所以，ARP协议会按以下的步骤，让我们获得对方的MAC地址：

1. 源地址计算机会向网络中广播一个特殊的含有目的地IP地址的以太网帧，被称为 _ARP 请求_。（还记得前面一节中的以太网类型字段么？ARP数据包使用0x0806来区分普通以太网数据包和ARP请求包。）

2. 所有在局域网中的计算机都会收到这个ARP请求并且对其进行校验。不过，只有拥有目的地IP地址的计算才会继续处理该ARP请求，其他计算机将会丢弃该数据包。

3. 含有指定IP地址的目的地计算机会构建一个 _ARP应答_。这个数据帧中含有目的地计算机的MAC地址。

4. 目的地计算机地址会将ARP应答发回源计算机地址。

5. 源计算机地址接收到ARP应答后，从该包中就能知道目的地电脑的MAC地址了。

一切要素都齐了!既然我们知道了MAC地址，我们就可以不受约束地发送了。

## ARP 缓存

每次都通过发送一些东西向一个计算机询问其MAC地址会使得流程十分繁杂，所有我们将结果 _缓存_ 一会儿。然后，当我们想要发送到局域网上的特定IP时，我们可以先查看 _ARP缓存_ 并查看IP/以太网对是否已经存在。如果存在，就不需要发送ARP请求——我们可以立即传输数据。

整个缓存会在一段时间之后失效并且会被清除，在规定上，没有一个标准的超时时间，不过我所了解的，从60秒到4个小时的超时时间都有。


如果给定IP地址的MAC地址发生变化，则缓存条目可能会失效。发生这种情况的最常见的情景就是，如果有人关闭了他们的笔记本电脑并离开了网络(带走了他们的MAC地址)，然后另一个拥有不同笔记本电脑(和不同MAC地址)的人出现并分配了相同的IP地址。如果发生这种情况，具有陈旧条目的发送端计算机就会把该IP的帧发送到错误的(旧的)MAC地址。

## ARP 结构

ARP数据属于以太网帧的负载的一部分，它具有固定的长度。像前面提到的那样，它将会设置以太网类型/数据包字段为0x0806。在下面的有效载荷结构中，当说“硬件”时，它指的是链路层(例如本例中的以太网)，当说“协议”时，它指的是网络层(例如本例中的IP协议)。它在字段中填入这些对应的名称，因为没有硬性的要求ARP一定要使用以太网或IP——它也可以与其他协议一起工作。

负载字段，是一个具有固定长度的28位八元组,其中具体包括:

* 2位八元组: 硬件类型(以太网是`0x0001`)
* 2位八元组: 协议类型（IPv4是`0x8000`）
* 1位八元组: 以八元组表示的硬件地址长度（以太网是`0x06`）
* 1位八元组: 以八元组表示的协议地址长度（IPv4是`0x04`）
* 2位八元组: 操作类型(`0x01`是请求, `0x02`代表回复)
* 6位八元组: 发送端硬件地址（发送端MAC地址）
* 4位八元组: 发送端协议地址（发送端IP地址）
* 6位八元组: 接收端硬件地址（接收端MAC地址）
* 4位八元组: 接收端协议地址（接收端IP地址）

## ARP 请求/回复

这里有点令人困惑的是，因为“发送方”字段总是传输数据的计算机设置的，而不是从谁是请求者的角度去设置的。所以我们将会声明1号计算是ARP的请求方，而2号计算机将会对其进行应答。

如果1号计算机发送一个ARP请求（”如果这是的你IP，那么你的MAC地址是什么?"）,那么它设置以下字段(除了上面提到的模版中的其他ARP请求字段之外):

* **发送端硬件地址**: 1号计算机的MAC地址
* **发送端的协议地址**: 1号计算机的IP地址
* **目的端的硬件地址**: 未使用
* **目的端的协议地址**: 1号计算机的所要询问的目的地IP地址


在2号计算机做出的ARP应答中("我拥有该IP地址，这是我的MAC地址"),它将会设置一下的字段:

* **发送端硬件地址**: 2号计算机的MAC地址
* **发送端的协议地址**: 2号计算机的IP地址
* **目的端的硬件地址**: 1号计算机的MAC地址
* **目的端的协议地址**: 1号计算机的IP地址

当1号计算机接收到声称是它要寻找的计算机的ARP回复时，它将会查看发送者字段并且从中提取出对应的MAC地址和IP地址。在这之后，1号计算机就可以通过以太网向2号计算机发送数据了，因为对于它，2号计算机现在是一个已知的MAC地址了。这就是如何在通过一个特定IP地址来查询到对应MAC地址的方法。

## ARP的其他功能

### ARP 公告

刚刚连上网的计算机主动宣布其ARP信息并不罕见，因为这可以让其他人有机会将数据添加到他们的缓存中，并且覆盖这些缓存中可能过时的数据以进行更新。

### ARP 探测
一台计算机可以发出一个特殊构造的ARP请求，这个请求基本上是问:“还有其他人在使用这个IP地址吗?”通常发送端使用自己的IP地址进行请求，如果它得到响应，那么它就知道网络中有IP冲突。

## IPv6 和 ARP

IPv6 拥有它自己的ARP版本，叫做[NDP](https://en.wikipedia.org/wiki/Neighbor_Discovery_Protocol) (邻居发现协议)。眼尖的人可能已经注意到，ARP只支持最多4字节的协议地址(比如说，IP地址)，而IPv6地址是16字节。


NDP解决了这个问题，并定义了许多可以用来代替ARP的协议[ICMPv6](https://en.wikipedia.org/wiki/Internet_Control_Message_Protocol_for_IPv6)(因特网消息控制协议(IPv6))消息。

## 思考题

* 请描述下ARP协议是为了解决什么问题。

* 为什么在ARP缓存中的条目需要有超时时间？

* 为什么IPv6不能使用ARP？