# 想创建Reserved Dedicated Bare Metal实例？请问你真的搞懂EC2了吗？

其实说到云计算，说到公有云，说到AWS，创建一个EC2虚拟机实例，并没有什么难的，大家一看也就明白大概是怎么一回事。但是当大家考虑到cost effective的时候，那么问题来了，怎么使用EC2才更省钱呢？

虽然AWS默认的On-demand付费方式，已经是比On-premise的方式省钱，因为不用的时候可以关机，关机之后就不收费了。而数据中心的服务器，反正就是7 x 24不停运转。除此之外，AWS还提供其它的付费方式，比如可以竞价使用Spot实例，也可以签约长期（1年或3年）保留一个Reserved实例，涉及到BYOL的情况，还可以直接搞一台Reserved Host主机来使用。

那么问题来了，我可以搞一个Reserved Dedicated Instance吗？我可以竞价一个便宜点的Spot Dedicated Host吗？我可以拥有一个专属我自己的裸金属Dedicated Bare Metal实例吗？

下面，我们对实例Instance前面的这些定语（Reserved，Dedicated，Spot，Instance，Host）来分2个类别，为大家理清思绪。


# 1. Tenancy
- 通过wizard创建一个instance时，既要选择VPC Tenancy，又需要选择Instance Tenancy，都是Tenancy，重复的事情要做两遍？
- 无论怎样，VPC都是属于我自己的，那么为什么还要整出来default和dedicated之分呢？

