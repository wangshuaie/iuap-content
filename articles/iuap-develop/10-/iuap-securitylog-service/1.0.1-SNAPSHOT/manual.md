
#安全日志技术说明及使用文档
##一．技术背景
iUAP、NC Cloud产品的开发非常迅速，为了保障安全性，需要有对应的日志进行记录，保证有迹可循，这种情况下就需要开发一套可满足国家及行业安全和规范要求的安全日志系统。

##二．应用场景
为iUAP、NC Cloud产品开发提供安全日记记录的方法及工具。比喻，用户登录，用户认证，金额调整等等，都需要记录用户的轨迹。

##三．技术方案
根据需求，提供了两种方式来满足安全日志系统的使用方式：同步调用、异步调用。根据客户的需求，选择相应的实现方式。

####1，同步调用

同步调用时业务系统需要集成iuap-securitylog-local-sdk，日志服务可和业务应用一起部署，也可以分开独立部署，会以war包的形式提供出来。

####2，异步调用

异步调用时业务系统需要集成iuap-securitylog-rest-sdk，日志服务与业务应用分开部署，为了保证日志不会丢失，能够准确记录，我们采用分布式的MQ来做中转，用户每一次记录日志都会把日志发送到MQ服务器，在日志服务端，会有一个定时任务去获取日志，然后存储到数据库中。


##四．服务端部署说明

####1，说明：

服务端部署，部署一个war包
<pre>
&lt;dependency>
  &lt;groupId>com.yonyou.iuap&lt;/groupId>
  &lt;artifactId>iuap-securitylog-service&lt;/artifactId>
  &lt;version>1.0.1-SNAPSHOT&lt;/version>
  &lt;type>war&lt;/type>
&lt;/dependency>
</pre>

####2，配置说明：
2.1，数据库信息配置（securitylog-application.properties）：

    #下面几项必须配置
    jdbc.url=jdbc:mysql://172.20.14.207:3306/securitylog?useUnicode=true&characterEncoding=gb2312
    jdbc.driverclass=com.mysql.jdbc.Driver
    jdbc.user=root
    jdbc.password=root

    #默认数据库连接池配置,不需要设置
    initPoolSize=10
    maxPoolSize=5


2.2，对于异步调用的方式，需要额外配置MQ服务的参数
MQ服务器配置securitylogMQConfig.properties，需要放在classpath目录下：

    #集群地址配置，多个的用逗号隔开，
    mq.address=172.20.14.133:5672

    #如果mq.isLocal=true, 可以不用配置下面两项的值
    mq.username=admin
    mq.password=admin
