# 简单讨论下Mesh和API网关

今天有人贴了这个译文[“使用了 Service Mesh 后我还需要 API 网关吗？”](https://mp.weixin.qq.com/s/XX4qSBwRBAX-4pYXtd4Q8A),原文在这里：[“Do I Need an API Gateway if I Use a Service Mesh?”](https://blog.christianposta.com/microservices/do-i-need-an-api-gateway-if-i-have-a-service-mesh/)。其实之前还有个蚂蚁的文章，也列在这里，感兴趣的可以顺路一起看下：[“蚂蚁金服 API Gateway Mesh 思考与实践”](https://mp.weixin.qq.com/s/o4yrZXhW4tnggctL03t7kw)。这里不去讨论这些文章内容的本身和观点，只是正好有人讨论这个，我就把我的理解也写出来，可能有些观点是重合的，可能有些观点是冲突的，希望多一种声音带给大家多一些思考。

长话短说：

1. Service Mesh是宏观的，API网关是微观的。这句话解释起来可以长篇大论，从很具体的角度解释这个：在如果你做微服务，那么很可能会需要用到API网关，原因可以参考这个：[Pattern: API Gateway / Backends for Frontends
Context](https://microservices.io/patterns/apigateway.html)；当然，这句话还有一层意思，就是如果你做微服务，也未必一定要用API网关。我的观点是，**推荐在微服务架构里采用API网关**。当你有很多很多微服务的时候，我推荐你**一定使用Service Mesh**，但是这个时候就**没有必要使用API网关**了，因为**网关是Service Mesh的组件**。那么什么是“很多很多”，这里我给几个实践中可以参考的情况：
* 微服务数量很多，多到你需要查询系统才能知道具体有多少；相应的，如果你可以清晰记得你有几个“微服务”，那么就没有必要认为是“很多很多”
* 服务开始有了版本的分化
* 有可能采用了不同的技术栈开发服务
* 部署已经开始跨地域、跨数据中心
* 规模使用kubernetes的时候，**强烈建议**使用Service Mesh。我一直认为：Service Mesh弥补了Kubernetes里网络和服务治理欠缺的能力，而这些能力，是kubernetes本来就应该有的

2. API网关主要作用是**服务治理**，Service Mesh**除了服务治理**以外，还有很多**其他功能**，比如网络功能。在[这个文章(sidecar模式对Mesh性能影响与分析)](http://flomesh.cn/blog/bookinfo-vm-benchmark.html#sidecar%E6%A8%A1%E5%BC%8F%E5%AF%B9mesh%E6%80%A7%E8%83%BD%E5%BD%B1%E5%93%8D%E4%B8%8E%E5%88%86%E6%9E%90)里我把Service Mesh的作用，按照堆栈的方式做了分类：
* 网络设备的功能，比如软负载(常见的是ingress)
* 中间件的功能，比如链路的加解密、协议转换、格式转换
* SDK的功能，比如签名和验签等
* 服务治理的功能，包括采样和统计服务质量、链路特征、服务依赖等，进而完成熔断、降级等功能

3. **网关**是Service Mesh的核心组件。注意这里我用的**网关**这个词，而不是**API网关**。很有趣的一个事情是，在英文里，Service Mesh用的词汇是proxy，而API网关用的是Gateway--这两个词在英文里本身就很有明确概念上的区别。可以这么理解：
* Proxy强调的是“代理”
* Gateway强调的是“入口”

4. API网关除了**网关**之外，还强调**API**的能力，也就是对接口管理的功能。体现主要是：API生命周期的能力、应用的权限和访问控制、输入输出的校验等。通常而言，这些是Service Mesh不关注的领域

最后，简单总结下Service Mesh使用网关/代理的地方：
* Ingress 
* Egress
* Sidecar 

实际上，把完整的API网关功能融合到Service Mesh里，是一种趋势。我们开发的[Flomesh](http://flomesh.cn)就尝试把完整API能力带入到Service Mesh方案里。


