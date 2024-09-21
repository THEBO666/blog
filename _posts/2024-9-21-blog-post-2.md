---
layout: post
title: 数据链路层
categories: [计算机网络]
description: 数据链路层笔记
keywords: 计算机网络，数据链路层
---

# 数据链路层

![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20131130.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20131130.png)

# 1. 数据链路层概述

## 1.1 关键词释义

- 结点：将在本层协议运行的任何设备均称为**结点**，其包括主机、路由器、交换机等。
- 链路：就是从一个结点到相邻结点的一段物理线路，而中间没有任何其他的交换结点。
- 数据链路：是指把实现通信协议的硬件和软件加到链路上，就构成了数据链路
- 帧：点对点数据链路层的协议数据单元

![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20111649.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20111649.png)

- 在此可以将模型简化为3层，点对点信道的数据链路层在进行通信时的主要步骤如下：
    
    （1）节点 A 的数据链路层把网络层交下来的 IP 数据报添加首部和尾部封装成帧。
    （2）节点 A 把封装好的帧发送给节点 B 的数据链路层。
    （3）若节点 B 的数据链路层收到的帧无差错，则从收到的帧中提取出 IP 数据报交给上
    面的网络层；否则丢弃这个帧。
    

## 1.2 数据链路层基本问题

数据链路层在物理层提供的服务基础上**向网络层提供服务**，其最基本的服务是将源自网络层来的数据**可靠地**传输到相邻结点的目标机网络层。其主要作用是**加强物理层传输原始比特流的功能**，将物理层提供的可能出错的物理连接改造为逻辑上无差错的数据链路，使之对网络层表现为一条无差错的路。

数据链路层协议有许多种，但有三个基本问题则是共同的。这三个基本问题是：**封装成帧**、**透明传输**和**差错检测**。下面分别讨论这三个基本问题。

### 1.2.1 封装成帧

![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20132511.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20132511.png)

**封装成帧(framing)就是在一段数据的前后分别添加首部和尾部，这样就构成了一个帧**。接收端在收到物理层上交的比特流后，就能根据首部和尾部的标记，从收到的比特流中识别帧的开始和结束。下图表示用帧首部和帧尾部封装成帧的一般概念。

![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20124501.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20124501.png)

我们知道，分组交换的一个重要概念就是：所有在互联网上传送的数据都以分组(即 IP 数据报)为传送单位。**网络层的 IP 数据报传送到数据链路层就成为帧的数据部分。在帧的数据部分的前面和后面分别添加上首部和尾部，构成了一个完整的帧。**这样的帧就是数据链路层的数据传送单元。一个帧的帧长等于帧的数据部分长度加上帧首部和帧尾部的长度。首部和尾部的一个重要作用就是进行帧定界 (即确定帧的界限)。

此外，首部和尾部还包括许多必要的控制信息。在发送帧时，是从帧首部开始发送的。各种数据链路层协议都对帧首部和帧尾部的格式有明确的规定。显然，为了提高帧的传输效率，应当使帧的数据部分长度尽可能地大于首部和尾部的长度。但是，每一种链路层协议都规定了所能传送的帧的数据部分长度上限——最大传送单元 MTU(Maximum Transfer Unit)。上图给出了帧的首部和尾部的位置，以及帧的数据部分与 MTU 的关系。

如何实现“封装成帧”呢？

![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20130831.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20130831.png)

### 1.2.2 透明传输

透明传输是指不管所传数据是什么样的比特组合，都应当能够在链路上传送。因此，链路层就“看不见”有什么妨碍数据传输的东西。
当所传数据中的比特组合恰巧与某一个控制信息完全一样时， 就必须采取适当的措施，使收方不会将这样的数据误认为是某种控制信息。这样才能保证数据链路层的传输是透明的。下图为1种情况下引起的错误。

![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20133823.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20133823.png)

数据部分如果出现了“帧定界”字符，在其前面插入“转义 字符（ESC 0x1B）。如下图所示。

![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20134101.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20134101.png)

### 1.2.3 差错检测

![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/3c681eed9f72b8d2954fac2e4d2e5402.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/3c681eed9f72b8d2954fac2e4d2e5402.png)

# 2. 封装成帧与透明传输

## 2.1 封装成帧

封装成帧是指数据链路层给上层交付的协议数据单元添加帧头和帧尾使之成为帧。

- 帧头和帧尾中包含有重要的控制信息
    
    ![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20135210.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20135210.png)
    
- 帧头和帧尾的作用之一就是**帧定界**
    
    PPP帧中帧头尾均有**1字节的标志位**用于界定帧
    
    ![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20140059.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20140059.png)
    

以太网版本2的MAC帧在交付给物理层时会在帧前附加**8字节前导码**

![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20140216.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20140216.png)

- 前7个字节为**前同步码**，作用使接收方的时钟同步
- 最后1字节为**帧开始界定符**，表明后面紧跟着的便是MAC帧
- 此外，以太网V2还规定了**帧间间隔时间**为96比特发送时间（因此不需要帧结束界定符）
    
    ![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/a3d0c71e46db5e9c05a614461a52347b.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/a3d0c71e46db5e9c05a614461a52347b.png)
    
    帧间间隔还有其他作用，后续会进行说明。
    

## 2.2 透明传输

透明传输指**数据链路层对上层交付的传输数据没有任何限制**，就好像数据链路层不存在一样。

- 当上层数据中包含**帧标志符**时，会造成接收方的**误判**
    
    ![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/541f0e429bd530c86974493139d6804e.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/541f0e429bd530c86974493139d6804e.png)
    

- 可以在数据中与标识符相同的字段前加上**转义字符**，接收方遇到转义字符便知道其后跟着的数据而非真正的标识符，并且接收方需将该转义字符剔除掉
    
    ![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/534fac14a23ac8708eaa85f0608c03a8.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/534fac14a23ac8708eaa85f0608c03a8.png)
    

- 但如果数据中还含有和**转义字符相同的字段**，这时便又会引起接收方的**误判**（将本为数据的转义字符剔除），此时将数据中的转义字符前再加上一个转义字符，这样一来接收方每次遇到转义字符时便知道其后紧跟的是数据并剔除该一个转义字符即可
    
    ![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/daf12478ea6d11218d5a57f27bfcbe11.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/daf12478ea6d11218d5a57f27bfcbe11.png)
    

综上所述，对于**面向字节的物理链路使用字节填充（或称字符填充）的方法实现透明传输**（如上图所示）；**面向比特的物理链路使用比特填充的方法实现透明传输**（如下图所示）**。**

