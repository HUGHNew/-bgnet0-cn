# 因特网协议版本6（IPv6）

这是一个新时代的方案!因为能用32位表示的地址太少了(不包括保留的地址，只有4,294,967,296个)，互联网规则的决定者们觉得需要一个新的定址方案，一个使用更多位数，可以涵盖一切意图和需求并且能一直使用的方案。

随着网络的发展，我们已经遇到一个问题: IP地址不够用了。溯洄到1970年代，一个拥有数十亿计算机的世界是超出想象的，但是，时至今日，世界上的计算机数量已经远远超过这个维度。所以，互联网规则的制定者们决定将IP地址从32位增加到128位，这样将会有 79,228,162,514,264,337,593,543,950,336的地址空间，这可以足够被使用一个相当相当长的时间。
> 虽然在这个地址空间中也有很多保留地址，但是这个位数，仍然有**很多**地址空间，无论你用什么角度去查看。

而这是IPv4和IPv6的一大不同点。

出于演示目的，我们将继续使用IPv4，因为它依然是最常见的IP地址，并且更易于编写。但需要知道的是，总有一天IPv6将成为唯一的地址表示方法。

## 简介

基于IPv6有那么多的地址空间，光用点和十进制数字是不够的，所以它们想到了一种新的表示IPv6地址的方法:冒号加上十六进制数字。由于每一个每一个十六进制数字有16位，所以我们需要用8个这样的数字来表示128位。比如说:

和IPv4一样，使用斜线来表示子网，比如下面这个地址表示64位网络位(用`/64`来表示)和64位主机位(因为`128-64=64`)

拥有64位来表示主机号！这意味着在该子网段，可以拥有18,446,744,073,709,551,616 台主机！正如所示，用IPv6可以表示很多地址！

> 当我们讨论特定主机的标准IPv6地址时，`/64`是一种强力推荐的规则来说明你的子网有多大，甚至一些协议依赖于此。
> 但当我们讨论子网时，你可能会看到较小的数字表示较大的地址空间，但是最终都会期望于将该空间地址继续划分进入`/64`子网段以用来装填单独的主机。

很明显，编写所有这些十六进制数字可能会显得有点笨拙，特别是如果其中有大量的零，所以有一些简写规则。
1. 在一个16位数段中，可以移除开头的位为0的值。
2. 在应用规则1之后，如果出现多个连续的零，可以用两个连续的冒号替换。

让我们来看一个实例，假设我们有如下一个地址:

``` {.default}
2001:0db8:6ffa:0000:0000:00ab:98bf:070a
```

首先，让我们运用规则一来移除先头的0:

``` {.default}
2001:db8:6ffa:0:0:ab:98bf:70a
```

现在，我们看到了中间有两段是连续的`0`，根据规则二我们可以直接将其替换为两个冒号:

``` {.default}
2001:db8:6ffa::ab:98bf:70a
```

按照这种规则，我们得到了一个更加简洁的表示。

## 链路本地地址

[这是那种“最好能了解下”的支持，但简单翻译就是“IPv6会自动给所有接口分配一个IP地址”。]

无论是IPv4还是IPv6，都会有一些保留地址给特定的局域网。这些地址通常不会被IPv4使用，但是在IPv6中，它们是有用处的，这些地址全部位于子网段`fe80::/10`。
> 将它展开，就是这样:
> ``` {.default}
> fe80:0000:0000:0000:0000:0000:0000:0000
> ```

这里前10位是网络部分，在IPv6链路本地地址中，接下来的54位被保留(`0`)，然后剩下64位用来标识主机。当一个IPv6接口被激活时，它会根据以太网地址和其它相关的信息自动计算它的链路本地地址。

链路本地地址在局域网中是唯一的，但是不一定是全局唯一的，路由器将不会将任何链路本地地址的数据包转发出局域网，这样可以避免出现重复IP的问题。
例如，如果DHCP服务器分发一个IP，网络接口可能稍后获得不同的IP，在这种情况下，它将有两个IP。

## 特殊的IPv6地址以及子网段

如同IPv4一样，有一些地址拥有特殊的含义。
* **`::1`** - 本地地址，当前计算机，IPv6版本的`127.0.0.1`
* **`2001:db8::/32`** - 用于在文档中作为示例地址段
* **`fe80::/10`** - 链路本地地址段

这些IPv6的范围拥有特殊的意义，不过这些都是常见的地址范围。

## IPv6 和 DNS

同样，在IPv6中，DNS将IPv6地址转化为人类可读的名称，你可以使用`dig`命令来查找`AAAA`记录（被DNS称之为IPv6地址记录）。

``` {.default}
$ dig example.com AAAA
```

``` {.default}
; <<>> DiG 9.10.6 <<>> example.com AAAA
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 13491
;; flags: qr rd ra ad; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 1232
;; QUESTION SECTION:
;example.com.			IN	AAAA

;; ANSWER SECTION:
example.com.		81016	IN	AAAA	2606:2800:220:1:248:1893:25c8:1946

;; Query time: 14 msec
;; SERVER: 1.1.1.1#53(1.1.1.1)
;; WHEN: Wed Sep 28 16:05:16 PDT 2022
;; MSG SIZE  rcvd: 68
```

可以在上面`ANSWER SECTION`看到`example.com`的IPv6地址。

## IPv6 与 URL

由于URL使用`:`来分割端口号，这造成了它和IPv6中的`:`的意思会发生冲突。假设你的服务跑在端口33490上，你可以在你的网页浏览器中输入IPv6地址，但将其放入中括号中。比如，可以用如下字符串去连接一个跑在本地`::1`地址上的该服务:

``` {.default}
http://[::1]:33490/
```

## 思考题

* IPv6相对于IPv4的好处是什么？

* 如何将`2001:0db8:004a:0000:0000:00ab:ab4d:000a`写的更加简洁？
