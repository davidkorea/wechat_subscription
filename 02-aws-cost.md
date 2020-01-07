
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

# 1. On-demand Instance

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

## 1.1 Instance Type and Size
AWS提供**通用型，计算优化型，内存优化型，存储优化型，加速计算型（GPU）**共五大类别的实例，供用户根据不同使用需求来选择。

相同实例类型，Size每升高一级，配置x2，价格x2。c5.9xlarge的价格 = 2 * (4xlarge价格) + 1 * (xlarge价格)
![]()

相同实例类型，Generation越新，性能更好，价格更低。迁移至不同代际的实例可能需要对应用进行相应的架构调整和运维调整
![]()

不同实例类型，比较每ECUs价格 和 每G内存价格
- 1 ECU（Elastic Compute Units）= 1 GHz Intel Xeon processing power. Intel CPU Only
- 相同的配置，除了比较价格优势外，选择T类型可以获得burst加速
- 当需要T2，T3在Unlimited mode下burst时，可以考虑计算型优化实例


## 1.2 CPU

目前AWS使用的CPU包含Intel，AMD EPYC，AWS Graviton，虽然不同厂家的CPU，很难使用ECUs（Intel Only）将算力标准化后来比较性能-价格

相同配置下AMD可能会比Intel便宜

## 1.3 Instance Software Stack
- OS: AWS Linux < Linux Distributions < Windows
- Database: MS SQL Web < MS SQL Standard < MS SQL ENterprise

## 1.4 Tenancy
价格 Shared < Dedicated Host < Bare Metal

- Dedicated Host：Single AWS account use, can spin multiple Dedicated Instance, charge for whole Host
- Dedicated Instance: Single AWS account use, charge for Instance
- Bare Metal: no Virtual Machine Monitor(hypervisor)，cannot devide into small instances to use





# 2. Reserved Instance

如果是需要长期稳定运行的服务，比如SAP或是大型数据库，建议考虑使用Reserved Instance，相较于On-demand实例的价格可以节约大约75%。

签约时间越长（12个月或36个月），越多的提前付费，折扣越高

下面来具体讨论一下RI的不同签约方式。

## 2.1 Scope

### Regional Reservation
2 features，折扣或自动应用在符合条件的on-demand实例上

- **AZ Flexibility**，所选Region的所有AZ均可以享受RI折扣
- **Instance Size Flexibility**，For Linux and Unix RI with shared Tenacy，if reserve c5.4xlarge, can apply 2 c5.2xlarge, or 4 c5.xlarge

### AZ-Specific Reservation
- no AZ Flexibility and Size Flexibility
- AWS可以保证在任何需要实例的时候提供，无需担心供给不足的情况


## 2.2 Payment
- ALL Upfront
- Partial UPfront，charged a discounted hourly rate on a monthly basis
- No Upfront, billed a discounted hourly rate for every hour

## 2.3 Offering class
- **Standard** Reserved Instance
  - change AZ in same Region
  - change scope： AZ-specific <-> regional
  - change size within same instance type

- **Convertible** Reserved Instance，除了上面的要素可以更改外，还可以
  - change instance type, platfrom, scope
  - change instance generation 
  - 更改前后的normolization factor需要保持一致，否则不能通过变更请求
    - c5.4xlarge(32) = 2 c5.2xlarge(32)
  
normolization factor：
- nano - 0.25
- micro - 0.5
- small - 1
- medium - 2
- large - 4
- xlarge - 8
- 2xlarge - 16


![]()


## 2.4 Floating折扣
设置关联账户，当购买账户还有余量没用完时，可以给关联账户的相应实例使用折扣

## 2.5 多实例同小时折扣

同一个自然小时内（01：00 - 01：59），多个符合签约RI条件的on-demand实例可以同时享受折扣，所有实例运行时间加总超过1小时的部分不再享受折扣

## 2.6 Expiration
当签约的1年或者3年期满后，EC2实例会继续运行，但折扣将自动消失。西药在期满前，手动续费

## 2.7 Scheduled Reserved Instance
指定开始时间 和 运行时长，相较于peak-hour按需价格的5%的折扣，off-peak hour的10%的折扣。不是所有Region都支持

# 3. Spot Instance

每当提到随着价格变动Spot实例会自动被AWS关掉的情况，很自然地，大家会对于使用Spot实例产生犹豫。但是对于公有云的架构设计，有这么一条原则，就是“Architect for failure”。我们应该使用一群小的实例跑同一个服务，来消除各种不确定因素引起的服务不可用。在这种情况下，我们可以放心大胆的来使用Spot实例来获得更多的优惠折扣。

