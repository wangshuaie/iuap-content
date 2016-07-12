
#安全日志技术说明及使用文档
##一．技术背景
iUAP、NC Cloud产品的开发非常迅速，为了保障安全性，需要有对应的日志进行记录，保证有迹可循，这种情况下就需要开发一套可满足国家及行业安全和规范要求的安全日志系统。

##二．应用场景
为iUAP、NC Cloud产品开发提供安全日记记录的方法及工具。比喻，用户登录，用户认证，金额调整等等，都需要记录用户的轨迹。

##三．使用方式
本组件为安全日志服务的异步SDK组件，需要集成到具体业务应用服务中。本组件的使用依赖安全日志服务组件同时需要依赖RabbitMQ服务，具体参考安全日志服务的使用说明。

##四．接口API和使用说明
####1, 首先引入jar包

1.1，需要的Jar包：

<pre>
     &lt;dependency>
       &lt;groupId>com.yonyou.iuap&lt;/groupId>
       &lt;artifactId>iuap-securitylog-rest-sdk&lt;/artifactId>
       &lt;version>1.0.1-SNAPSHOT&lt;/version>
     &lt;/dependency>
</pre>

1.2，需要配置的配置文件，securitylogrestsdk-applicationContext.xml和securitylogrestsdk-applicationContext-mq-provider.xml

1.3，	需要的MQ配置文件，logConfig.properties ，里面是关于MQ的内容。支持通过环境变量传入路径的方式，key值是securitylog-logConfig-filePath，即securitylog-logConfig-filePath=配置文件路径。

当然，若是从环境变量中读取不到，最后还是会走默认的classpath下的配置文件，注意和服务端保持一致：

    #集群地址配置，多个的用逗号隔开
    mq.address=172.20.14.133:5672

    #如果mq.isLocal=true, 可以不用配置下面两项的值
    mq.username=admin
    mq.password=admin

1.4,调用工具类：在要记录日志的地方，调用com.iuap.log.security.utils.SecurityLogUtil.saveLog(SecurityLog)方法来记录日志