![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20141319.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20141319.png)

# 3. 差错检测

实际的通信链路都不是理想的，比特在传输的过程中可能会产生差错：1可能变为0，而0也可能变为1，这称为**比特差错**。在一段时间内，**传输错误的比特占所传输比特总数的比率称为误码率BER**（Bit Error Rate）。使用差错检测码来检测数据在传输过程中是否产生了比特差错，是数据链路层所要解决的重要问题之一。

![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/9fd8e643ec69c2f88c2e832a4e1a7163.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/9fd8e643ec69c2f88c2e832a4e1a7163.png)

以上两种帧格式都包含帧检验序列FCS字段。

首先介绍比较简单的奇偶校验。

## 3.1 奇偶校验

- 在待发送的数据后面**添加1位奇偶校验位**，使整个数据（包括所添加的校验位在内）中**“1”的个数**为奇数（奇校验）或偶数（偶校验）
- 如果有**奇数个位发生误码**，则奇偶性发生变化，**可以检查出误码**
- 如果有**偶数个位发生误码**，则奇偶性不发生变化，**不能检查出误码（漏检）**

![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/4adbc51fd27b4fab49bb9234f9c6e6e9.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/4adbc51fd27b4fab49bb9234f9c6e6e9.png)

由于这两种校验方法的漏检率较高，故通常不会采用，下面介绍一种有效的检测方法。

## 3.2 循环冗余校验CRC（Cyclic Redundancy Check）

其方法如下：

- 收发双方约定好一个生成多项式G(X);
- 发送方基于待发送的数据和生成多项式计算出差错检测码（冗余码），将其添加到待传输数据的后面一起传输;
- 接收方通过生成多项式来计算收到的数据是否产生了误码

![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20143158.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20143158.png)

下面举一个例子来说明：

![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/38dce97219bc9aa63e611efa4271c41a.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/38dce97219bc9aa63e611efa4271c41a.png)

![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/5795cc52a618331e77f9248c479f11f9.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/5795cc52a618331e77f9248c479f11f9.png)

![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/ad5584a032a1039584f6e95f6fdacd64.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/ad5584a032a1039584f6e95f6fdacd64.png)

- 检错码只能检测出帧在传输过程中出现了差错，但并不能定位错误，因此无法纠正错误
- 想要纠正传输中的差错，可以使用冗余信息更多的纠错码进行前向纠错。但纠错码的开销比较大，在计算机网络中较少使用
- 循环冗余校验CRC有很好的检错能力（漏检率非常低），虽然计算比较复杂，但是非常易于用硬件实现，因此被广泛应用于数据链路层
- 在计算机网络中通常采用检错重传方式来纠正传输中的差错，或者仅仅是丢弃检测到差错的帧，这取决于数据链路层向其上层提供的是可靠传输服务还是不可靠传输服务

# 4.可靠传输

## 4.1 可靠传输的基本概念

- 数据链路层向上层提供的服务类型
    - 不可靠传输服务：仅仅丢弃有误码的帧，其他什么也不做
    - 可靠传输服务：想办法实现发送端发送什么，接收端就收到什么
- 一般情况下，**有线链路**的误码率较低，为了减小开销，并不要求数据链路层向上提供可靠传输服务。即使出现了误码，可靠传输的问题由其上层处理
- **无线链路**易受干扰，误码率比较高，因此要求数据链路层必须向上层提供可靠传输服务
- 比特差错只是传输差错中的一种。从整个计算机网络体系结构来看，传输差错还包括**分组丢失**、**分组失序**以及**分组重复**（这些传输差错一般出现在数据链路层的上层）
- 可靠传输服务并不仅局限于数据链路层，其他各层均可选择实现可靠传输
    
    ![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/edb5e17b0080aa89f3ae18ca456d0702.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/edb5e17b0080aa89f3ae18ca456d0702.png)
    

## 4.2 **停止等待协议SW（Stop-and-Wait）**

![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20150845.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20150845.png)

下面讲述的三种可靠传输协议，不仅局限于数据链路层，还可以应用到计算机网络体系结构的各层协议中。其具体方法如下：

1. 发送方向接收方**发送数据**
2. 接收方通过**差错检测**若无误码则接收并向发送方发送**确认分组（ACK）**
3. 发送方接收到确认分组后便可**删除上条数据的缓存**并发送下一组数据
4. 若出现了误码，接收方丢弃该数据并向发送方发送**否认分组（NAK）**
5. 发送方收到否认分组后**重新传送**上一条数据

![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20151718.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20151718.png)

- 当发送的数据中途丢失时，此时接收方未接收到数据便无法响应发送确认或否认分组，而发送方也一直等待接收方的反馈分组，此时陷入一种类似“死锁”的状态。

为解决该问题，可以在发送方发送完一个数据分组时，启动一个**超时计时器**。若到了计时器所设置的**重传时间**而发送方仍收不到接收方的任何ACK或NAK，则重传原来的数据分组，这就叫**超时重传**一般可将重传时间设置为略大于“从发送方到接收方的平均往返时间”，原理如下图所示。

![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20152041.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20152041.png)

- 若接收方发送的确认分组中途丢失，这时超过重传时间后发送方又将上条数据重发，此时接收方便接收到了两条同样的数据，应当丢弃第二次重发的数据。

为避免分组重复这种传输错误，必须给每个分组带上序号。接收方通过编号便可判断是否是重复的数据，对于重复的数据丢弃即可对于停止-等待协议，由于每发送一个数据就停止等待，只要保证每发送一个新的数据分组，其发送序号与上次发送的数据分组的序号不同就可以了，因此用一个比特来编号就够了。

![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20152301.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20152301.png)

- 若接收方发送的确认分组迟到，并且在迟到期间发送方重发了上条数据A，确认分组到达后又紧接着发送了下一条数据B，接收方收到重复的数据将其丢弃并发送确认分组，这时发送方接收到确认分组后会误以为是数据B的确认信号则会立即发送下条数据。

这里应当忽略重复的确认分组，而是等到真正的数据B确认分组传达后再发送下条数据，因此同样的**还需给确认分组编号**，遇到重复的确认分组将其忽略对于数据链路层的点对点信道，往返时间比较固定，不会出现确认迟到的情况，因此若只是在数据链路层实现停止-等待协议，可以不用给确认分组编号。

![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20152501.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20152501.png)

