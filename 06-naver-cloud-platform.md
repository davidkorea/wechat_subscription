
# NCP Windows Server

说到公有云，在国内当然是阿里云、腾讯云、华为云等一线大厂，而国外则是AWS，Azure，Google Cloud几乎霸占了整个市场。韩国当然也不例外，AWS和Azure必须是主流，几乎所有企及考虑上云的时候，首先都会考虑这两个巨无霸。

那么，为什幺要使用一个名不见经传的公有云服务商 Naver Cloud Platform，当然是在特定的场景下不得不使用Naver云平台。韩国一些政府公共机构的网站只允许韩国国内IP登陆，因此对于身在国内的小伙伴，有需要在韩国政府网站上申请一些资料的话，真是难上加难。当然，如果你知道실업급여的话，那一定明白我在说什么。

因为我一直都在使用AWS，所有首先是使用首尔地区的EC2实例，但是韩国政府网站提示AWS的公有IP是海外IP，不能使用。因此，不得不转战Naver这个韩国本土的云厂商。

下面具体来说一下Naver Cloud Platform（NCP）的账号申请，以及Windows虚拟机的创建

# 1. NCP账号申请

1. 韩国手机号
2. 韩国信用卡/借记卡

需要韩国手机号进行实名认证，以及绑定支付方式。目前新用户注册，绑定支付方式后赠送10万韩币积分，用于支付云服务使用费用。

申请地址：[https://www.ncloud.com/join/type](https://www.ncloud.com/join/type)

<img width="400"  src="https://user-images.githubusercontent.com/26485327/85983327-4eaf7f00-ba22-11ea-868b-d8c342232763.png">


# 2. 创建Windows虚拟机

这里主要来介绍Windows虚拟机的创建，因为我的场景是政府网站的资料申请。需要注意的是，相比较AWS等公有云厂商，NCP在创建了虚拟机后，还需要一个端口映射的操作。





# 3. 资费
