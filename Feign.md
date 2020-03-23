### Feign

feign是一个http请求调用的轻量级框架，可以以java接口注解的方式调用http请求。封装了http调用的流程，适合面向接口化的编程习惯。

常见的http调用服务的框架，HttpURLConnection、Apache HttpComponnets、OkHttp3、Netty等等。

feign的配置

feign的调用分两层：即ribbon层的调用和hystrix的调用，高版本的hystrix默认是关闭的。

feign运行流程：

![feign运行流程](/home/huajiang/图片/feign运行流程.png)

@feignclient会使用jdk动态代理方式，生成远程调用接口的实例，并将其注入到IOC容器中。生成动态代理实例的核心，就是需要定制一个调用处理器，调用处理器会根据方法的调用进行处理，分发给method handler进行具体的方法调用。最后交由feign client进行最后的url请求。

如果为服务上添加了hystrix依赖，那么feign会通过代理模式，自动将所有方法用hystrix进行包装。

![微服务调用过程](/home/huajiang/图片/微服务调用过程.png)

可以看见feign的调用过程分为两层，一层是hystrix调用，一层是ribben调用。因此两层的超时时间设置有一定的关系：

hystrix的超时时间 = ribbon的重试次数 *（ribbon.readTimeout + ribbon.connectTimeout)

ribbon的重试次数  = 1 + ribbon.MaxAutoRetries + ribbon.MaxAutoRetriesNextServer + (ribbon.MaxAutoRetries * ribbon.MaxAutoRetriesNextServer)