# （番外一）计网的每一层都是用来解决什么问题的？--自底向上



 
- 一点微小的笔记
- 本文讲述了如何跟外行通俗易懂的解释计算机网络

-------------------

1. 网络的构成就像一个社会，它的每一次迭代，都是为了解决一个具体的问题。所以本文尝试用~~通俗易懂的语言~~（自己的话😊）解释计算计网络分层结构中每一层的由来，也就是每一层解决了什么问题。
2. 任何网络的目的都是 switching 和通过 switching 共享资源，是为了连接端到端。可以联想一下交通网络、社交网络、神经网络，都是如此。计算机网络也是网络的一种。所以计算机网络的目的也是通过把无数的网络设备（主要是hosts）连接在一起，达到通信和共享资源的目的。
3. 如果要你来设计一个计网，想想你会怎么设计呢？
- [ ] 怎么传

----------

4. **首先来想一下怎么 switching**。在计算机网络中，“交换”(switching)的含义就是转接——把一条电话线转接到另一条电话线，是他们连通起来。
从通信资源的分配角度来看，“交换”就是按照某种方式动态地分配传输线路的资源。
5. 世界上有两种网：1.分组交换（基于报文交换）；2.电路交换(电话)。电路交换是一种Connection-Oriented Communication面向连接的通信。建立连接->通信->断开连接。这样的话通信双方独占物理通路，即使通信线路空闲，也不能供其他用户使用，因而信道利用低。  但是有一个好处就是适合语音这种实时性要求高的服务，你也不想你说完后一句对面的前一句才传到吧。
6. 小时候曾经流行过的“拨号上网”，就是使用的电路交换。所以一上网就打不了座机。但是这样其实不太符合我们网上冲浪的需求，因为比如我现在打开了写博客的网站，然后在编辑器要写很久，暂时不需要再通信，没必要一直给我连接着，这样就是浪费了通信线路啊。
7. 所以排除了“电路交换”。再来看看分组交换。分组交换也有两种方式：1.虚电路(面向连接)；2.数据报，比如互联网。虚电路方式也是面向连接的。所以我们也不选它。大多数时候，互联网没有 real-time 的要求，使用面向连接的虚电路会浪费资源，所以没必要使用面向连接的虚电路。
8. 所以我们最终，选用了`数据报`的 switching 方式。这是一种无连接通信。Connectionless Communication就像写信，你只需填写好收信人的地址信息，然后将信投入邮局，就算完成了任务。此时，邮局会根据收信人的地址信息，将信件送达指定目的地。它不事先建立逻辑信道，而是根据实际情况选择最佳路径，在无连接的通信会话中，每个数据分组是一个在网络上传输的独立单元，称作`数据报`。 该方法中没有接收方发来的分组接收或未接收的应答，也没有流控制，所以分组可能不按次序到达，接收方如有质量需要，可能对数据报重新排序。
9. 顺便说下，Internet 就是互联网，因特网是它的直译名，而互联网是它事实上的标准译名（参考谢希仁《计算机网络》第七版前言部分）。

- [x] 怎么传

------
10.  好的，我们现在知道了互联网使用的是数据报。那我们来思考一下，如何传输 datagram 数据报。顺便说一句：数据报和数据包的区别就得先介绍PDU（Protocol Data Unit）即协议数据单元，PDU是指对等层次之间传递的数据单位。物理层的 PDU是数据位（bit），数据链路层的 PDU是数据帧（frame），网络层的PDU是数据包（packet），传输层的 PDU是数据段（segment），其他更高层次的PDU是数据（message）。
由此我们知道数据包是网络层的PDU，而数据报是TCP/IP协议定义的一个在因特网上传输的包。
11. 我们的 Internet，肯定是基于物理电路的，要把数据转化为物理信号进行传输。所以我们也需要一个将数据转化为物理信号的layer。为了解决这个问题，`物理层`诞生了。

