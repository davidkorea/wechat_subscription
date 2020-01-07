


## 还不敢使用Spot实例？AWS上云成本节约方法论

众所周知，相对于On-Premise数据中心，除了考虑日常业务负载之外，还需要预留可以应付peak load的能力。而云计算素有“pay-as-you-go”的美称，用多少买多少，即买即用，无需大量先前成本投入。

可是云计算虽然已经有如此优点，我们仍然可以通过更多方式，来继续节约成本。

在具体讨论之前，先分享几个心法：
1. 相同配置spec情况下，T实例优于其他实例，因为T实例可以burst
2. 对于On-demand实例，选择新一代的实例类型，将获得更高的配置以及更低的价格
3. 对于可中断的业务，使用旧一代的Spot Instance实例，将会大大降低被关闭的几率
4. 使用Fleet EC2，将On-demand和Spot实例放在同一个Auto Scaling Group，搭配ELB使用
5. 对于GPU资源，除了使用G、P实例外，还可以使用Elastic Graphics和Elastic Inference

本文将针对不同的使用场景，通过以下几个方面，来探讨在计算资源使用方面如何节约上云成本。文末会附上总结脑图。

- Reservered Instance
- Spot Instance
- Severless
- Container

需要指明的是，节约成本不是一蹴而就，一劳永逸的。因为随着AWS的不断推陈出新，我们使用的计算资源价格也会随之变化。我们需要做的就是以“day-by-day”的方式。监控日常使用行为，比较更换其它计算资源后的成本，以及转换计算资源所需要的人工成本。

## 长期稳定运行的业务

签约Reserved Instance + Spot Instance


## 机器学习

- EG
- EI

## 微服务
- Lambda，需要考虑非直接费用，如其他服务激活trigger的费用，以及数据传输费用
- ECS，不收托管费，针对计算资源付费
- EKS，有托管费用






