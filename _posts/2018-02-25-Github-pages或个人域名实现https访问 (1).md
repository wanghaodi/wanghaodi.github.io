---
layout:     post
title:      Github
subtitle:   SSL
date:       2018-02-25
author:     HD
header-img: img/post-bg-alone.jpg
catalog: true
tags:
    - SSL
    - Github
    - https
---


# 前言
HTTPS（全称：Hyper Text Transfer Protocol over Secure Socket Layer），是以安全为目标的HTTP通道，简单讲是HTTP的安全版。即HTTP下加入SSL层，HTTPS的安全基础是SSL，因此加密的详细内容就需要SSL。 它是一个URI scheme（抽象标识符体系），句法类同http:体系。用于安全的HTTP数据传输。

但是，当使用https://访问个人域名或Github pages时会出现一个问题，浏览器会警告**站点不安全**，如图
![][1]
那么，我们应该怎么解决这个问题呢？

# 准备
首先，为大家介绍一下[CloudFlare][2]

> Cloudflare是一家美国的跨国科技企业，总部位于旧金山，在英国伦敦亦设有办事处。Cloudflare以向客户提供网站安全管理、性能优化及相关的技术支持为主要业务。通过基于反向代理的内容传递网络(ContentDeliveryNetwork,CDN)及分布式域名解析服务(DistributedDomainNameServer)，Cloudflare可以帮助受保护站点抵御包括拒绝服务攻击(DenialofService)在内的大多数网络攻击，确保该网站长期在线，同时提升网站的性能、访问速度以改善访客体验。

简单地说，CloudFlare是一家CDN提供商，它提供了免费的https服务(但不是应用SSL证书)。实现模式就是，用户到CDN服务器的连接为https，而CDN服务器到GithubPage服务器的连接为http，即在CDN服务器加上反向代理。

# 快速开始

 1. 首先我们需要在[CloudFlare][3]注册账户，注册完毕后登录账户
 2. 添加我们的域名
 ![][4]
 3. 点击NEXT，到当前页面后，选择Free
 ![][5]
 4. 到当前页面后点击 Continue
 ![][6]
 5. 到自己的域名注册商，设置DNS解析地址为CloudFlare所提供的DNS
 ![][7]
 6.设置完毕后状态
 ![][8]
 这里设置完毕后可能需要等待一些时间，才能成功，直到Overview变成如图状态
![][9]

# 设置DNS

 - 在 CloudFlare 的 DNS 设置域名匹配到自己的GithubPage(启用动态DNS加速)。

![][10]

 - 在 CloudFlare 的 Crypto 设置 SSL 为  Flexible 并设置Always use HTTPS为开启状态
![][11]
![][12]

# 设置Page Rules

 - 在 CloudFlare 的 Page Rules 中设置路由规则。
 ![][13]
 - 点击Create Page Rules,创建如图两条规则
 ![][14]

这样稍等一些时间即可成功。

# 后记

> 还有同学可能要问，如果我有两个域名，怎么使一个域名解析到另一个域名呢，比如，我有两个域名，一个是`whd.fun`另一个是`wanghaodi.top`我的目标是使`whd.fun`解析到`wanghaodi.top`实现两个域名访问同一个页面，其实，这也很容易，在CloudFlare中再添加一个域名

![][15]

> 并设置whd.fun的 Page Rules为如图所示即可，别忘了同意需要设置whd.fun的DNS，方法同wanghaodi.top域名一样，这里不再过多阐述。

![][16]

# 小结

我在博客中的每篇文章都是我一字一句敲出来的，转载的文章我也注明了出处，表示对原作者的尊重。同时也希望大家都能尊重我的付出。

最后，也希望大家关注我的个人博客 [HD Blog][17]

谢谢~



 

   

 


  [1]: http://ww1.sinaimg.cn/large/6712cbb1ly1foskjfxp6lj221s1860uk.jpg
  [2]: https://www.cloudflare.com/
  [3]: https://www.cloudflare.com/
  [4]: http://ww1.sinaimg.cn/large/6712cbb1ly1fosl59cvmmj221g13v437.jpg
  [5]: http://ww1.sinaimg.cn/large/6712cbb1ly1fosl6dfxs5j221m147tir.jpg
  [6]: http://ww1.sinaimg.cn/large/6712cbb1ly1fosl88ew25j221q180wmh.jpg
  [7]: http://ww1.sinaimg.cn/large/6712cbb1ly1foslbnvendj221q18043u.jpg
  [8]: http://ww1.sinaimg.cn/large/6712cbb1ly1foslcau7xxj21zp0tg41l.jpg
  [9]: http://ww1.sinaimg.cn/large/6712cbb1ly1fosle050vyj221q180tcg.jpg
  [10]: http://ww1.sinaimg.cn/large/6712cbb1ly1foslg0ty4kj221l1230wo.jpg
  [11]: http://ww1.sinaimg.cn/large/6712cbb1ly1foslhwjyywj21z010vtc2.jpg
  [12]: http://ww1.sinaimg.cn/large/6712cbb1ly1fosli57vb1j21wv0f3dgo.jpg
  [13]: http://ww1.sinaimg.cn/large/6712cbb1ly1foslk56vpsj221q180n3d.jpg
  [14]: http://ww1.sinaimg.cn/large/6712cbb1ly1foslkuw6fkj21u20evgmh.jpg
  [15]: http://ww1.sinaimg.cn/large/6712cbb1ly1foslpmmbqbj221m0fljsy.jpg
  [16]: http://ww1.sinaimg.cn/large/6712cbb1ly1foslqgth2lj21uq0g0gmt.jpg
  [17]: https://wanghaodi.top
