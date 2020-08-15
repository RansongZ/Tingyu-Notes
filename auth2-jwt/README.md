# auth2-jwt

## 🤝 吐血推荐
- [**万文长字分析OAuth 2.0+JWT+spring security完成认证授权-生产级（附带源码）**](https://mp.weixin.qq.com/s/uLM7XOcLoT08aIVfVjIrkg)


原创 汀雨v587  汀雨笔记  5月22日
点蓝色字关注“汀雨笔记”    



 前言导读

分析一下为什么要用OAuth2和JWT来做
1. **单点登录（SSO）**方案单击登录方案是最常见的解决方案，但单点登录需要每个与用户交互的服务都必须与认证服务进行通信，这不但会造成重复，**也会产生大量琐碎的网络流量**；
2. **分布式会话（Session）**方案通过将用户会话信息存储在共享存储中，如Redis，并使用用户会话的ID作为key来实现分布式哈希映射。当用户访问微服务时，会话数据就可以从共享存储中获取。该解决方案在高可用和扩展方面都很好，但是由于会话信息保存在共享存储中，所以需要一定的保护机制保护数据安全，因此在具体的实现中会具**有比较高的复杂度**。
3. **客户端令牌（Token）**方案令牌由客户端生成，并由认证服务器签名。在令牌中会包含足够的信息，客户端在请求时会将令牌附加在请求上，从而为各个微服务提供用户身份数据。此方案解决了分布式会话方案的安全性问题，**但如何及时注销用户认证信息则是一个大问题，虽然可以使用短期令牌并频繁地与认证服务器进行校验**，但并不可以彻底解决。JWT（JSON Web Tokens）是非常出名的客户端令牌解决方案，它足够简单，并且对各种环境支持程度也比较高
4. **客户端令牌与API网关结合**
通过在微服务架构中实施API网关，可以将原始的客户端令牌转换为内部会话令牌。一方面可以有效地隐藏微服务，另一方面通过API网关的统一入口可以实现令牌的注销处理。在David Borsos的第二个方案：分布式Session方案中要求开发者能够将用户会话信息单独拎出来进行集中管理。业界比较成熟的开源项目有Spring Session，其使用Redis数据库或缓存机制来实现Session存储，并通过过滤器实现Session数据的自动加载。随着近几年云服务应用的发展，基于令牌（Token）的认证使用范围也越来越广。对于基于令牌认证通常包含下面几层含义：
- 令牌是认证用户信息的集合，而不仅仅是一个无意义的ID。
- 在令牌中已经包含足够多的信息，验证令牌就可以完成用户身份的校验，从而减轻了因为用户验证需要检索数据库的压力，提升了系统性能。
- 因为令牌是需要服务器进行签名发放的，所以如果令牌通过解码认证，我们就可以认为该令牌所包含的信息是合法有效的。
- 服务器会通过HTTP头部中的Authorization获取令牌信息并进行检查，并不需要在服务器端存储任何信息。
- 通过服务器对令牌的检查机制，可以将基于令牌的认证使用在基于浏览器的客户端和移动设备的App或是第三方应用上。
·可以支持跨程序调用。基于Cookie是不允许垮域访问的，而令牌则不存在这个问题。
      综上所述，基于令牌的认证由于会包含认证用户的相关信息，因此可以通过验证令牌来完成用户身份的校验，完全不同于之前基于会话的认证。因此，基于令牌的这个优点，像T微信、支付宝、微博及GitHub等，都推出了基于令牌的认证服务，用于访问所开放的API及单点登录。接下来将重点介绍基于令牌认证方案中的OAuth 2.0和JWT


 总结
通过日志可以出

用户输入账号、密码，请求/auth/login到认证服务器

认证服务器根据username检验用户，存放全局UserDetails

认证服务器拦截请求判断是否合法，如果合法进入successHandler进行url重定向

重定向获取的code值继续访问/token，这时JWT会对token增强，添加我们需要返回给前端的字段

获得access_token和需要的字段



当前代码托管到我的Gitee :

https://gitee.com/ran_song/Limit

欢迎指正交流哦！！  

欢迎关注我的微信公众号<搜索：汀雨笔记>，会首发一些最新文章哦！
下面是我的个人网站：http://ransongv587.com




扫码关注公众号
精彩内容早知道

