
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

这里主要来介绍Windows虚拟机的创建，因为我的场景是政府网站的资料申请。

1. 选择windows镜像
<img width="1448" src="https://user-images.githubusercontent.com/26485327/85984901-d26a6b00-ba24-11ea-9635-381d118096e8.png">

2. 选择按小时计费方式
<img width="676" src="https://user-images.githubusercontent.com/26485327/85984914-d6968880-ba24-11ea-9aff-0b017d11ffb5.png">

3. 生成密钥，并下载保存。此密钥用于远程登陆
<img width="802" src="https://user-images.githubusercontent.com/26485327/85984919-d7c7b580-ba24-11ea-9d14-32e33e087641.png">

4. 创建ACG（Access Control Group），用于开放虚拟机远程桌面的TCP端口3389
<img width="844" src="https://user-images.githubusercontent.com/26485327/85984923-d8604c00-ba24-11ea-8188-e29732c3400c.png">
<img width="988" src="https://user-images.githubusercontent.com/26485327/85984920-d8604c00-ba24-11ea-9ae1-659c3ccd3244.png">

5. 审查虚拟机规格，并创建
<img width="1179" src="https://user-images.githubusercontent.com/26485327/85984925-d8f8e280-ba24-11ea-9015-9e6f57075514.png">


# 3. 远程连接Windows虚拟机

需要注意的是，相比较AWS等公有云厂商，NCP在创建了虚拟机后，还需要一个端口映射的操作才能进行远程访问。相当于，先访问到Naver对外提供的一个堡垒机，通过该堡垒机的端口和创建的虚拟机的端口映射，来访问虚拟机。

<img width="700"  src="https://user-images.githubusercontent.com/26485327/85985544-d480f980-ba25-11ea-9794-192405e399ec.png">


1. 


# 4. 资费