## 3.1 Spot Instance Pools

Spot Instance Pools是由特定的实例类型，实例大小，操作系统和AZ构成的，由AWS管理的未使用的EC2实例。

通过设定多个Spot Pools，可以降低实例中断造成的影响。因为一个实例关闭后，还可以在多个pool中选择其他的实例来使用。

选择方式有如下3中：
1. lowestPrice，从所有Spot Pools中选择最低价格来创建Spot实例
2. diversified，从多个Spot Pools中选择实例来满是请求的计算容量
3. instancePoolsToUseCount，设定实例需要在多少个Spot Pools中分布

## 3.2 Defined-Duration Spot Instance
可以预定Spot实例最长运行6小时，期间无中断。大约30%-50%折扣

## 3.3 Request type
- One-time
- Persistent

可以在Launch Template的Advanced details中选择 customize





# 4. EC2 Fleets

EC2 Fleets就是On-demand Instance+ Spot Instance的组合，其中的On-demand实例还可以通过购买RI的方式来获得折扣。这算得上是最为cost-effective的方式。经典使用是将On-demand + Spot实例放在一个Auto Scaling Group，来搭配ELB来使用。


ASG可以通过Launch Configuration和Launch Template来创建。若创建EC2 Fleets则必须使用Launch Template来创建ASG

## 4.1 先决条件
#### 1. 创建Launch Template
- AMI
- Instance Type
- Key Pair
- Network Interface
  - enable “Auto-assign public IP” for non-default VPC using
#### 2. 创建ELB
提前创建好负载均衡器，以便在创建ASG时，直接关联已创建好的负载均衡器
- CLB，无需Target Group，创建好后备用
- ALB，需要创建Target Group，关联ASG后，会自动将ASG创建的实例注册至该Target Group下

## 4.2 创建ASG
- select “Launch Template” created already in above steps
- **Fleet Composition** - `Combine purchase options and instances`
- **Instance Types**, On-demand实例，按照创建有限顺序排列，建议多余2个。当排在前面的实例由于某种原因无法创建时，会自动以此向下选择实例类型来创建，以满足需要的计算容量
- **Instances Distribution** - uncheck `Use the default settings to get started quickly`

- **On-Demand Allocation Strategy** - `Prioritized`, 按照上面**Instance Types**中的优先顺序
- **Maximum Spot Price**
  - `Use default (recommended)`, 推荐此选项，只要低于按需的价格，都可以创建。以避免自己设定价格总是不能满足，导致创建失败
  - `Set your maximum price (per instance/hour)`
- **Spot Allocation Strategy**
  - `Launch Spot Instances optimally based on the available Spot capacity per Availability Zone`
  - `Diversify Spot Instances across your [ 2 ] lowest priced instance types per Availability Zone`

- **Optional On-Demand Base** - `Designate the first [ 2 ] instances as On-Demand`
- **On-Demand Percentage Above Base** - `[ 70 ] % On-Demand and 30% Spot`
- **Group size** - `Start with [ 5 ] instances`
- **Network** - 选择自定义VPC时，需要提前在Launch Template中设定地洞分配公有IP

解释如下：
- ASG默认创建**Group size** = `5`个实例
- 其中**Optional On-Demand Base** = `2`个On-demand按需实例
- 剩余3个实例容量，将按照**On-Demand Percentage Above Base** = `70% On-demand, 30% Spot`的比例进行分配
  - 即，2个On-demand + 1个Spot
  
接下来继续关联ELB，展开**Advanced Details**
- **Load Balancing** - `Receive traffic from one or more load balancers`，一般情况下CLB和ALB二选一即可，但也允许ASG创建的实例注册到多个负载均衡器下，非ASG创建的实例不可
  - **Classic Load Balancers** - `[ classiclb ]`
  - **Target Groups** - `[ My-targetgroup ]`



# 4. 图像处理和机器学习
- EG
- EI

# 5. Serverless
- Lambda，需要考虑非直接费用，如其他服务激活trigger的费用，以及数据传输费用
  - 事件驱动，轻量，无状态
  - 专注代码开发，无需关心基础架构
  - 测试不同RAM情况下的执行时间，开源工具 Epsagon

# 6. 微服务
- ECS，不收托管费，针对计算资源付费。EKS，有托管费用
  - container可以提高基础设置的计算使用率，因为Docker engine比hypervisor轻量
  - 使用容器不可避免的管理多cluster






