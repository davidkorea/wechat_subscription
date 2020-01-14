# 想创建Reserved Dedicated Bare Metal实例？请问你真的搞懂EC2了吗？

其实说到云计算，说到公有云，说到AWS，创建一个EC2虚拟机实例，并没有什么难的，大家一看也就明白大概是怎么一回事。但是当大家考虑到cost effective的时候，那么问题来了，怎么使用EC2才更省钱呢？

虽然AWS默认的On-demand付费方式，已经是比On-premise的方式省钱，因为不用的时候可以关机，关机之后就不收费了。而数据中心的服务器，反正就是7 x 24不停运转。除此之外，AWS还提供其它的付费方式，比如可以竞价使用Spot实例，也可以签约长期（1年或3年）保留一个Reserved实例，涉及到BYOL的情况，还可以直接搞一台Reserved Host主机来使用。

那么问题来了，我可以搞一个Reserved Dedicated Instance吗？我可以竞价一个便宜点的Spot Dedicated Host吗？我可以拥有一个专属我自己的裸金属Dedicated Bare Metal实例吗？

下面，我们对实例Instance前面的这些定语（Reserved，Dedicated，Spot，Instance，Host）来分2个类别，为大家理清思绪。


## 1. Tenancy
- 通过wizard创建一个instance是，既需要VPC tenancy，又需要选择Instance Tenancy，重复的事情做两遍？
- 无论怎么样，VPC都是属于我自己的，那么为什么还要整出来default和dedicated之分呢？
- Instance tenancy表达的其实就是，是否要和其它用户共用一个物理服务器
- bare metal虽然是一个实例类型，但其实是给了你一个没有hypervisor的物理服务器
### 1.1 Instance Tenancy

### 1.2 VPC Tenancy

## 2. Purchase options
- On-demand
- Spot
- Reserved

到底什么样的实例可以使用什么样的购买方式呢？

