#安全日志同步SDK组件概述#


## 业务需求 ##

iUAP、NC Cloud产品的开发非常迅速，为了保障安全性，需要有对应的日志进行记录，保证有迹可循，这种情况下就需要开发一套可满足国家及行业安全和规范要求的安全日志系统。

## 解决方案
安全日志组件为iUAP、NC Cloud产品开发提供安全日记记录的方法及工具。比喻，用户登录，用户认证，金额调整等等，都需要记录用户的轨迹。


# 整体设计 #

## 依赖环境 ##

1.1，组件采用Maven进行编译和打包发布，其对外提供的依赖方式如下：

${iuap.modules.version} 为平台在maven私服上发布的组件的version。

##基本概念
安全日志同步调用SDK组件，这里的同步是指使用者在调用本组件接口写安全日志时，调用者的业务逻辑和写日志是同步进行的，在安全日志调用返回前，调用者需要等待。


##使用方式

本组件为安全日志服务的同步SDK组件，需要集成到具体业务应用服务中。本组件的使用依赖于安全日志服务组件，即要部署安全日志服务，具体参考安全日志服务的使用说明。



# 使用说明 #


##组件配置##

（1）spring配置参考示例工程中，security-example-applicationContext.xml。

（2）需要配置服务信息的配置文件securitylogconfiger.properties，也支持从环境变量中传入，key值为securitylogconfiger-filePath，传入方式为securitylogconfiger-filePath = 配置文件路径。

    #使用非公有服务时，需要配置日志服务所在的ip和端口
    serverip=127.0.0.1
    serverport=8080

    #应用名

    appname=iuap-securitylog-server

##组件API##

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