- [ ] 怎么找

----

12.  有了转换信号的物理层，但是我们还得知道，信号发给谁呢？怎么去找呢？直观的猜想，是根据每台主机全球唯一的那个MAC地址，去寻址。所以根据MAC地址寻址的工作，就交给`数据链路层`来做啦。
13.  但是，但是，存在一个问题。MAC地址是扁平化的。所谓扁平化，也就是说，MAC地址的空间分布，毫无规律。如果你有100万台Hosts，要通过MAC地址来寻址，就太难了。因为无论你设计什么样的算法，数据量都太大了。所以，IP地址就用来解决MAC地址寻址难的问题。有了 IP 地址，`网络层`就诞生了。
14.  然鹅，一台主机不是只跟一台server进行通信的，我能不能既在爱奇艺上面看着《延禧攻略》，又一边~~假装~~在博客园上写博客呢？所以这就涉及到一个问题，如何并行通信（和多个servers同时通信）。Port端口号可以用来解决这个问题。
- [x] 怎么找

----
15. 另外，基于不同需求：有人想要连的快，不介意数据丢失，比如《延禧攻略》随便看看就行了。但是有人要求数据可靠，比如在 deadline 前电邮给老师的作业。于是基于这些不同的需求，就产生了 UDP(无连接的user datagram)和 TCP(面向连接的stream)。所以`传输层 Transport Layer`诞生啦。
16. 根据上面的描述，我们就可以知道传输层的报文里面，绝对有目标端口信息。另外传输层有两种协议：不太靠谱的 UDP 和 非常靠谱的 TCP。UDP就像渣男，虽然不太靠谱，但是相处起来很舒服，TCP 是很靠谱，但是对别人要求也很高，属于那种非常认真的人。扯远了...

------

17. 另外，不同的 applications 有不同的传输需求，比如请求网页、发送邮件...而且，还有 DHCP server。为了方便开发者，就对这些常用需求进行了封装。所以`应用层`就产生了。回忆一下，你耳熟能详的 FTP，就是应用层的协议，处理文件传输的 application。
18. 以上就是我们的《计算机网络|自下向顶方法》...

----
19. 另外什么是传说中的 `TCP\IP` 呢?
TCP/IP是个协议组，可分为三个层次：网络层、传输层和应用层。
~~物理层，数据链路层没有协议~~
在网络层有IP协议、ICMP协议（ping）、ARP协议（注意这里并不在数据链路层）、RARP协议和BOOTP协议。
在传输层中有TCP协议与UDP协议。
在应用层有FTP、HTTP(传输超文本)、TELNET、SMTP、**DNS**等协议。
HTTP本身就是一个协议，是从Web服务器传输超文本到本地浏览器的传送协议。

----

20. 再来思考一下到底怎么连的问题。如果有四台 hosts, 要互相能通信？怎么连？
- 每一台都和另三台连起来？那如果再来十台电脑，是不是要在电脑上再加十个接口？
- 那，把他们连接到一个小盒子上，让小盒子帮着通信？ 哎这个可以有啊，那如果我有一万台电脑，一个小盒子能够用？
- 嘿嘿，那让每一个小盒子连一百台，然后把一百台小盒子再连给一个小盒子。
- 我们可以用“电话线，宽带，和光纤”，把电脑接给小盒子，它们被称作“接入网” 而ISP就像小盒子，帮你在网络里做通信 而ISP的分层，无非就是，终端太多了，没办法不分层。
- 端系统通过ISP（因特网服务提供商）接入网络，每个是由ISP由多个交换机和多段通信链路组成的网络。ISP是分层级的，用户接入的是底层ISP，底层ISP的服务由高层ISP提供，通过分层可以降低网络管理的难度和复杂度。
端系统以及交换机都要运行一系列协议。TCP和IP是最重要的两个协议，IP协议定义了在端系统和路由器中发送和接受分组的格式；TCP协议为端系统提供可靠地数据传输。
因特网通常指的是全球性的公共互联网。