- 停止-等待协议的信道利用率：
    
    ![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/7b186ad77766b112e016db0f2079a3b3.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/7b186ad77766b112e016db0f2079a3b3.png)
    
    ![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/c3de34ba9e619124e132c157c589a87c.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/c3de34ba9e619124e132c157c589a87c.png)
    
    - 当往返时延RTT远大于数据帧发送时延时（例如使用卫星链路），信道利用率非常低
    - 若出现重传，则对于传送有效的数据信息来说，信道利用率更低
    - 为克服停止-等待协议信道利用率很低的缺点，便产生另外两种协议（后退N帧协议GBN 和 选择重传协议SR）

## 4.3 **回退N帧协议GBN（Go-Back-N）**

其具体方法如下：

1.  采用n个比特给分组遍序号，即序号0 ~n-1，本例取为3
2. 发送窗口的尺寸范围为：（本例取为5）
    
    $$
    1\leq\mathrm{W}_{\mathrm{T}}\leq2^n-1
    $$
    
3. 接收窗口的尺寸范围为：
    
    $$
    \mathrm{W}_{\mathrm{T}}=1
    $$
    

下面举例说明：

![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20161304.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20161304.png)

1. 发送方先连续发送5个数据帧
2. 接收方逐个接收到数据帧，并返回每个数据帧的确认分组
3. 发送方每接收到一个确认分组便移动一位发送窗口，再执行步骤1

![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/a475cac56275b98cb686bfe7465600ea.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/a475cac56275b98cb686bfe7465600ea.png)

- 累计确认
    
    接收方不一定要对收到的数据分组逐个发送确认，而是可以在收到几个数据分组后（由具体实现决定），对按序到达的最后一个数据分组发送确认。${ACK}_{n}$表示序号为$n$及以前的所有数据分组都已正确接收。
    
    ![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/4f201a8fcee7c388ccf28b645f5b9c67.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/4f201a8fcee7c388ccf28b645f5b9c67.png)
    
    这里接收方产生了两个确认分组${ACK}_{1}$和${ACK}_{4}$，若途中${ACK}_{1}$丢失，发送方收到${ACK}_{4}$后也可知道序号4及以前的数据分组均已正确接收。
    
    - 优点：
        - 即使确认分组丢失，发送方也可能不必重传
        - 减少接收方开销、减少对网络资源占用
    - 缺点：
        - 不能向发送方及时反映接收方已经确认接收的数据分组信息
- 有差错情况
    
    ![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/cb05d36ce7b46dc9944f4736f2822181.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/cb05d36ce7b46dc9944f4736f2822181.png)
    
    此时5号数据出现误码被丢弃，而其余的数据分组与接收窗口的序号不匹配，也会被丢弃。接着接收方会对之前按序接收的最后一个分组进行确认，每丢弃一个数据分组就发送一个确认分组（${ACK}_{4}$）。
    
    发送方收到重复的确认，便知道之前所发送的数据分组出现了差错，于是可以不等超时计时器超时就立刻重传（至于收到几个重复确认就立刻重传，由具体实现决定）。
    
    > 本例中，尽管序号为6、7、0、1的数据分组正确到达接收方，但由于5号数据分组误码不被接受，它们也受到牵连而不被接受，发送方还需重传这些数据分组，这便是所谓的Go-Back-N。
    > 
    > 
    > 可见，当通信线路质量不好时，回退N帧协议的信道利用率并不比停止-等待协议高。
    > 
    - 若${W}_{T}$超过取值范围
        
        ![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/7bcb6cf29c43f3d592c10a66a875fe05.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/7bcb6cf29c43f3d592c10a66a875fe05.png)
        
        这里一次性发送8个数据分组，第一遍的确认分组丢失，于是发送方超时重传这8个分组，此时重复分组的序号和接收窗口的序号是匹配的接收方无法分辨新旧数据。
        
- 总结
    
    ![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20164002.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20164002.png)
    

## 4.4 **选择重传协议SR（Selective Request）**

- 回退N帧协议的接收窗口尺寸${W}_{R}$**只能等于1**，因此接收方只能按序接收正确到达的数据分组
- 一个数据分组的误码就会导致其后续多个数据分组不能被接收方按序接收而丢弃（尽管它们无乱序和误码）。这必然会造成发送方对这些数据分组的超时重传，显然这时对通信资源的极大浪费
- 为了进一步提高性能，可设法只重传出现误码的数据分组。因此，接收窗口的尺寸${W}_{R}$**不应再等于1（而应大于1），以便接收方先收下失序到达但无误码并且序号落在接收窗口内的那些数据分组**，等到所缺分组收齐后再一并送交上层。这就是选择重传协议

> **选择重传协议**为了使发送方仅重传出现差错的分组，接收方**不能再采用累积确认**，而需要对每个正确接收到的数据分组进行**逐一确认**
> 

下面举例说明：

1. 采用3个比特给分组编序号，即序号0 - 7
2. 发送窗口的尺寸${W}_{T}$为4
3. 接收窗口的尺寸${W}_{R}$为4
    
    ![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/4344ac7c90d33b85eed3e083eabbec64.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/4344ac7c90d33b85eed3e083eabbec64.png)
    
    1. 发送方依次发送0 - 3序号四个数据分组
    2. 其中2号数据分组出现误码，接收方将其丢弃，接收窗口只有按序接收时才可滑动，因此0、1序号数据使窗口向右滑动2位，由于2号数据分组的缺失，窗口需在此停驻
    3. 数据使窗口向右滑动2位，由于2号数据分组的缺失，窗口需在此停驻
    
    ![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20171158.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20171158.png)
    
    1. 发送方接着发送4、5号数据分组，接收方正确接收后返回响应的确认分组
    2. 此时2号分组的重传计时器超时重传，接收方正确接收，由于接收窗口中的数据分组均已按序接收，于是窗口可向右滑动4位（同样的发送方的发送窗口中的确认分组也均按序接收到后也会向右滑动4位）
    
    ![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20171454.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20171454.png)
    

下面来看另一种情况：

当${W}_{T}={W}_{R}=5$时，会发生什么：

![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20172215.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20172215.png)

![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20172339.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20172339.png)

![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20172404.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20172404.png)

故我们可以做出以下总结。

- 总结

![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20171647.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20171647.png)

# 5. 点对点协议（PPP）

点对点协议PPP（Point-to-Point Protocol）是目前使用最广泛的点对点数据链路层协议

## 5.1 PPP协议的特点

我们知道，互联网用户通常都要连接到某个 ISP 才能接入到互联网。PPP 协议就是用户
计算机和 ISP 进行通信时所使用的数据链路层协议。

![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20191347.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20191347.png)

