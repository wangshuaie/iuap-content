# Dubbo服务开发


## 7.3.1 Dubbo微服务简介

Dubbo是一广泛使用的开源分布式服务框架，是阿里公布的，当当在此基础上做了扩展，更名为Dubbox（即DubboeXtensions）。Dubbo框架提供控制台和管理界面，可以利用管理界面查看服务的提供和消费情况，设置路由控制等。
Dubbo解决了分布式服务的URL调用配置复杂，相互之间依赖关系难以整理，服务容量难以估算的问题。并结合自身的集群机制以及版本控制机制，提供了动态扩容/缩容和动态升级/降级的功能。
使用zookeeper作为服务注册与发现的注册中心，结合与Spring的结合，使得服务的提供和调用变得简单，消费者端可以类似使用本地spring容器中的bean一样调用远程服务，iuap平台对dubbox服务的使用提供完整方案和示例，开发者进行简单的配置即可快速搭建分布式服务架构。

 ![](../image/image122.png)

## 7.3.2 Dubbo微服务配置

（1）maven配置
maven的依赖配置参考如下，其中版本号dubbo.version、zkclient.version、zookeeper.version版本统一定义，目前dubbox使用的版本为2.8.4.RELEASE。

 ![](../image/image123.png)
	如果使用iuap-dubbox组件，可以省略上述配置，直接在maven工程的pom文件中引入对iuap-dubbox组件的依赖即可（如下图），组件在原生的dubbox服务基础上，增加了对日志信息和业务信息上下文的传递，并引入了通用spring4.x版本编译过的dubbo2.8.4版本。

![](../image/image124.png) 

组件中对dubbox的默认引入如下：

 ![](../image/image125.png)

（2）属性文件配置

 ![](../image/image126.png)

属性文件中制定了Zookeeper的连接地址和端口、dubbo服务使用的端口号、负载均衡的策略等信息。

（3）spring配置文件，分为服务提供者和服务消费者

 ![](../image/image127.png)

 ![](../image/image128.png)

## 7.3.3 Dubbo微服务使用

    实现服务提供者，接口名和实现类名在配置文件中需要统一

 ![](../image/image129.png)

    消费端服务声明，参考配置中的信息，其中接口名和服务提供者接口相同。
    从spring容器中直接获取bean即可调用

 ![](../image/image130.png)

