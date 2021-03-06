
# （二）交换机

- 帧转发技术
- MAC地址表的维护方式
- 三种帧转发方式
- 冲突域和广播域

-------------------
## 从 Ingress 说起
你玩过 Ingress 游戏吗？是蓝军还是绿军呢?<br>
2016年7月份的时候，我在深圳腾讯实习。那时候认识了一个小伙伴，带我玩 ingress。其实玩的也是似懂非懂，就知道可以面基其他玩家。还面基了一个腾讯的同事，看到我是妹子觉得很懵逼233.<br>
顺便说句，在那时候我是蓝军，来了新加坡，我就叛变为绿军了。<br>
Ingress 游戏的中文名大概可以翻译为 “虚拟入口”。但其实，在计算机网络的世界里面，术语`ingress`用于描述帧通过特定端口进入交换机设备。<br>
这里就出现了三个你可能不懂的词语：“帧”，“端口”，“交换机”。没关系，来了解一下`交换机`是什么你就会懂了。<br>
## 交换机和路由器
路由器大家都不会陌生，都是见过摸过的。其实路由器还有一个名字“分组交换机”。其实交换机呢，也有一个全名，叫“链路交换机”。<br>
通过名字，你就能直观地感受到，路由器处理的是 IP packet，是工作在下三层的，而交换机处理的是以太帧，是工作在数据链路层，下二层的。不过其实现在也有三层交换机。<br>
既然本质上都是交换机，那么交换机应该有什么呢？如果给交换机建个模，交换机至少应该有“输入端口”，“输出端口”和“交换结构”（连接交换机的输入端口与输出端口）。<br>
而事实上，路由器有四个组成部分：输入端口、输出端口、交换结构、路由选择处理器。 <br>
路由选择处理器又是什么东西呢?<br>
是用来执行路由选择协议、维护路由选择表以及连接的链路状态信息，并为路由器计算转发表的组件。也可以执行网络管理功能。<br>
最主要的功能是围绕“路由转发”的。<br>

------
你可能不懂什么叫“路由转发”。也想问什么是路由?<br>
其实可以来拆字理解：<br>
路：路径的意思，就是数据的转发路径；<br>
由：由来的意思，也就是说路径的由来，寻址的意思。<br>
所以路由嘛，就是用来做数据转发路径的寻址。<br>

近段时间我在写 ReactJS，要用到 Route 库，做一些页面跳转之类。在这种web开发中，`route` 就是指根据 url 分配到对应的处理程序。<br>
但是 web 开发中的路由不是指路由器和网络协议中的路由协议，不过基本思想是一样的。<br>
网络原理中，路由指的是根据上一接口的数据包中的 IP 地址，查询路由表转发到另一个接口，它决定的是一个端到端的网络路径。所以也可以说路由协议是用来转发的。<br>
至于路由协议和路由表的生成和维护，那又是一个很长的故事了。以后再说。<br>
**只需记住，网络中的路由就是根据 IP 地址，查询路由表寻址，然后转发数据包的。**<br>

----
路由器就是网络层设备，用来做 IP 寻址的，做不同网络之间的转发。而局域网内部呢？其实在 01 笔记里面，我已经提到过：**局域网内使用链路交换机，基于MAC而非IP寻址**。<br>
- 由于交换机只需要查看 二层数据帧 的头部即可决策转发地址，策略十分简单，可以直接通过硬件芯片实现相应功能，所以可以做到廉价高速，被大量应用在接入层。
- 而路由器由于需要处理跨网络的连接，必须在接收到完整的 IP数据包 后才能转发数据，路由协议又比较复杂，所以只能使用软件的方式实现相应的功能，要达到高性能只能付出更高的价格。
- 另外，由于二层转发只需要查看 帧头部 即可开始转发，也使得 (二层)交换机 有了一项独门功夫：直通转发。简单说就是只接收数据帧头部就开始转发，从而达到更高的性能。