- PPP协议为在点对点链路传输各种协议数据报提供了一个标准方法，主要由以下三部分构成
    - 对各种协议数据报的封装方法（封装成帧）
    - 链路控制协议LCP，用于建立、配置以及测试数据链路的连接
    - 一套网络控制协议NCPs，其中的每一个协议支持不同的网络层协议
    
    ![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20191613.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20191613.png)
    

## 5.2 PPP协议的帧格式

### 5.2.1 各字段含义

其帧格式如下图所示：

![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20191849.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20191849.png)

### 5.2.2 透明传输

- 面向字节的异步链路采用插入转义字符的字节填充法
    
    ![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20192304.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20192304.png)
    
- 面向比特的同步链路采用插入比特0的比特填充法
    
    ![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20192533.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20192533.png)
    

### 5.2.3 差错检测

![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20192804.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20192804.png)

接收方每收到一个PPP帧，就进行CRC检验。若CRC检验正确，就收下该帧；反之就丢弃该帧。**使用PPP的数据链路层向上提供的是不可靠传输服务。**

## 5.3 PPP协议的工作状态

![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20193017.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20193017.png)

# 6. 媒体访问控制与协议

## 6.1 媒体接入控制的基本概念

![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20194440.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20194440.png)

如上图所示，多个主机连接一条总线，如果恰好有两个或多个结点在同一时刻发送数据，那么信号将要在共享媒体上发生碰撞，这样数据便会发送失败，如下图所示。

![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20194749.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20194749.png)

因此，共享信道要考虑的一个问题就是如何协调多个发送和接收站点对一个共享传输媒体的占用，即媒体接入控制MAC（Medium Access Control）

![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20194914.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20194914.png)

> 随着技术的发展，交换技术的成熟和成本的降低，具有更高性能的使用点对点链路和链路层交换机的交换式局域网在有线领域已经完全取代了共享式局域网，但由于无线信道的广播天性，无线局域网仍然使用的是共享媒体技术。
> 

## 6.2 **静态划分信道**

- 复用（Multiplexing）是通信技术中的一个重要概念。复用就是通过一条物理线路同时传输多路用户的信号
- 当网络中的传输媒体的传输容量大于多条单一信道传输的总通信量时，可以利用复用技术在一条物理线路上建立多条通信信道来充分利用传输媒体的带宽

![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/a662694f62d798ded08f1c75da421469.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/a662694f62d798ded08f1c75da421469.png)

### 6.2.1 频分复用FDM

![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20201921.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20201921.png)

### 6.2.2 时分复用TDM

![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20201959.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20201959.png)

### 6.2.3 波分复用WDM

![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20202055.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20202055.png)

### 6.2.4 码分复用CDM

- 码分复用CDM是另一种共享信道的方法。实际上，由于该技术主要用于多址接入，人们更常用的名词是码分多址CDMA（Code Division Multiple Access）
- 同理，频分复用FDM和时分复用TDM同样可用于多址接入，相应的名词时频分多址FDMA（Frequency Division Multiple Access）和时分多址TDMA（Time Division Multiple Access）
- 复用和多址概念可简单理解如下：
    - 复用是将单一媒体的频带资源划分成很多子信道，这些子信道之间相互独立，互不干扰。从媒体的整体频带资源上看，每个子信道只占用该媒体频带资源的一部分
    - 多址（更确切地应该称为多点接入）处理的是动态分配信道给用户。这在用户仅仅暂时性地占用信道的应用中是必须的，而所有的移动通信系统基本上都属于这种情况。相反，在信道永久性地分配给用户的应用中，多址是不需要的（对于无线广播或电视广播站就是这样）
    - 某种程度上，FDMA、TDMA、CDMA可以分别看成是FDM、TDM、CDM的应用
- 与FDM和TDM不同，CDM的每个用户可以**在同样的时间使用同样的频带进行通信**
- 由于各用户使用经过特殊挑选的不同码型，因此各用户之间不会造成干扰
- CDM最初是用于军事通信的，因为这种系统所发送的信号有很强的抗干扰能力，其频谱类似于白噪声，不易被敌人发现
- 随着技术的进步，CDMA设备的价格和体积都大幅度下降，因而现在已广泛用于民用的移动通信中
- 在CDMA中，每一个比特时间再划分为m个短的间隔，称为码片（Chip）。通常m的值是64或128。为了简单期间，后续的举例中，假设m为8
- 使用CDMA的每个站被指派一个唯一的m bit码片序列（Chip Sequence）
    - 一个站若要**发送比特1**，则**发送它自己的m bit码片序列**
    - 一个站若要**发送比特0**，则**发送它自己的m bit码片序列的二进制反码**
    - 例：
        
        ![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20215148.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20215148.png)
        
- 码片序列挑选原则如下：
    - 分配给每个站的**码片序列必须各不相同**，实际常采用伪随机码序列
    - 分配给每个站的**码片序列必须相互正交**（规格化内积为0）
        
        > 令向量S表示站S的码片序列，令向量T表示其他任何站的码片序列。两个不同站的码片序列正交，就是向量S和T的规格化内积为0。
        > 
        > 
        > $$
        > S\cdot T = \frac{1}{m} \sum_{i=1}^{m}S_{i} T_{i} = 0\\\\S\cdot S = \frac{1}{m} \sum_{i=1}^{m}S_{i}S_{i} = \frac{1}{m} \sum_{i=1}^{m}S_{i}^{2} = \frac{1}{m} \sum_{i=1}^{m}\left(\pm l\right)^{2} = 1               
        > $$
        > 
        > $$
        > \begin{aligned}S\cdotp\bar{T}&\equiv 0\\\\S\cdotp\bar{S}&\equiv -1\end{aligned}
        > $$
        > 
- 应用举例：
    
    ![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20220956.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20220956.png)
    

## 6.3 **CSMA/CD协议**

动态接入控制在总线局域网使用协议为CSMA/CD，在无线局域网是CSMA/CA。

![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20223538.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20223538.png)

CSMA/CD（Carrier Sense Multiple Access with Collision Detection，载波侦听多路访问/冲突检测）

### 6.3.1 协议中名词解释

- **多址接入MA**
    
    多个站连接在一条总线上，竞争使用总线
    
    ![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20225248.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20225248.png)
    
- **载波监听CS**
    
    每个站在发送帧之前先要检测一下总线上是否有其他站点在发送帧（先听后说）
    
    - 若检测到总线空闲96比特时间，则发送该帧
    - 若检测到总线忙，则继续检测并等待总线转为空闲96比特时间，然后发送该帧
        
        > **96比特时间**是指发送96bit所耗费的时间，也称为帧间最小间隔。其作用是使接收方可以检测出一个帧的结束，同时也使得所有其他站点都能有机会平等竞争信道并发送帧
        > 
    
    ![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20225016.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20225016.png)
    
