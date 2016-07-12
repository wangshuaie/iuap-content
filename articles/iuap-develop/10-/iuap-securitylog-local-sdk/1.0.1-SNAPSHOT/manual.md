
#安全日志技术说明及使用文档
##一．技术背景
iUAP、NC Cloud产品的开发非常迅速，为了保障安全性，需要有对应的日志进行记录，保证有迹可循，这种情况下就需要开发一套可满足国家及行业安全和规范要求的安全日志系统。

##二．应用场景
为iUAP、NC Cloud产品开发提供安全日记记录的方法及工具。比喻，用户登录，用户认证，金额调整等等，都需要记录用户的轨迹。

##三．使用方式
本组件为安全日志服务的同步SDK组件，需要集成到具体业务应用服务中。本组件的使用依赖与安全日志服务组件，具体参考安全日志服务的使用说明。

##四．接口API和使用说明


###1，首先引入jar包
1.1需要的jar包
<pre>
  &lt;dependency>
      &lt;groupId>com.yonyou.iuap&lt;/groupId>
      &lt;artifactId>iuap-securitylog-local-sdk&lt;/artifactId>
      &lt;version>1.0.1-SNAPSHOT&lt;/version>  
  &lt;/dependency>
</pre>

1.2，	需要配置服务信息的配置文件securitylogconfiger.properties，也支持从环境变量中传入，key值为securitylogconfiger-filePath，传入方式为securitylogconfiger-filePath = 配置文件路径。

    #使用非公有服务时，需要配置日志服务所在的ip和端口
    serverip=127.0.0.1
    serverport=8080

    #应用名
    appname=SecurityLog_Server

1.3,调用工具类：在要记录日志的地方，调用com.iuap.log.security.utils.SecurityLogUtil.saveLog(SecurityLog)方法来记录日志



