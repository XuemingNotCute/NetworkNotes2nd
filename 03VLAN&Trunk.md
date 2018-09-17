

# （三）VLAN 与 Trunk


将大型广播域分段是提高网络性能的方法之一。<br>
路由器能够将广播包阻隔在一个接口上，但是，路由器的LAN接口数量有限，它的主要功能是在网络间传输数据，而不是对终端设备提供网络接入。<br>访问LAN的功能还是由接入层交换机来实现。<br>与三层交换机相类似，通过在二层交换机上创建VLAN来减少广播域。<br>现代交换机就是通过VLAN来构造的，因此在某种程度上，学习交换机就是学习VLAN。

-------------------
## 问题的产生:
广播域的概念：即广播帧传播覆盖的范围。<br>如下图所示，当网络上的所有设备在广播域产生大量的广播以及多播帧，就会与`数据流`竞争带宽。<br>这是由网络管理数据流组成，如：ARP，DHCP，STP等。<br>如下图所示，假设PC 1产生ARP，Windows登录，DHCP等请求：
![](https://raw.githubusercontent.com/XuemingNotCute/MarkdownPhotos/master/18.png)<br>
这些广播帧到达交换机1之后，遍历整个网络并到达所有节点直至路由器。<br>随着网络节点增加，开销的总数也在增长，直至影响交换机性能。<br>通过实施VLAN**断开广播域将数据流隔离开来**，能够解决这一问题。

## 什么是VLAN：
VLAN（virtual local area network）是**一组与位置无关的逻辑端口**。<br>VLAN就相当于一个独立的三层网络。<br>VLAN的成员无需局限于同一交换机的顺序或偶数端口。<br>下图显示了一个常规的部署`常见拓扑1`，左边这张图节点连接到交换机，交换机连接到路由器。<br>所有的节点都位于同一IP网络，因为他们都连接到路由器同一接口。
![](https://raw.githubusercontent.com/XuemingNotCute/MarkdownPhotos/master/19.png)<br>
图中没有显示的是，缺省情况下，所有节点实际上都是同一VLAN。<br>因此，这种拓扑接口**可看作**是基于同一VLAN的，如上面右图所示。<br>例如，Cisco设备默认VLAN是VLAN 1，也称为管理VLAN。默认配置下包含所有的端口，体现在源地址表（source address table，SAT）中。<br>该表用于交换机按照目的MAC地址将帧转发至合适的二层端口。<br>引入VLAN之后，源地址表按照VLAN将端口与MAC地址相对应起来，从而使得交换机能够做出更多高级转发决策。下图显示了show mac address table和show vlan命令的显示输出。<br>所有端口（FA0/1 – FA0/24）都在VLAN 1。

![](https://raw.githubusercontent.com/XuemingNotCute/MarkdownPhotos/master/20.jpg)<br>

另一种常用的拓扑结构`常见拓扑2`是两个交换机被一个路由器分离开来，如下图所示。<br>这种情况下，每台交换机各连接一组节点。<br>每个交换机上的各节点共享一个IP地址域，这里有两个网段：192.168.1.0和192.168.2.0。
![](https://raw.githubusercontent.com/XuemingNotCute/MarkdownPhotos/master/21.png)
注意到两台交换机的VLAN相同。<br>**非本地网络数据流必须经过路由器转发。**<br>路由器不会转发二层单播，多播以及广播帧。<br>这种拓扑逻辑在两个地方类似于多VLAN：同一VLAN下的节点共享一个通用地址域，非本地数据流（对应多VLAN情况不同VLAN的节点）需通过路由器转发。<br>在一台交换机上添加一个VLAN，去掉另一台交换机的话，结构如下所示：
![](https://raw.githubusercontent.com/XuemingNotCute/MarkdownPhotos/master/23.png)<br>


**每一个VLAN相当于一个独立的三层IP网络**，因此，192.168.1.0上的节点试图与192.168.2.0上的节点通信时，**不同VLAN通信必须通过路由器**，即使所有设备都连接到同一交换机。<br>二层单播，多播和广播数据只会在同一VLAN内转发及泛洪，因此VLAN 1产生的数据不会为VLAN 2节点所见。<br>`只有交换机能看得到VLAN`，节点和路由器都感觉不到VLAN的存在。<br>添加了路由决策之后，可以利用3层的功能来实现更多的安全设定，更多流量以及负载均衡。

------

## VLAN的作用：
- **安全性**：每一个分组的敏感数据需要与网络其他部分隔离开，减少保密信息遭到破坏的可能性。如下图所示，VLAN 10上的教职工主机完全与学生和访客数据隔离。(s:switch交换机)
![](https://raw.githubusercontent.com/XuemingNotCute/MarkdownPhotos/master/24.jpg)<br>

- 节约成本：无需昂贵的网络升级，并且带宽及上行链路利用率更加有效。

- 性能提高：将二层网络划分成多个逻辑工作组（广播域）减少网络间不必要的数据流并提升性能。

- **缩小广播域**：减少一个广播域上的设备数量。如上图所示：网络上有六台主机但有三个广播域：教职工，学生，访客。

- 提升IT管理效率：网络需求相似的用户共享同一VLAN，从而网络管理更为简单。当添加一个新的交换机，在指定端口VLAN时，所有策略和步骤已配置好。

- 简化项目和应用管理：VLAN将用户和网络设备汇集起来，以支持不同的业务或地理位置需求。

**每一个VLAN对应于一个IP网络，因此，部署VLAN的时候必须结合考虑网络地址层级的实现情况。**

## 交换机间VLAN：

多交换机的情况下，VLAN是怎么工作的呢？
下图所示的这种情况，两个交换机VLAN相同，都是默认VLAN 1，即两个交换机之间的联系同在VLAN 1之内。
路由器是所有节点的出口。
![](https://raw.githubusercontent.com/XuemingNotCute/MarkdownPhotos/master/25.jpg)<br>

这时单播，多播和广播数据自由传输，所有节点属于同一IP地址。这时节点之间的通信不会有问题，因为交换机的SAT显示它们在同一VLAN。



而下面这种连接方式就会有问题。由于VLAN在连接端口的主机之间创建了三层边界，它们将无法通信。

![](https://raw.githubusercontent.com/XuemingNotCute/MarkdownPhotos/master/26.png)<br>

仔细看上图，这里有很多问题。<br>第一，所有主机都在同一IP网，尽管连接到不同的VLAN。<br>第二，路由器在VLAN 1,因此与所有节点隔离。<br>最后，两台交换机通过不同的VLAN互连。每一点都会造成通信阻碍，合在一起，网络各元素之间会完全无法通信。<br>

交换机用满或同一管理单元物理上彼此分离的情形是很常见的。这种情况下，VLAN需要通过trunk延伸至相邻交换机。trunk能够连接交换机，在网络间传载VLAN信息。如下图所示：<br>
![](https://raw.githubusercontent.com/XuemingNotCute/MarkdownPhotos/master/27.png)<br>

对之前的拓扑的改进包括：

- PC 1和PC 2分配到192.168.1.0网段以及VLAN 2。
- PC 3和PC 4分配到192.168.2.0网段以及VLAN 3。
- 路由器接口连接到VLAN 2和VLAN 3。
- 交换机间通过trunk线互连。

注意到trunk端口出现在VLAN 1，他们没有用字母T来标识。trunk在任何VLAN都没有成员。现在VLAN跨越多交换机，同一VLAN下的节点可以物理上位于任何地方。

## 什么是Trunk
Trunk是在两个网络设备之间承载多于一种VLAN的端到端的连接，将VLAN延伸至整个网络。<br>没有VLAN Trunk，VLAN也不会非常有用。VLAN Trunk允许VLAN数据流在交换机间传输，所以设备在同一VLAN，但连接到不同交换机，能够不通过路由器来进行通信。

**一个VLAN trunk不属于某一特定VLAN**，而是交换机和路由器间多个VLAN的通道。<br>如下图所示，交换机S1和S2，以及S1和S3之间的链路，配置为传输从VLAN10,20,30以及90的数据流。该网络没有VLAN trunk就无法工作。
![](https://raw.githubusercontent.com/XuemingNotCute/MarkdownPhotos/master/28.PNG)<br>
当安装好trunk线之后，帧在trunk线传输是就可以使用trunk协议来修改以太网帧。<br>**这也意味着交换机端口有不止一种操作模式。**<br>缺省情况下，所有端口都称为接入端口。<br>当一个端口用于交换机间互连传输VLAN信息时，这种端口模式改变为trunk，节点也路由器通常不知道VLAN的存在并使用标准以太网帧或“untagged”帧。<br>trunk线能够使用“tagged”帧来标记VLAN或优先级。<br>
因此，在trunk端口，运行trunk协议来允许帧中包含trunk信息。如下图所示：<br>
![](https://raw.githubusercontent.com/XuemingNotCute/MarkdownPhotos/master/29.jpg)<br>

PC 1在经过路由表处理后向PC 2发送数据流。这两个节点在同一VLAN但不同交换机。步骤如下：

- 以太网帧离开PC 1到达Switch 1。

-  Switch 1的SAT表明目的地是trunk线的另一端。

- Switch 1使用trunk协议在以太网帧中添加VLAN id。

-  新帧离开Switch 1的trunk端口被Switch 2接收。

-    Switch 2读取trunk id并解析trunk协议。

-  源帧按照Switch 2的SAT转发至目的地（端口4）。

 

VLAN tag如下图所示，包含类型域，优先级域，CFI（Canonical Format Indicator）指示MAC数据域，VLAN ID。
![](https://raw.githubusercontent.com/XuemingNotCute/MarkdownPhotos/master/30.jpg)<br>
注：在帧中加入 Tag。