- **碰撞检测CD**
    
    每个正在发送帧的站边发送边检测碰撞（边听边说）
    
    - 一旦发现总线上出现碰撞，则立即停止发送，退避一段随机时间后再次发送（“一旦冲突，立即停说，等待时机，重新再说”）
    
    ![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20225217.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20225217.png)
    
    > 以太网还采取了一种叫做**强化碰撞**的措施。这就是当发送帧的站点一旦检测到碰撞，除了立即停止发送帧外，还要再继续发送**32bit或48bit的人为干扰信号**（Jamming Signal），以便**有足够多的碰撞信号使所有站点都能检测出碰撞**
    > 

### 6.3.2 争用期（碰撞窗口）

![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20230533.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20230533.png)

- 主机最多经过$2\tau$（即$\sigma$→0）的时长就可以检测到本次发送是否遭受了碰撞
- 因此，以太网端到端往返传播时延$2\tau$称为争用期或碰撞窗口
- 经过争用期这段时间还没有检测到碰撞，才能肯定这次发送不会发生碰撞
- 每个主机在自己发送帧之后的一小段时间内，存在着遭遇碰撞的可能性。这小段时间是不确定的，它取决于另一个发送帧的主机到本主机的距离，但不会超过总线的端到端往返传播时延，即一个争用期时间
- 显然，在以太网中发送帧的主机越多，端到端往返传播时延越大，发送碰撞的概率就越大。因此，**共享式以太网不能连接太多的主机，使用的总线也不能太长**
    - 10Mb/s以太网把争用期定为512比特发送时间，即51.2微秒，因此其总线长度不能超过5120m，但考虑到其他一些因素，如信号衰减等，以太网规定总线长度不能超过2500m

### 6.3.3 帧长

- **最小帧长**
    
    ![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20231353.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20231353.png)
    
    上图为一个较短的帧从A发送。但是出现了差错，故以太网的帧长不宜太短。
    
    - **以太网规定最小帧长为64字节**，即512比特（512比特时间即为争用期）
        - 如果要发送的数据非常少，那么必须加入一些填充字节，是帧长不小于64字节
    - 以太网的**最小帧长确保了主机可在帧发送完成之前就检测到该帧的发送过程中是否遭遇了碰撞**
        - 如果在争用期（共发送64字节）没有检测到碰撞，那么后续发送的数据就一定不会发送碰撞
        - 如果在争用期内检测到碰撞，就立即中止发送，这时已经发送出去的数据一定小于64字节，因此**凡长度小于64字节的帧都是由于碰撞而异常中止的无效帧**
- **最大帧长**

![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20231843.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20231843.png)

上图为一个很长的帧从A发送。但是出现其一直占用总线，影响其他主机；故以太网的帧长不宜太长。

![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20231959.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20231959.png)

### 6.3.4 截断二进制指数退避算法

![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20232228.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20232228.png)

- 若连续多次发生碰撞，就表明可能有较多的主机参与竞争信道。但使用上述退避算法可**使重传需要推迟的平均时间随重传次数而增大**（这也称为**动态退避**），因而**减小发生碰撞的概率**，有利于整个系统的稳定
- **当重传达16次仍不能成功时**，表明同时打算发送帧的主机太多，以至于连续发生碰撞，则**丢弃该帧**，并向高层报告

### 6.3.5 信道利用率

![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20232545.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20232545.png)

- 考虑以下理想情况：
    - 各主机发送帧都不会碰撞
    - 总线一旦空闲就有某个主机立即发送帧
- 故发送一帧的占用时间为$T_0+\tau$，而帧本身发送时间是$T_0$
    - 极限信道利用率为
    
    $$
    S_{max} = \frac{T_{0}}{T_{0} + \tau} = \frac{1}{I + \frac{T}{T_{0}}} = \frac{1}{I +{a}}
    $$
    
    因此为提高信道利用率，应使以太网帧的长度尽量长些，端到端的距离受到限制。
    

### 6.3.6 帧发送与接收

- **帧发送流程**
    
    ![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20233338.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20233338.png)
    
- 帧接收流程
    
    ![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20233429.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-17%20233429.png)
    

> CSMA/CD协议曾经用于各种总线结构以太网和双绞线以太网的早期版本中。
> 
> 
> 现在的以太网基于交换机和全双工连接，不会有碰撞，因此没有必要使用CSMA/CD协议。
> 

## 6.4 **CSMA/CA协议**

载波监听多址接入/碰撞避免 CSMA/CA （Carrier Sense Multiple Access/Collision Avoidance）

- CSMA/CD协议已经成功应用于广播信道的有线局域网，那么广播信道的无线局域网是否也可以使用呢？
    
    在无线局域网中，可以使用多址接入CSMA，但是不能使用碰撞检测CD，原因如下：
    
    - 由于无线信道的传输条件特殊，其信号强度的动态范围非常大，无线网卡上接收到的信号强度往往会远远小于发送信号的强度（可能相差百万倍）。如果要在无线网卡上实现碰撞检测CD，对硬件的要求非常高
    - 即使能够在硬件上实现无线局域网的碰撞检测功能，但由于无线电波传播的特殊性（存在隐蔽站问题），进行碰撞检测的意义也不大（隐蔽站问题如下图所示）
        
        ![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-18%20094818.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-18%20094818.png)
        
- 为此802.11无线局域网使用CSMA/CA协议，在CSMA 基础上加了一个**碰撞避免的功能CA**，而不再实现碰撞检测功能
- 由于不可能避免所有的碰撞，并且**无线信道误码率较高**，802.11标准还使用了**数据链路层确认机制（停止-等待协议）**来保证数据被正确接收
    - 分布式协调功能DCF（Distributed Coordination Function）。在DCF方式下，没有中心控制站点，每个站点使用CSMA/CA协议通过争用信道来获取发送权，这是802.11定义的默认方式
    - 点协调功能PCF（Point Coordination Function）。PCF方式使用集中控制的接入算法（一般在接入点AP实现集中控制），是802.11定义的可选方式，在实际中较少使用

### 6.4.1 帧间间隔IFS

802.11标准规定，所有的**站点必须在持续检测到信道空闲一段指定时间后才能发送帧**，这段时间称为帧间间隔IFS（InterFrame Space）