好了，现在你已经明白了网络的层次化（并没有）。

----
21.  IP 寻址和 IPV4 地址不够用问题
你肯定是知道， 为了在辣么多计算机里，找到目标，我们采用了，**有规律的IP地址**。而`路由器`，又叫分组交换机，就是帮我们在公网里，做IP寻址的.
嗯。路由器是用来IP寻址的。
最初，IP地址是IPv4 首先，IP地址是分成了五类（ABCDE）。
奈何不够用啊，于是，我们是使用了子网划分。然鹅，手动分配子网IP，会死人的！ 于是，有了DHCP这个局域网网络协议，用来动态分配子网IP。
但是呢还是不算很好用，于是，诞生了无分类编址（CIDR）。
奈何，还是不够用啊。为了勉强解决 IPV4 地址枯竭的问题。于是，NAT出现啦，于是专用网的IP不再占用公网IP。
以上一些都是用来试图解决 IPV4 地址不够用的问题的。

-------
> 一些关于局域网的问题

22. 上面提到 DHCP 是局域网网络协议。
	首先，什么是专用网呢？<br> 1.局域网，比如，公用一个路由器的宿舍啊，家啊 ；<br>2.部分广域网，比如军队、铁路、交通、电力等部门，拥有自己专用的通信网和计算机网。<br>然鹅，这些网络不对内部外的用户开放。这些网络覆盖的地理范围很广，因此，这些专用网都是广域网。
保密性质的广域网，通信要扯到VPN，VPN 是另一个问题了。

23. 来谈谈局域网内的通信： 
-	如果我们是一个大局域网，比如我们学校有一百台电脑， 首先，路由器没一百个接口让我插！ 其次，如果我不想和公网通信，那我就没必要用路由器！ 所以，链路交换机来了！
链路交换机是基于MAC寻址的，因为局域网没大到必须用IP寻址的地步啊 但更准确的说话，链路交换机采用了，跨越链路层和网络层边界的协议——ARP。毕竟，ARP要做一个IP到MAC的映射。
所以我们知道了，**局域网内使用链路交换机，基于MAC而非IP寻址。**。
-	 为啥ARP协议（其实是网络层的协议而不是数据链路层的协议）要做IP到MAC的映射呢？因为，在应用层和运输层里，目的地址都写得是IP, 不把IP转化为MAC，怎么去寻址呢？
-	你问我，局域网为啥不用路由器，为啥要用`链路交换机`？因为交换机功能少，接口多，比路由器划算啊。
-	 那，局域网和公网怎么通信呢? 答案是，使用`NAT`。
-NAT是“Network Address Translation网络地址转换”，它是一个IETF(Internet Engineering Task Force, Internet工程任务组)标准，允许一个整体机构以一个公用IP（Internet Protocol）地址出现在Internet上。顾名思义，它是一种把内部私有网络地址（IP地址）翻译成合法网络IP地址的技术。因此我们可以认为，NAT在一定程度上，能够有效的解决公网地址不足的问题。
分组交换机，也就是路由器，用自己的公网IP，帮你们局域网里的人们，给公网发信息 然后把接受到的信息，再转发给，那个找他帮忙的人，这就是~~灌篮（误）~~NAT技术。

-------------------------------------------

24. 但是NAT又有一些缺点，比如隐藏了私网地址使得网络排错更加困难。IPV4资源池枯竭的问题总是要解决的，所以就有了IPv6。 那么就牵扯到了IPv4和IPv6间的通信（双栈||隧道）。
还有，IP地址没有语义，用户难以记忆 http://xxx.xxx.xxx.xxx。 于是域名应运而生， 顺便带出来了 DNS 服务器。



------------------------------
后面还有一些需要继续去看的：
比如：
- VPN
- 各种 proxy
- 路由算法
- 校验方式
- ......

继续刷......



