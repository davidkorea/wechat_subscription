
## 不敢使用Spot实例？AWS上云成本优化方法论

众所周知，相对于On-Premise数据中心，除了考虑日常业务负载之外，还需要预留可以应付peak load的能力。而云计算素有“pay-as-you-go”的美称，用多少买多少，即买即用，无需大量先前成本投入。

可是云计算虽然已经有如此优点，我们仍然可以通过更多方式，来继续节约成本。

在具体讨论之前，先分享几个心法：
1. CPU，价格不同，关注ECU
2. 相同配置spec情况下，T实例优于其他实例，因为T实例可以burst
3. 对于On-demand实例，选择新一代的实例类型，将获得更高的配置以及更低的价格
4. 对于可中断的业务，使用旧一代的Spot Instance实例，将会大大降低被关闭的几率
5. 使用Fleet EC2，将On-demand和Spot实例放在同一个Auto Scaling Group，搭配ELB使用
6. 对于GPU资源，除了使用G、P实例外，还可以使用Elastic Graphics和Elastic Inference

本文将针对不同的使用场景，通过以下几个方面，来探讨在计算资源使用方面如何节约上云成本。文末会附上总结脑图。

- Reservered Instance
- Spot Instance
- Severless
- Container

需要指明的是，节约成本不是一蹴而就，一劳永逸的。因为随着AWS的不断推陈出新，我们使用的计算资源价格也会随之变化。我们需要做的就是以“day-by-day”的方式。监控日常使用行为，比较更换其它计算资源后的成本，以及转换计算资源所需要的人工成本。

# EC2计算资源

## 1. lift-and-shift 直接迁移

> **lift-and-shift**: you lift the code out of an environment and shift it to another.
 
应用从本地数据中心迁移上云时，可以考虑直接上云和重构上云两种方式。而直接上云，顾名思义就是将本地应用原封不动的迁移到云端，此种方式最为简单。

当考虑到服务运行成本以及上云维护成本时，使用与本地服务器相近配置的On-demand实例是比较常见的选择，但该选择可能不是cost-effective的方式。

下面来具体讨论一下，如何来选择相对具有更高性价比的按需实例。

首先看一下实例命名规则，`c5d.large`
- Instance type, c
- Generation, 5
- Additional capabilities
  - `a`: AMD EPYC processor
  - `d`: NVMe SSD
  - `e`: extended memory
  - `n`: enhanced network
  - `s`: smaller vCPU and memory
- Instance size, large

### 1.1 Instance Type and Size
AWS提供**通用型，计算优化型，内存优化型，存储优化型，加速计算型（GPU）**共五大类别的实例，供用户根据不同使用需求来选择。

相同实例类型，Size每升高一级，配置x2，价格x2。c5.9xlarge的价格 = 2 * (4xlarge价格) + 1 * (xlarge价格)
![]()

相同实例类型，Generation越新，性能更好，价格更低。迁移至不同代际的实例可能需要对应用进行相应的架构调整和运维调整
![]()

不同实例类型，比较每ECUs价格，和每G内存价格
- 1 ECU（Elastic Compute Units）= 1 GHz Intel Xeon processing power. Intel CPU Only
- 相同的配置，处理比较价格优势外，选择T类型可以获得burst加速
- 当需要T2，T3在Unlimited mode下burst时，可以考虑及酸性优化实例


### 1.2 CPU

目前AWS使用的CPU包含Intel，AMD EPYC，AWS Graviton，虽然不同厂家的CPU，很难使用ECUs（Intel Only）将算力标准化后来比较性能-价格

相同配置下AMD可能会比Intel便宜

### 1。3 instance Software Stack
- OS: AWS Linux < Linux Distributions < Windows
- Database: MS SQL Web < MS SQL Standard < MS SQL ENterprise

### 1.4 Tenancy
Shared < Dedicated Host < Bare Metal

- Dedicated Host：Single AWS account use, can spin multiple Dedicated Instance, charge for whole Host
- Dedicated Instance: Single AWS account use, charge for Instance
- Bare Metal: no Virtual Machine Monitor(hypervisor)，cannot devide into small instances to use





### 2. SAP，Databases

如果是需要长期稳定运行的服务，比如SAP或是大型数据库，建议考虑使用Reserved Instance，相较于On-demand实例的价格可以节约大约70%。

下面来具体讨论一下RI的不同签约方式



### 3. EC2 Fleets

EC2 Fleets就是On-demand Instance+ Spot Instance的组合，其中的On-demand实例还可以通过购买RI的方式来获得折扣。这算得上是最为cost-effective的方式。经典使用是将On-demand + Spot实例放在一个Auto Scaling Group，来搭配ELB来使用。

下面分别来介绍一下





## 图像处理和机器学习
- EG
- EI

## Serverless
- Lambda，需要考虑非直接费用，如其他服务激活trigger的费用，以及数据传输费用
  - 事件驱动，轻量，无状态
  - 专注代码开发，无需关心基础架构
  - 测试不同RAM情况下的执行时间，开源工具 Epsagon

## 微服务
- ECS，不收托管费，针对计算资源付费。EKS，有托管费用
  - container可以提高基础设置的计算使用率，因为Docker engine比hypervisor轻量
  - 使用容器不可避免的管理多cluster