- 帧间间隔的长短取决于该站点要发送的帧的类型：
    - 高优先级帧需要等待的时间较短，因此可优先获得发送权
    - 低优先级帧需要等待的时间较长。若某个站的低优先级帧还没来得及发送，而其他站的高优先级帧已发送到信道上，则信道变为忙态，因而低优先级帧就只能再推迟发送了。这样就减少了发生碰撞的机会
- 常用的两种帧间间隔如下：
    - 短帧间间隔SIFS（28μs），是最短的帧间间隔，用来分隔开属于一次对话的各帧。一个站点应当能够在这段时间内从发送方式切换到接收方式。使用SIFS的帧类型有ACK帧、CTS帧、由过长的MAC帧分片后的数据帧、以及所有回答AP探询的帧和在PCF方式中接入点AP发送出的任何帧
    - DCF帧间间隔DIFS（128μs），它比短帧间间隔SIFS要长得多，在DCF方式中用来发送数据帧和管理帧

### 6.4.2 工作原理

![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-18%20100534.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-18%20100534.png)

观察上图，我们发现**源站在检测到信道空闲后还要再等待一段时间DIFS**，这是为什么呢？

- 考虑到可能有其他的站有高优先级的帧要发送。若有，就要让高优先级帧先发送

那**目的站为什么正确接收数据帧后还要等待一段时间SIFS才能发送ACK帧？**

- SIFS是最短的帧间间隔，用来分隔开属于一次对话的各帧。在这段时间内，一个站点应当能够从发送方式切换到接收方式

### 6.4.3 退避算法

![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-18%20100858.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-18%20100858.png)

- 当站点检测到信道是空闲的，并且所发送的数据帧不是成功发送完上一个数据帧之后立即连续发送的数据帧，则不使用退避算法
- 以下情况必须使用退避算法：
    - 在发送数据帧之前检测到信道处于忙状态时
    - 在每一次重传一个数据帧时
    - 在每一次成功发送后要连续发送下一个帧时（这时为了避免一个站点长时间占用信道）
- 在执行退避算法时，站点为退避计时器设置一个随机的退避时间：
    - 当退避计时器的时间减小到零时，就开始发送数据
    - 当退避计时器的时间还未减小到零时而信道又转变为忙状态，这时就冻结退避计时器的数值，重新等待信道变为空闲，再经过时间DIFS后，继续启动退避计时器
- 在进行第$i$次退避时，退避时间在时隙编号{$0,1,...,2^{2+i}−1$}中随机选择一个，然后乘以基本退避时间（也就是一个时隙的长度）就可以得到随机的退避时间。这样做是为了使不同站点选择相同退避时间的概率减少。当时隙编号达到255时（对应于第6次退避）就不再增加了。下图为一个退避算法执行的例子。

![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-18%20163152.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-18%20163152.png)

### 6.4.4 信道预约和虚拟载波监听

![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/bbe5dc504faece6e31fe07d0afb92170.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/bbe5dc504faece6e31fe07d0afb92170.png)

- 为了尽可能减少碰撞的概率和降低碰撞的影响，802.11标准允许要发送数据的站点对信道进行预约
    - 源站在发送数据帧之前先发送一个短的控制帧，称为**请求发送RTS**（Request To Send），它包括源地址、目的地址以及这次通信（包括相应的确认帧）所需的持续时间
    - 若目的站正确收到源站发来的RTS帧，且媒体空闲，就发送一个响应控制帧，称为**允许发送CTS**（Clear To Send），它也包括这次通信所需的持续时间（从RTS帧中将此持续时间复制到CTS帧中）
    - 源站收到CTS帧后，再等待一段时间SIFS后，就可发送其数据帧
    - 目的站正确收到了源站发来的数据帧，在等待时间SIFS后，就向源站发送确认帧ACK
- 除源站和目的站以外的**其他各站**，在**收到CTS帧（或数据帧）后就推迟接入到无线局域网中**。这样就保证了源站和目的站之间的通信不会受到其他站的干扰
    
    ![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-18%20164858.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-18%20164858.png)
    
- 如果RTS帧发生碰撞，源站就收不到CTS帧，需执行退避算法重传RTS帧
- 由于RTS帧和CTS帧很短，发送碰撞的概率、碰撞产生的开销及本身的开销都很小。而对于一般的数据帧，其发送时延往往大于传播时延（因为是局域网），碰撞的概率很大，且一旦发生碰撞而导致数据帧重发，则浪费的时间就很多，因此用很小的代价对信道进行预约往往是值得的。802.11标准规定了3种情况供用户选择：
    - 使用RTS帧和CTS帧
    - 不使用RTS帧和CTS帧
    - 只有当数据帧的长度超过某一数值时才使用RTS帧和CTS帧
- 除RTS帧和CTS帧会携带通信需要持续的时间，数据帧也能携带通信需要持续的时间，这称为802.11的**虚拟载波监听**机制
- 由于利用虚拟载波监听机制，**站点只要监听到RTS帧、CTS帧或数据帧中的任何一个，就能知道信道被占用的持续时间**，而不需要真正监听到信道上的信号，因此**虚拟载波监听机制能减少隐蔽带来的碰撞问题**

### 6.4.5 总结

![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-18%20165415.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-18%20165415.png)

# 7. **MAC地址、IP地址以及ARP协议**

![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-18%20181007.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-18%20181007.png)

## 7.1 MAC地址

- 当多个主机连接在同一个广播信道上，要想实现两个主机之间的通信，则每个主机都必须有一个唯一的标识，即一个数据链路层地址

![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-18%20190940.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-18%20190940.png)

- 在每个主机发送的**帧中必须携带标识发送主机和接收主机的地址**。由于这类地址是用于媒体接入控制MAC(Media Access Control)，因此这类地址被称为**MAC地址**
    - MAC地址一般被固化在网卡（网络适配器）的电可擦可编程只读存储器EEPROM中，因此MAC地址也被称为**硬件地址**
        
        ![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-18%20192051.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-18%20192051.png)
        
    - MAC地址有时也被称为**物理地址**。**请注意：这并不意味着MAC地址属于网络体系结构中的物理层！**
