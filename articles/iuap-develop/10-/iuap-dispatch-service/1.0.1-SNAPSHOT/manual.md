# 后台调度任务 #


** 1. 功能简介 **


本组件提供对Quartz的封装，提供独立的任务调度服务。避免业务系统直接配置Quartz的带来的性能消耗和集群环境的并发问题。 业务系统通过Rest服务添加任务，任务调动执行时回调业务系统的URL启动任务。


** 2. 配置说明** 

     (1) 下载WAR包文件
 
	 (2) 数据库建表
         执行附带的数据库脚本文件，请按照目前集成的数据库类型，选择不同的脚本。
     (3) 修改数据库连接
         在dispatch_dbinfo.properties文件中指定具体数据库。


** 3 使用示例 **
  Rest服务为post调用

* 新建任务Rest接口*

 ** A、新增一个简单任务**

*(1)Rest服务URL*
   "http://localhost:8080/iuap-dispatch-service/dispatchservice/add.do"
*(2)参数,格式如下*

{"replace":true,"recallConfig":{"data":{},"option":{"url":"http://localhost:8080/iuap-dispatch-service/dispatchservice/pause.do"},"recallType":"HTTP"},"taskConfig":{"triggerType":"SimpleTrigger","jobCode":"22b511e8-1b80-4f4d-b65e-48f52d8aa682","groupCode":"simpleTaskGroup","startDate":1463813876403,"endDate":null,"priority":0,"timeConfig":{"interval":2,"intervalType":"SECOND","isForever":false,"repeatCount":1}},"note":"note"};

其中

*替换属性：*
"replace" 指是否替换已有的同名任务。 note为调用者自定义的参数

*回调配置：* 指回调参数、URL地址以及回调类型，目前只支持HTTP类型

{"data":{},"option":{"url":"*http://localhost:8080/iuap-dispatch-service/dispatchservice/pause.do*"},"recallType":"HTTP"}

"url":http://localhost:8080/iuap-dispatch-service/dispatchservice/pause.do
这个是示例，要传入回调的URL

*时间配置：*{"interval":2,"intervalType":"SECOND","isForever":false,"repeatCount":1}

*任务配置（集成了时间配置）*：      {"triggerType":"SimpleTrigger","jobCode":"666f35fc-387e-41c6-96e0-146acd5541a6","groupCode":"simpleTaskGroup","startDate":1464065198495,"endDate":null,"priority":0,"timeConfig":{"interval":2,"intervalType":"SECOND","isForever":false,"repeatCount":1}} 




 ** B、新增一个Cron任务**
 *(1)Rest服务URL*
   *(1)Rest服务URL*
   "http://localhost:8080/iuap-dispatch-service/dispatchservice/add.do"
* (2)参数,格式如下*
 "{\"replace\":true, \"recallConfig\":{\"data\":{\"serverName\":\"Windows 2003\"},\"option\":{\"url\":\"http://localhost:8080/iuap-dispatch-service/dispatchservice/pause.do\"},\"recallType\":\"HTTP\"}, \"taskConfig\":{\"cronExpress\":\"* */1 * * * ?\",\"groupCode\":\"cronTaskGroup\",\"jobCode\":\"cronTask\",\"priority\":0,\"triggerType\":\"CronTrigger\"}}";

其中

*替换属性：*
"replace" 指是否替换已有的同名任务。 note为调用者自定义的参数

*回调配置：* 指回调参数、URL地址以及回调类型，目前只支持HTTP类型

{"data":{},"option":{"url":"*http://localhost:8080/iuap-dispatch-service/dispatchservice/pause.do*"},"recallType":"HTTP"}
 
    
*任务配置：*
 " \"taskConfig\":{\"cronExpress\":\"* */1 * * * ?\",\"groupCode\":\"cronTaskGroup\",\"jobCode\":\"cronTask\",\"priority\":0,\"triggerType\":\"CronTrigger\"};



 
