# 想创建Reserved Dedicated Bare metal实例？请问你真的搞懂EC2了吗？

### Tenancy
- 通过wizard创建一个instance是，既需要VPC tenancy，又需要选择Instance Tenancy，重复的事情做两遍？
- 无论怎么样，VPC都是属于我自己的，那么为什么还要整出来default和dedicated之分呢？
- Instance tenancy表达的其实就是，是否要和其它用户共用一个物理服务器
- bare metal虽然是一个实例类型，但其实是给了你一个没有hypervisor的物理服务器

### Purchase options
- On-demand
- Spot
- Reserved

到底什么样的实例可以使用什么样的购买方式呢？