- 一般情况下，用户主机会包含两个网络适配器：有线局域网适配器（有线网卡）和无线局域网适配器（无线网卡）。每个网络适配器都有一个全球唯一的MAC地址。而交换机和路由器往往拥有更多的网络接口，所以会拥有更多的MAC地址。综上所述，**严格来说，MAC地址是对网络上各接口的唯一标识，而不是对网络上各设备的唯一标识**
- IEEE 802的局域网MAC地址格式如下：
    
    ![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-18%20200547.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-18%20200547.png)
    
    ![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-18%20205409.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-18%20205409.png)
    
    - MAC地址发送顺序为：
        
        ![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-18%20210210.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-18%20210210.png)
        
    - MAC地址有单播、广播、多播三种，对应的格式也不同
        - 单播：
            
            ![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-18%20205722.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-18%20205722.png)
            
        - 广播：
            
            ![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-18%20205813.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-18%20205813.png)
            
        - 多播：
            
            ![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-18%20205951.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-18%20205951.png)
            
            > 由于多播地址的第一字节的b0为1，故可通过判断**十六进制的低位数是否可被2整除**快速判断是否为多播地址
            > 

## 7.2 IP地址

需要注意的是，IP地址属于网络层的内容，具体内容将在下一章讲解。

- IP地址是因特网上的主机和路由器所使用的地址，用于标识两部分信息
    - 网络编号：标识因特网上数以百万计的网络
    - 主机编号：标识同一网络上不同主机（或路由器各接口）
    
    ![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/dcbdc0d5f9f2c1ec77454b21ce60ea47.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/dcbdc0d5f9f2c1ec77454b21ce60ea47.png)
    
- 显然，之前介绍的MAC地址不具备区分不同网络的功能
    - 如果只是一个单独的网络，不接入因特网，可以只是用MAC地址（这不是一般用户的应用方式）
    - 如果主机所在的网络需要接入因特网，则IP地址和MAC地址都需要使用
- 下图为网络体系结构中的IP地址、MAC地址
    
    ![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-19%20193103.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-19%20193103.png)
    
- 下面我们看看数据包转发过程中IP地址与MAC地址的变化情况
    
    ![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-19%20193210.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-19%20193210.png)
    
    转发过程中，有以下几个特点：
    
    - 数据包转发过程中**源IP地址和目的IP地址保持不变**
    - 数据包转发过程中**源MAC地址和目的MAC地址逐个链路（或逐个网络）改变**
    
    > 目前发送站知道应该将数据包转给具体哪个路由或主机，发送站知道其目的站的IP地址，但不知道其对应的MAC地址是什么（将在后续的ARP协议中解释）
    > 

## 7.3 ARP协议

之前提到如何通过IP地址找到MAC地址，下面将进行解释，以下图为例。

![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-19%20194243.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-19%20194243.png)

假如主机B打算给C发送数据包。目前B知道C的IP地址，但不知道MAC地址，因此B的数据链路层封装MAC帧时，无法填写目的MAC地址。

实际上，每台主机都会有一个ARP高速缓存表。表中记录有IP地址和MAC地址的对应关系，于是主机B在表中试图查找主机C的IP地址，如下图所示。

![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-19%20194505.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-19%20194505.png)

由于未找到，则主机B需要发送ARP请求报文（广播）获取主机C的MAC地址，（实际上的ARP报文并不是上述结构，在此只是为了简化）原理如下图所示：

![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-19%20194609.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-19%20194609.png)

主机C收到广播后，首先将B的IP地址与MAC地址记录到自己的ARP高速缓存表中，再给B发送ARP响应（单播），以告知自己的MAC地址

![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-19%20195006.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-19%20195006.png)

主机B收到主机C发送的ARP响应后，将对应信息添加在自己的ARP高速缓存中

![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-19%20195042.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-19%20195042.png)

> 其中每条记录的类型分为**动态**（dynamic）和**静态**（static）两种：
> 
> - 动态：自动获取，生命周期默认为2min
> - 静态：手工设置，不同的操作系统下的生命周期不同，例如系统重启后不存在或系统重启后依然有效

需要注意的是，ARP协议只能在一段链路或一个网络上使用，而不能跨网络使用，ARP协议的使用是逐段链路进行的

![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-19%20195207.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-19%20195207.png)

# 8. 集线器与交换机

## 8.1 集线器

- 在前面总是用总线型以太网来举例，如下图所示。
    
    ![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-19%20195538.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-19%20195538.png)
    
- 早期使用无源电缆和大量机械接头构成以太网，但其并不可靠，故被淘汰
    
    ![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-19%20201101.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-19%20201101.png)
    
- 后面改为使用双绞线和集线器HUB的星型以太网
    
    ![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/a9e2cc4fa3ed43500c371c4cd9e71a83.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/a9e2cc4fa3ed43500c371c4cd9e71a83.png)
    
    - **使用集线器的以太网在逻辑上仍是一个总线网**，各站共享总线资源，**使用的还是CSMA/CD协议**
    - **集线器只工作在物理层**，它的每个接口仅简单地转发比特，不进行碰撞检测（由各站的网卡检测）
    - **集线器一般都有少量的容错能力和网络管理功能**。例如，若网络中某个网卡出了故障，不停地发送帧。此时，集线器可以检测到这个问题，在内部断开与出故障网卡的连线，使整个以太网仍然能正常工作
- 使用集线器HUB在物理层扩展以太网，如下图所示，但是会产生更大的碰撞域
    
    ![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/5b3db2c02a02421e3f82904887d709ff.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/5b3db2c02a02421e3f82904887d709ff.png)
    

## 8.2 交换机

### 8.2.1 交换机介绍

在接下来的对比中，我们假设以下对比交换机中忽略ARP过程并且假设交换机的帧交换表已学习好了

![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-19%20201717.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-19%20201717.png)

- 下图为以太网交换机的工作示意图
    
    ![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-19%20201919.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-19%20201919.png)
    
    - 以太网交换机通常都有**多个接口**。每个接口都可以直接与一台主机或另一个以太网交换机相连。一般都工作在**全双工方式**
    - 以太网交换机具有并行性，能**同时连通多对接口**，使多对主机能同时通信，**无碰撞**（**不使用CSMA/CD协议**）
    - 以太网交换机一般都具有多种速率的接口，例如：10Mb/s、100Mb/s、1Gb/s、10Gb/s接口的多种组合
    - 以太网交换机**工作在数据链路层**（也包括物理层）,它收到帧后，在帧交换表中查找**帧的目的MAC地址所对应的接口号**，然后通过该接口转发帧
    - 以太网交换机是一种即插即用设备，其内部的**帧交换表**是通过**自学习算法**自动地逐渐建立起来的
    - 帧的两种转发方式：
        - **存储转发**
        - **直通交换**：采用基于硬件的交叉矩阵（交换时延非常小，但不检查帧是否有差错）

### 8.2.2 以太网集线器和交换机的对比

- 单播对比
    
    ![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-19%20202338.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-19%20202338.png)
    
- 广播对比
    
    ![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-19%20202429.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-19%20202429.png)
    