总结来说，路由谋短，交换求快。出于速度、性能和经济成本的考虑，在同一局域网内，使用 LAN switches 进行交换。<br>

所以交换机是二层设备（也有三层交换机），基于 MAC 地址寻址转发，处理的 PDU（01说过的，协议数据单元）是以太帧。<br>

-------
大家注意，学网络不仅仅是协议，硬件、设备也很重要。<br>




## 帧转发
![](https://raw.githubusercontent.com/XuemingNotCute/MarkdownPhotos/master/5.png)
如上图，[01](https://github.com/XuemingNotCute/NetworkNotes2nd/blob/master/01Network_Transfer.md) 里面我们说过:
>在给 Datagram 封装的过程中，应用层将HTTP报文头添加到 数据之前。然后 IP 报头被加到 TCP 信息之前。  Ethernet Protocol 添加到IP报文的两端之后，就形成了数据链路帧 Ethernet Frame。
上述帧发送至通向网络客户端的路径上的最近一个路由器。 路由器移除以太网信息，观察IP报文，判定最佳路径。将报文插入一个新的帧，并发送至目标路径上下一个相邻路由器。 每一个路由器在转发之前都移除并添加新的数据链路层信息。

注意，其他层的协议都只是在上一层的 datagram 之前添加本层协议的报头，而唯独帧，是添加在两端的。你应该已经注意到上图里面的`Frame header` 和 `Frame trailer` 了吧。

------
回忆了 01，你就知道：
以太网上的帧包含源MAC地址与目的MAC地址。<br>
交换机从源设备接收到帧并快速发往目的地址。(路由谋短，交换求快)<br>
交换的基本概念指基于以下两条准则做出决策的设备：
1. 进入（ingress）端口
2. 目的地址
术语`ingress`用于描述帧通过特定端口进入设备，`egress`用于描述设备通过特定端口离开设备。<br>
交换机做出转发决定的时候，是基于进入端口以及消息的目的地址的。<br>
LAN交换机维护一张表，通过这张表决定如何转发数据流。<br>
LAN交换机唯一智能部分是利用这张表基于消息的进入端口和目的地址来转发。<br>
一个LAN交换机中只有一张定义了目的地址和进入端口的主交换表；<br>
因此，无论进入端口如何，同一目的地址的消息永远从同一出口离开。

## MAC地址表
前面说到了IP路由表，那么与之对应，也有MAC地址表。
我们知道 IP 路由表是动态自动更新的。其实 **MAC 地址表也是动态更新的。**

一个交换机要知道使用哪一个端口传送帧，首先必须学习各端口有哪些设备。<br>
随着交换机学习到端口与设备的关系，它建立起一张MAC地址表，或内容可寻址寄存表（CAM）。<br>
CAM是一种应用于高速查找应用的特定类型的memory。交换机将连接到它的端口的设备的MAC地址记录到MAC表中，然后利用表中信息将帧发送至输出端口设备，该端口已指定给该设备。<br>
**记住交换机操作模式的一句简单的话是：交换机学习“源地址”，基于“目的地址”转发**。<br>
帧进入交换机时，交换机“学习”接收帧的源MAC地址，并将此地址添加到MAC地址表中，或刷新已存在的MAC地址表项的老化寄存器；后续报文如果去往该MAC地址，则可以根据此表项转发。帧转发时，交换机检查目的MAC地址并与MAC地址表中地址进行比较。如果地址在表中，则转发至表中与MAC地址相对应的端口。如果没有在表中找到目的MAC地址，交换机会转发到除了进入端口以外的所有端口泛洪（flooding泛洪就是广播）。<br>
有多个互连交换机的网络中，MAC地址表对于一个连接至其他交换机的端口记录多个MAC地址。

## 更新 MAC 地址表的方法
1. 交换机在port 1接收到来自PC 1的帧。<br>![](https://raw.githubusercontent.com/XuemingNotCute/MarkdownPhotos/master/6.png)
2. . 交换机检查源MAC地址并与MAC地址表相比较。
- 如果地址不在表中，则交换机在MAC地址表中将PC 1的源MAC地址关联到进入端口（port 1）。<br>
注：格式类似于字典的键值对-> **port no. : host**<br>![](https://raw.githubusercontent.com/XuemingNotCute/MarkdownPhotos/master/7.png)
- 如果已经存在该源地址的MAC地址表项，则交换机重置老化计时器。通常一个表项会保持5分钟。

3.  交换机记录源地址信息之后，检查目的地址
- 如果目的MAC地址不在表项中或如果它是一个广播MAC地址，则交换机把该帧泛洪（flood）至除了进入端口以外的所有端口。<br>![](https://raw.githubusercontent.com/XuemingNotCute/MarkdownPhotos/master/8.png)

4. 目标设备（PC 3）返回目的地址为PC 1的单播帧。<br>![](https://raw.githubusercontent.com/XuemingNotCute/MarkdownPhotos/master/9.png)

5. 交换机地址表中输入PC 3的源MAC地址以及进入端口的端口号。在表项中找到该帧的目的地址及关联的输出端口。<br>![](https://raw.githubusercontent.com/XuemingNotCute/MarkdownPhotos/master/10.png)
6. 交换机现在可以在源和目标设备之间传送帧而无需泛洪，因为地址表中已有指定关联端口的表项。<br>![](https://raw.githubusercontent.com/XuemingNotCute/MarkdownPhotos/master/11.png)


## 交换机转发方式：
#### 存储转发交换(Store-and-Forward)
运行在存储转发模式下的交换机在发送信息前要把整帧数据读入内存并检查其正确性。<br>
尽管采用这种方式比采用直通方式更花时间，但采用这种方式可以存储转发数据，从而保证其准确性。<br>
由于运行在存储转发模式下的交换机不传播错误数据，因而更适合大型局域网。<br>
存储转发模式有两大主要特征区别于直通转发模式(还记得前面说过链路交换机可以直通转发嘛，就很快)：<br>
1. 差错控制：
使用存储转发技术的交换机对进入帧进行差错控制。在进入端口接收完整一帧之后，交换机将数据报最后一个字段的帧校验序列（frame check sequence, FCS）与自己的FCS进行比较。<br>FCS校验过程用以帮助确保帧没有物理及数据链路错误，如果该帧校验正确，则交换机转发。否则，丢弃。<br>也就是检查下两层错误。<br>![](https://raw.githubusercontent.com/XuemingNotCute/MarkdownPhotos/master/12.png)<br>
2. 自动缓存：
存储转发交换机**通过进入端口**缓存，支持不同速率以太网的混合连接。<br>
例如，接收到一个以1Gb/s速率发出的帧，转发至百兆以太网端口，就需要使用存储转发方式。当进入与输出端口速率不匹配时，交换机将整帧内容放入缓存中，计算FCS校验，转发至输出缓存之后将帧发出。
- Cisco思科交换机的主要交换方式是存储转发交换。



#### 直通交换（Cut-Through）
直通交换的一个优势是比存储转发技术更为快速。<br>采用直通模式的交换机会在接收完整个数据包之前就读取帧头，并决定把数据发往哪个端口。<br>
~~读完帧头就转发~~**读到目的地址就开始转发**（见下图）。<br>
不用缓存数据也不用检查数据的完整性。<br>
这种交换方式有两大特点：`快速帧转发`以及`无效帧处理`。<br>
1. **快速帧转发**：
如下图所示，一旦交换机在MAC地址表中查找到目的MAC地址，就立刻做出转发决定。<br>而无需等待帧的剩余部分进入端口再做出转发决定。<br>![](https://raw.githubusercontent.com/XuemingNotCute/MarkdownPhotos/master/13.png)<br>使用直通方式的交换机能够快速决定是否有必要检查帧头的更多部分，以针对额外的过滤目的。<br>例如，交换机可以检查前14个字节（源MAC地址，目的MAC，以太网类型字段），以及对之后的40字节进行检查，以实现IPv4三层和四层相关功能。
2. **无效帧处理**：
对于大多数无效帧，直通方式交换机并不将其丢弃。<br>
错误帧被转发至其他网段。<br>如果网络中出现高差错率（无效帧），直通交换可能会对带宽造成不利影响，损坏以及无效帧会造成带宽拥塞。<br>在拥塞情况下，这种交换机必须像存储转发交换机那样缓存。<br>(Doubt：什么时候直通交换机会缓存帧？拥塞情况下，也就是高差错率网络情况下)
3. **无碎片转发（Fragment Free）**
**无碎片转发是直通方式的一种改进模式。**<br>交换机转发之前检查帧是否大于64字节（小于则丢弃），以保证没有碎片帧。<br>**额，非常简单粗暴了。**<br>无碎片方式比直通方式拥有更好的差错检测，而实际上没有增加延时。<br>它比较适合于`高性能计算应用`，即进程到进程延时小于10毫秒的应用场景。


## 交换机域：
#### 交换机域：
交换机比较容易混淆的两个术语是`冲突域`和`广播域`。这一段讲述这两个影响 LAN 性能的重要概念。
#### 冲突域
设备间共享同一`网段`（稍后介绍）称为冲突域。因为该网段内两个以上设备同时尝试通讯时，可能发生冲突。<br>
使用工作在数据链路层的交换机可将各个网段的冲突域隔离，并减少竞争带宽的设备数量。<br>
交换机的每一个端口就是一个新的网段，因为插入端口的设备之间无需竞争。<br>
结果是每一个端口都代表一个新的冲突域。网段上的设备可以使用更多带宽，冲突域内的冲突不会影响到其他网段，这也成为微网段。<br>

如下图所示，每一个交换机端口连接到一台主机，每一个交换机端口代表一个隔离的冲突域。<br>
![](https://raw.githubusercontent.com/XuemingNotCute/MarkdownPhotos/master/14.png)
####广播域
尽管交换机按照MAC地址过滤大多数帧，它们并不能过滤广播帧。<br>LAN上的交换机接收到广播包后，必须对所有端口泛洪。<br>互连的交换机集合形成了一个广播域。<br>**网络层设备如路由器，可隔离二层广播域。路由器可同时隔离冲突和广播域。**<br>
当设备发出二层广播包，帧中的目的MAC地址被设置为全二进制数，广播域中的所有设备都会接收到该帧。二层广播域也称为MAC广播域。<br>MAC广播域包含LAN上所有接收到广播帧的设备。<br>广播通信比较多时，可能会带来`广播风暴`。<br>特别是在包含不同速率的网段，高速网段产生的广播流量可能导致低速网段严重拥挤，乃至崩溃。

## 补充概念：

#### 子网和网段
如图所示:

![](https://raw.githubusercontent.com/XuemingNotCute/MarkdownPhotos/master/15.png)<br>
![](https://raw.githubusercontent.com/XuemingNotCute/MarkdownPhotos/master/16.png)<br>
![](https://raw.githubusercontent.com/XuemingNotCute/MarkdownPhotos/master/17.png)
#### 网关和路由器的区别

概念理解有错误。首先`网关`是一个大概念，不具体特指一类产品，只要连接两个不同的网络的设备都可以叫网关；<br>而`路由器`一般特指能够实现路由寻找和转发的特定类产品，路由器很显然能够实现网关的功能。<br>当然电信行业说的‘路由器’又和家用的‘路由器’两个概念，这个暂且不表。<br>回到题目中你说问的默认网关是什么，默认网关事实上不是一个产品而是一个网络层的概念，PC本身不具备路由寻址能力，所以PC要把所有的IP包发送到一个默认的中转地址上面进行转发，也就是默认网关。<br>这个网关可以在路由器上，可以在三层交换机上，可以在防火墙上，可以在服务器上，所以和物理的设备无关。

