#Dubbox扩展组件#
##简介##
iuap-dubbox组件适配dubbo 2.8.4，基于spring 4.0.5 重新编译；同时支持服务调用过程中的上下文信息传递。上下文信息可以定制。

在对dubbox调用过程中，服务消费者向服务提供者传递上下文信息。传递的信息包括当前上下文中的用户信、线程信息等，还包括定制的日志输出信息。开发者可以在dubbo服务声明时指定过滤器，从而配置传递的上下文信息。

##配置和使用方式##
**1:在属性文件中，配置zookeeper连接地址、协议名称、负载策略等**

	dubbo.application.name=dubbox_provider
	dubbo.registry.address=zookeeper://localhost:2181
	dubbo.monitor.protocol=registry
	dubbo.protocol.name=dubbo
	dubbo.protocol.port=20880
	dubbo.service.loadbalance=consistenthash
	dubbo.log4j.file=logs/dubbox_provider.log
	dubbo.log4j.level=DEBUG

**2:增加dubbox对应的provider、customer的Spring配置文件**
	
	1）provider示例：applicationContext-dubbo-provider.xml

	<!-- dubbo 配置 -->
	<dubbo:application name="${dubbo.application.name}"/>
	<dubbo:registry address="${dubbo.registry.address}"/>
	<dubbo:protocol name="${dubbo.protocol.name}" port="${dubbo.protocol.port}"/>

	<dubbo:monitor protocol="registry"></dubbo:monitor>
	<!-- 生产者声明 -->
	<bean id="dubboxProvider" class="uap.web.dubbox.DubboxProviderImplTest"/>
	<dubbo:service interface="uap.web.dubbox.DubboxProviderItfTest" ref="dubboxProvider" version="0.1" retries="0" timeout="35000" loadbalance="${dubbo.service.loadbalance}" filter="logcontext"/>

	2）consumer示例：applicationContext-dubbo-consumer.xml

        <!-- dubbo 配置 -->
        <dubbo:application name="${dubbo.application.name}"/>
        <dubbo:registry address="${dubbo.registry.address}" timeout="15000"/>
        <dubbo:consumer check="false"/>

        <!-- 消费者声明 -->
        <dubbo:reference id="dubboxConsumer" interface="uap.web.dubbox.DubboxProviderItfTest" version="0.1" filter="logcontext"/>

**3:在服务消费者的工程上注入bean，调用服务**

**4:其他详细配置和参数，请参考示例工程(DevTool/examples/example_dubbox_consumer和example_dubbox_provider)和官网文档**