- 多台主机同时给另一台主机发送单播帧对比
    
    ![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-19%20202545.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-19%20202545.png)
    
    使用交换机不会发生碰撞，其使用缓存并逐个发送，未产生碰撞
    
- 扩大广播域对比
    
    ![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-19%20202708.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-19%20202708.png)
    
- 小结

![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-19%20202856.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-19%20202856.png)

### 8.2.3 **以太网交换机自学习和转发帧的流程**

通过前面的内容，我们知道：

- 以太网交换机工作在数据链路层(也包括物理层)
- 以太网交换机收到帧后，在帧交换表中查找帧的目的MAC地址所对应的接口号,然后通过该接口转发帧

以太网交换机是一种即插即用设备，则上电信动时其内部的帧交换表是空的。随着网络中各主机间的通信，以太网交换机通过自学习算法自动逐渐建立起响交换表，下面介绍其自学习过程（假设各主机无需ARP）

1. 主机A给主机B发送数据
    
    ![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-19%20210533.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-19%20210533.png)
    
    1. 交换机1记录A的MAC地址及对应的接口号
    2.  交换机1在帧交换表中未找到主机B的MAC地址信息，于是盲目转发
    3. 交换机2收到数据后进行与交换机1相同的步骤
2. 主机B给主机A发送数据
    
    ![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-19%20210824.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-19%20210824.png)
    
    1. 交换机1记录B的MAC地址及对应的接口号
    2. 交换机1在帧交换表中找到了A的MAC地址对应的接口号，于是只需通过该接口转发给主机A
3. 主机E给主机A发送数据
    
    ![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-19%20210958.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-19%20210958.png)
    
    1. 交换机2记录E的MAC地址及对应的接口号
    2. 交换机2在帧交换表中找到了A的MAC地址对应的接口号，于是只需通过该接口转发给交换机1
    3. 交换机1记录E的MAC地址及对应的接口号
    4. 交换机1在帧交换表中找到了A的MAC地址对应的接口号，于是只需通过该接口转发给主机A
4. 主机G给主机A发送数据（丢弃帧的情况）
    
    ![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-19%20211243.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-19%20211243.png)
    
    1. 主机A不借助交换机可直接收到主机G的数据
    2. 但数据依旧会传到交换机1中，交换机1记录G的MAC地址和接口号
    3. 交换机1找到了A的接口号，但发现与G的接口号是一致的，于是丢弃不再转发该帧

> 帧交换表中，每条记录都有自己的**有效时间**，到期自动删除。这是因为**MAC地址和交换机接口的对应关系并不是永久性的**
> 
> - 交换机的接口改接了另一台主机
> - 主机更换了网卡
- 以太网交换机自学习和转发帧的流程
    1. 收到帧后进行**登记**。登记的内容为**帧的源MAC地址**及进入交换机的**接口号**
    2. 根据**帧的目的MAC地址**和交换机的**帧交换表**对帧进行**转发**，有以下三种情况：
        - **明确转发**：交换机知道应当从哪个（或哪些）接口转发该帧（单播，多播，广播）
        - **盲目转发**：交换机不知道应当从哪个端口转发帧，只能将其通过除进入交换机的接口外的其他所有接口转发（也称为泛洪）
        - **明确丢弃**：交换机知道不应该转发该帧，将其丢弃

### 8.2.4 **以太网交换机的生成树协议STP**

- 通常情况下，添加**冗余链路**可以提高以太网的可靠性

![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-19%20212606.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-19%20212606.png)

![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-19%20212657.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-19%20212657.png)

- 但是，冗余链路也会带来负面效应——形成**网络环路**
    
    ![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-19%20212808.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-19%20212808.png)
    
    其会带来以下问题：
    
    - **广播风暴** 大量消耗网络资源，使得网络无法正常转发其他数据帧
    - **主机收到重复的广播帧** 大量消耗主机资源
        
        ![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-19%20213039.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-19%20213039.png)
        
    - **交换机的帧交换表震荡（漂移）**
        
        H1的信息便会在两个错误接口信息中不断交换
        
        ![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-19%20213114.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-19%20213114.png)
        
- 以太网交换机使用**生成树协议STP**（Spanning Tree Protocol），可以在增加冗余链路来提高网络可靠性的同时又**避免网络环路带来的各种问题**
    - 不论交换机之间采用怎样的物理连接，交换机都能够**自动计算并构建一个逻辑上没有环路的网络**，其逻辑拓扑结构必须是树型的（无逻辑环路）
    - 最终生成的树型逻辑拓扑要**确保连通整个网络**
    - 当首次连接交换机或网络**物理拓扑发生变化**时（有可能是人为改变或故障）,交换机都将进行**生成树的重新计算**
        
        ![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-19%20213354.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-19%20213354.png)
        
        ![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-19%20213418.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-19%20213418.png)
        

# 9. 虚拟局域网VLAN

## 9.1 **虚拟局域网VLAN概述**

以太网交换机工作在**数据链路层**（也包括物理层），使用一个或多个以太网交换机互连起来的交换式以太网，其所有站点都属于**同一个广播域，**随着交换式以太网规模的扩大，广播域相应扩大，其带来的弊端有：

- **广播风暴** 会浪费网络资源和各主机的CPU资源
    
    ![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-19%20214404.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-19%20214404.png)
    
- 难以管理和维护
- 潜在的安全问题
- **网络中会频繁出现广播信息**
    - TCP/IP协议栈中的很多协议都会使用广播：
        - 地址解析协议ARP（已知IP地址，找出其相应的MAC地址）
        - 路由信息协议RIP（一种小型的内部路由协议）
        - 动态主机配置协议DHCP（用于自动配置IP地址）
    - NetBEUI：Widnows下使用的广播型协议
    - IPX/SPX：Novel网络的协议栈
    - Apple Talk：Apple公司的网络协议栈
- 分割广播域的方法
    - 使用路由器可以隔离广播域
        
        ![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-19%20214619.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-19%20214619.png)
        
        由于**路由器默认情况下不对广播数据包进行转发**，因此路由器很自然地就可以隔离广播域。然而路由器的成本较高，局域网内部全部使用路由器来隔离广播域是不现实的
        
    - 虚拟局域网VLAN（Virtual Local Area Network）是一种**将局域网内的设备划分成与物理位置无关的逻辑组的技术，这些逻辑组具有某些共同的需求**
        
        ![https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-19%20214727.png](https://cdn.jsdelivr.net/gh/junmoxiao6661/pigo_image@main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-09-19%20214727.png)