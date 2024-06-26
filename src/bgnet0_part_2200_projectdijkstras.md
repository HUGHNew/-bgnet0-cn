# 项目6: 用Dijkstra算法进行路由

通过内部网关协议，网络中的路由器可以彼此共享信息，这使得每个路由器可以了解它们自己所在网络。这样，每个路由器都可以在给定IP地址的情况下做出自主的路由决定。无论它要求去哪里，路由器总能把数据包转发到正确的方向。像开放最短路径优先(OSPF)这样的内部网关协议的实现使用Dijkstra算法沿着加权图寻找最短路径。

在这个项目中，我们将会模拟这种路由过程。我们将实现Dijkstra算法，打印出从一个IP到另一个IP的最短路径，显示中间所有路由器的IP。我们不会使用真正现实中的网络。相反，你的程序将读入一个包含网络描述的JSON文件，然后从中计算路由。

## 图知识复习

图由 _顶点_ 和 _边_ 组成。有时顶点被写作为“vertexes”或“verts”或“nodes（节点）”。边是从一个节点到另一个节点的连接。图上的任何节点都可以连接到任意数量的其他节点，这个数字甚至可以是零。一个节点可以连接到每一个其他节点。它甚至可以与自己连接。一个边还可以含有 _权重_， 通常这是指当你遍历图中的一条路径时所需要的代价。

例如，想象一张显示城市和城市间高速公路的地图。其中每条公路都标有它的长度。在这个例子中，城市是顶点，高速公路是边，高速公路的长度就是边的权重。

当你在这个图上寻找路线时，目的都是能够花最小的代价到达目的地。在我们的地图上，我们的目标是选择一条距离最短的路，从我们的起始城市出发，穿过中间的城市，到我们的目的地城市。

## Dijkstra算法简介

Edsger Dijkstra (_DIKE-struh_)是一位著名的计算机科学家，他提出了很多东西，但其中有一个非常有影响力，以至于人们用他的名字来命名:Dijkstra的算法。

> 高级技巧: 记住如何拼写"Dijkstra"的小技巧就是记住这其中"ijk"是按字母顺序出现的。

如果希望在未加权的图中找到节点之间的最短路径，只需执行宽度优先遍历，直到找到所要查找的目的地。这里的距离我们只能用“跳数”来衡量。但是如果你给节点之间的边添加权重，BFT就不能帮助你解决这个问题。也许有些路径是非常理想的，而另一些则是非常不理想的。

而Dijkstra的算法可以完成这个目标，这是一个变化的BFT。算法的要点是:从起点向外探索，只追求到目前为止总长度最短的路径。而每条路径的总权重是其所有边的权重之和。

在一个良好联通的图中，从起点到终点会有很多可能的路径。但是由于我们目前只需要找到最短路径，如果我们已经发现一条比100万英里短的路径，我们永远不可能再去找到一条让我们偏离100万英里的路径了。

## Dijkstra算法实现

