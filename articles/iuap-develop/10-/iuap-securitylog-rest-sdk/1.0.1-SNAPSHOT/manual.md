#安全日志异步SDK组件概述#


## 业务需求 ##

iUAP、NC Cloud产品的开发非常迅速，为了保障安全性，需要有对应的日志进行记录，保证有迹可循，这种情况下就需要开发一套可满足国家及行业安全和规范要求的安全日志系统。

## 解决方案
安全日志组件为iUAP、NC Cloud产品开发提供安全日记记录的方法及工具。比喻，用户登录，用户认证，金额调整等等，都需要记录用户的轨迹。


# 整体设计 #

## 依赖环境 ##

组件采用Maven进行编译和打包发布，其对外提供的依赖方式如下：

	<dependency>
	  <groupId>com.yonyou.iuap</groupId>
	  <artifactId>iuap-securitylog-rest-sdk</artifactId>
	  <version>${iuap.modules.version}</version>
	</dependency>

${iuap.modules.version} 为平台在maven私服上发布的组件的version。

##基本概念
安全日志异步调用SDK组件，这里的异步是指使用者在调用本组件接口写安全日志时，调用者调用安全日志接口，安全日志把日志放到后台MQ服务器后立刻返回，调用者无需要等待安全日志写入到数据库。
这样使写安全日志的操作尽量较少对业务逻辑的影响。


##使用方式

本组件为安全日志服务的异步SDK组件，需要集成到具体业务应用服务中。本组件的使用依赖安全日志服务组件同时需要依赖RabbitMQ服务，具体参考安全日志服务的使用说明。

##组件配置##

   需要配置的配置文件，securitylogrestsdk-applicationContext.xml和securitylogrestsdk-applicationContext-mq-provider.xml

   需要的MQ配置文件，logConfig.properties ，里面是关于MQ的内容。支持通过环境变量传入路径的方式，key值是securitylog-logConfig-filePath，即securitylog-logConfig-filePath=配置文件路径。

当然，若是从环境变量中读取不到，最后还是会走默认的classpath下的配置文件，注意和服务端保持一致：

    #集群地址配置，多个的用逗号隔开
    mq.address=172.20.14.133:5672

    #如果mq.isLocal=true, 可以不用配置下面两项的值
    mq.username=admin
    mq.password=admin


##组件API##
调用工具类：在要记录日志的地方，调用com.iuap.log.security.utils.SecurityLogUtil.saveLog(SecurityLog)方法来记录日志
    public void testSaveLog() throws UnsupportedEncodingException {
		SecurityLog log = new SecurityLog();
		log.setContentDes("测试额外企鹅企鹅王企鹅额企鹅请问请问请问请问我去恶趣味问请问请问请问测试额外企鹅企鹅王企鹅额企鹅请问请问请问请问测试额外企鹅企鹅王企鹅额企鹅请问请问请问请问我去恶趣味问请问请问请问测试额外企鹅企鹅王企鹅额企鹅请问请问请问请问我去恶趣味问请问请问请问我去恶趣味问请问请问请问");
		log.setNotice("测试 额外俄武器恶趣味请问请问额委屈额企鹅企鹅全文请请问去 ");
		log.setTimestamp(new Date());
		log.setIp("172.16.50.238");
		log.setProduct("UAP产品中心");
		log.setResult("成功");
		log.setSystem("windows系统");
		log.setLevel("-1");
		log.setUserAuthType("登录人认证");
		log.setUserIdentify("CA认证");
		log.setUserCode("usercode");
		log.setLessee("iuap.liuxing");
		SecurityLogUtil.saveLog(log);
	}