![image](https://user-images.githubusercontent.com/26485327/72311412-efee8700-36c7-11ea-9de0-cfe6ee261df7.png)

![image](https://user-images.githubusercontent.com/26485327/72311325-9423fe00-36c7-11ea-93a1-7cfd6ce2425a.png)


说到租户Tenancy，一般分为两类，一个是单租户Single Tenancy，一个是多租户Multi Tenancy。
1. 当你创建的EC2虚拟机和其它人创建的虚拟机在同一个物理服务器上的时候，即为多租户，你的EC2叫做Shared Tenancy Instance
2. 而当一个物理服务器只归你自己使用时，叫做单租户，你的EC2实例可以称作Dedicated Tenancy Instance

所以，Tenancy的划分就是看硬件是否共享，硬件仅自己用，就是单租户，硬件和别人共享使用，就是多租户。


## 1.1 Instance Tenancy
其实Instance Tenancy这个词，字面上看起来很难理解其真正含义，而其要表达的意思就是，是否要和其它用户共享一个物理服务器。所以更准确且有助于理解的方式可以称为：共享硬件，专用硬件。

### 1.1.1 Shared Hardware
AWS默认就是这种共享硬件的多租户方式，因为只有一台物理服务器可以为不同用户提供计算能力时，AWS才能降低成本，提供计算能力的按需服务。

### 1.1.2 Dedicated Hardware
专用硬件，就是这台物理服务器专属于你，别人的虚拟机不会被创建在该服务器上。可以分为以下几个类别
#### 1. Dedicated Instance
- 专属于你的物理服务器（含 Hypervisor）
- AWS创建专用实例
- 按专用实例收费
- 不适用于BYOL
- 不支持全部实例类型

和共享硬件的实例创建完全一样，只是物理服务器只有你自己使用而已。也因此，这个物理服务器上会运行你的其它的非专用实例，而没有其它人的实例。

#### 2. Dedicated Host
- 专属于你的物理服务器（含 Hypervisor）
- 自行创建EC2实例
- 按专用主机收费，无论是否创建实例，或创建多少实例
- 适用于BYOL
- 不支持全部实例类型
- RHEL, SUSE, Windows AMIs不可以用作专用主机上EC2的操作系统，AWS Linux和Ubuntu可以

对于AWS Nitro Based的主机类型，一个专用主机上面，可以运行同类型，不同Size的实例。但对于Xen Based的主机类型，只能创建和专用主机相同类型大小的实例。

#### 3. Bare Metal
- 专属于你的物理服务器（不含 Hypervisor）
- 不支持全部实例类型

虽然叫做裸金属实例，但其实就是一台没有虚拟化层的物理主机。因为没有Hypervisor，所以可以获得比其他专用硬件更好的性能。同时也可以自行选择VMware，HyperV等Hypervisor。

## 1.2 VPC Tenancy - An Attribute named “Hardware Tenancy” of VPC

![image](https://user-images.githubusercontent.com/26485327/72313950-858e1480-36d0-11ea-93fd-1bd790bc5930.png)

问题总是这样，绕了一圈又回来了，既然在硬件层面划分了Shared Tenancy共享硬件和Dedicated Tenancy专用硬件，VPC为什么还要划分？你可能会说VPC是一个网络层面的逻辑概念，你创建所有VPC都是专属于你的。而且Tenancy是按照硬件来划分的，也不是按照网络来划分的。没毛病，说的太对了。

而，这实际上是一个误解，创建VPC时，有一个VPC的属性选项，叫做“Hardware Tenancy”，指的是这个VPC中的实例被创建在共享还是专用硬件上。可以理解成一种批量操作。只要是放在Dedicated VPC中的实例，都是被创建在专用硬件上的实例。

特别注意：创建Bare Metal实例时，不能选择Dedicated Hardware VPC，只能选择默认。

那么，问题又来了，误解！怪我咯？这个锅必须是AWS来背啊，多写个Hardware会死啊？在创建向导界面明明可以写准确，找谁说理去！

![image](https://user-images.githubusercontent.com/26485327/72314414-3b0d9780-36d2-11ea-8f3e-ec4792811195.png)

-----


**OK，这个Tenancy总算是搞明白，并不是说硬件和VPC分别有两种租户方式，而是，两者都指的是硬件是否共享和是否专用。** 下面再来看一下实例的购买方式。

# 2. Purchase Options

其实上面提到的无论是共享硬件上的实例，还是专用硬件上的实例，都有多种付费方式。合理使用不同付费方式的组合可以在很大程度上节约成本。

## 2.1 On-demand
通过实例创建向导，默认就是这种方式。

这里有一个计费颗粒度Billing Granularity的概念，不同操作系统，计费颗粒度也不同
- AWS Linux - 按秒计费
  - 1h 5mins => charge 65 second
- Windows & Linux Distributions - 按小时计费
  - 1h 5mins => charge 2 hour

上面提到的所有硬件Tenancy类型均支持按需购买的模式。且均可"在EC2创建向"导界面实现购买。
- Shared Instance
- Dedicated Instance
- Dedicated Host
- Bare Metal


## 2.2 Spot
当实例价格超过你所竞价的价格时，AWS将主动关闭你的实例。会在关闭前的2分钟，发信息通知。当有可中断的业务是，可以选择竞价实例。

支持以下3种硬件Tenancy类型，均可在Spot界面实现购买。
- Shared Instance
- Dedicated Instance
- ~~Dedicated Host~~
- Bare Metal

![image](https://user-images.githubusercontent.com/26485327/72316071-44016780-36d8-11ea-8de5-c8a833b543f3.png)
![image](https://user-images.githubusercontent.com/26485327/72316121-672c1700-36d8-11ea-9f25-17aef68b057b.png)

## 2.3 Reserved

支持全部4中硬件Tenancy类型，Reserved Dedicated Host需要在专用主机界面购买，其它3中可在Reserved界面实现购买，

- Shared Instance
- Dedicated Instance
- Dedicated Host
- Bare Metal

![image](https://user-images.githubusercontent.com/26485327/72316300-0d781c80-36d9-11ea-9c0b-cddae1845ae2.png)
![image](https://user-images.githubusercontent.com/26485327/72316331-284a9100-36d9-11ea-88e8-b264e3b881c1.png)


