Dijkstra算法基于图构建了一个树形结构，当你开始寻找从任何节点回到起点的最短路径时，该节点将其路径中的先前节点记录为其 _父节点_。如果稍后找到另一个更短的路径，则将父节点更换为新的更短路径的节点。[flw[Wikipedia 上有一个清晰图表来演示整个过程|Dijkstra's_算法]].

那么，到底该算法具体是如何工作的呢？Dijkstra算法本身只构建了表示回到起点的最短路径的树结构，稍后可以通过最短路径树找到一条最终的最短路径。

* 从一个起点开始计算图上所有最短路径的Dijkstra算法具体方法如下:
  * 初始化:
    * 创建一个空的叫`to visit` 集合，这是我们需要访问的所有节点的全集。
    * 创建一个叫`distance`字典。对于任何给定的节点(作为键)，它将保存从该节点到起始节点的距离。
    * 创建一个叫`parent` 字典。对于任何给定节点(作为键)，它保存(沿着最短路径)指向起始节点的节点的键集合。
    * 对于每一个节点:
      * 将它的`parent`值设置为 `None`。
      * 将它的`distance`值设置为无穷。（Python有一个默认表示无穷的值`math.inf`，不过你也可以使用一个超级大的数，比如，四十亿。）
      * 将节点加入`to_visit`集合。
    * 将开始节点的distance值设置为`0`。

  * 运行：
    * 当 `to_visit` 不为空时:
      * 找到`to_visit`里`distance`最小的节点，将其称为“当前节点”。
      * 从`to_visit`集合中移除当前节点。
      * 对于每个仍然还在`to_visit`集合中当前节点的邻居:
        * 计算每个从开始节点到这些邻居节点到距离，这个距离等于到当前节点的距离加上当前节点到邻居节点的边权重。
        * 如果当前计算出来的新距离比邻居节点当前存储的`distance`值更小时：
          * 将邻居节点的`distance`值设置为当前计算的新距离。
          * 当邻居节点的`parent`值设置为当前节点。
        * [这个过程被称之为“松弛”。节点距离从无穷大开始，然后“松弛”到它们的最短距离。]

Wikipedia提供了上述过程的伪代码，如果你觉得伪代码对于你更容易理解的话,你可以阅读下面的伪代码:

``` {.py}
 1  function Dijkstra(Graph, source):
 2
 3      for each vertex v in Graph.Vertices:
 4          dist[v] ← INFINITY
 5          prev[v] ← UNDEFINED
 6          add v to Q
 7      dist[source] ← 0
 8
 9      while Q is not empty:
10          u ← vertex in Q with min dist[u]
11          remove u from Q
12
13          for each neighbor v of u still in Q:
14              alt ← dist[u] + Graph.Edges(u, v)
15              if alt < dist[v]:
16                  dist[v] ← alt
17                  prev[v] ← u
18
19      return dist[], prev[]
```

在这个阶段，我们已经构造了由所有`parent`指针组成的树。为了找到从一个点回到起点(树的根)的最短路径，你只需要沿着那个点回到树的`parent`节点的路线。

* 获取从起始点到目的最短路径的方法:
  * 将当前节点设置为**目标**节点。
  * 将`path`设置为一个空数组。
  * 当前节点不是起始节点时:
    * 将当前节点加入`path`。
    * 当前节点等于当前节点的`parent`节点
  * 将起始节点加入路线

当然，这将以相反的顺序构建路径。但是这是必要的过程，因为这样父指针都指向作为根节点的起始节点。所以要么只要在最后将其反转，要么将目的地节点作为起始节点运行Dijkstra算法。

### 获得最小距离

算法的一部分是找到仍然在`待访问`集合中`距离`值最小的节点。在本项目中，你可以使用一个复杂度为`O(n)`的线形搜索来找到当前节点的最短路径值。但是，在实际例子中，如果使用线形搜索，复杂度将会太高--那将是和节点个数相关的`O(n²)`复杂度。所以在实现时会使用[小顶堆](https://en.wikipedia.org/wiki/Binary_heap) 这样的数据结构，它最快将会提供接近 `O(log n)`复杂度，这使得我们在处理众多节点时，也拥有`O(log n)`复杂度。

如果你想挑战自己一下，可以尝试使用小顶堆。

## 我们的这个项目中都有什么？

[ _本项目中的所有IP地址都是IPv4地址._ ]

[fls[在这里下载本项目框架代码的压缩包|dijkstra/dijkstra.zip]].

这里面又很多通用的内容，那么，其中有哪些适合本项目相关的呢？

### 函数，输入和输出

你需要实现这个函数:

``` {.py}
def dijkstras_shortest_path(routers, src_ip, dest_ip):
```

该函数的输入是:

* `routers`: 一个代表图的字典
* `src_ip`: 一个点-数字符串，代表源IP地址
* `dest_ip`: 一个点-数字符串，代表目的地IP地址

该函数的输出是:
* 一个字符串数组，其中是所有路由途中的路由器IP地址
  * **注意: 如果源IP地址和目的地IP地址位于相同的子网中，返回一个空的数组。** 在这种情况下，将会产生路由路线。

函数的运行代码已经包含在上面的框架代码中，它将输出到控制台，如下所示，显示源、目标和中间的所有路由器:

``` {.default}
10.34.46.25 -> 10.34.166.228    ['10.34.46.1', '10.34.98.1', '10.34.166.1']
```

### 图的表示方式

`routers`变量中保存的图字典看起来如下面片段所示:

``` {.json}
{
    "10.34.98.1": {
        "connections": {
            "10.34.166.1": {
                "netmask": "/24",
                "interface": "en0",
                "ad": 70
            },
            "10.34.194.1": {
                "netmask": "/24",
                "interface": "en1",
                "ad": 93
            },
            "10.34.46.1": {
                "netmask": "/24",
                "interface": "en2",
                "ad": 64
            }
        },
        "netmask": "/24",
        "if_count": 3,
        "if_prefix": "en"
    },
     
    # ... etc. ...
```

最外层的键值（比如说`"10.34.98.1"`）是路由器的IP，他们是图的顶点。对于每一个顶点，都有一个`"connections"`的列表，也就是这其中图的边。

在每个链接中，还拥有一个字段`"ad"`，表示边的权重。

> "AD" 是 _管理距离_ 的缩写，它表示手动或自动(或两者混合)设置的权重，用于定义特定路由的成本。
> 默认值是110，越高的数值表示成本越高。
>
> 这个度量包含关于路由的许多想法，包括它提供了多少带宽、它有多拥塞、管理员希望使用(或不使用)多少，等等。

`"netmask"`字段中填写的是路由器IP的子网掩码，并且该字段也存在于所有链接的路由上。`"interface"`字段表示路由器上的哪个网络设备连接到哪个相邻的路由器，但在本项目中没有使用。

`"if_count"`和`"if_prefix"`也并没有在这个项目中被使用。

## 输入文件和示例输出

示例代码框架包括一个实例输入文件 (`example1.json`)和一个相当于这文件期待的输出 (`example1_output.json`)。

## 提示

* 本项目高度依赖于你在上一个项目中编写的网络功能!
* 在制定计划之前先完全理解这个项目的描述!
* 在编写任何代码之前，尽可能多地想出一个计划!


<!--
计分表:

12 
从源IP地址正确的选出开始路由器的IP。

12
从目的地IP地址正确的选出结束点路由器的IP。

8
最短路径中不包含起始点IP地址。

8
最短路径中不包含结束点IP地址。

20
最短路径数组包含所有从开始点到结束点路由线路上的IP地址。

20 
最短路径数组中包含正确的实际最短路径。

12
当源地址和目的地址在同一子网时，返回空最短路径。

5
框架代码中要求不被修改的代码未被修改。
-->


