# 后台调度任务SDK #



** 1. 功能简介 **


该组件是基于Quartz开发的，具有简单却强大的机制，能依据事件间隔来调度任务，也可以按照cron表达式来操作。任务调度引擎和组件客户端SDK分离，可减小应用服务器的负荷。


** 2. 集成说明 **

 (1) 依赖引入
在项目中引入iuap-dispatch-client -1.0.1-SNAPSHOT.jar



** 3. 配置说明** 

  (1)	配置监听Servlet
  如果当前应用需要执行任务，请在web.xml中配置监听servlet类地址

  <servlet>
  	<servlet-name>DispatchClientServlet</servlet-name>
 	<servlet-class>com.yonyou.iuap.dispatch.client.controller.DispatchClientServlet</servlet-class>
  </servlet>

  <servlet-mapping>
  	<servlet-name>DispatchClientServlet</servlet-name>
  	<url-pattern>/dispatchClientServlet</url-pattern>
  </servlet-mapping>

  (2) 服务配置
	在dispatch-client.properties中需要依据实际环境修改” dispatch.server.http.url”和” dispatch.recall.http.url”的值。

** 4 使用示例 **

*(1) 新建任务处理类，需要实现ITask接口*

    import java.util.Map;

    import com.yonyou.iuap.dispatch.client.ITask;

    public class TestTaskImpl implements ITask{
	
	
	public void execute(Map<String,String> data) {
		//业务操作
		
	 }
    }
 
*(2) 新增任务*

	**A、简单类型任务**


     public void addSimpleTask(Date startDate, TimeConfig timeConfig, String note){
		SimpleTaskConfig taskConfig = 
					new SimpleTaskConfig(UUID.randomUUID().toString()/*任务名*/, 
										 "simpleTaskGroup"/*任务组名*/, 
										 timeConfig);
		taskConfig.setStartDate(startDate);
		Map<String,String> params = new HashMap<String,String>();
		params.put("note", note);
		boolean success = DispatchRemoteManager.add(taskConfig, TestTaskImpl.class, params, true);
		if(success){
			System.out.println("任务添加成功--"+taskConfig.getJobCode());
		}else{
			System.out.println("任务添加失败--"+taskConfig.getJobCode());
		}
	}
 
    **B、Cron类型任务**

    public void addCronTask(){
		CronTaskConfig cronTaskConfig = new CronTaskConfig("cronTask", 
														   "cronTaskGroup", 
														   "* */1 * * * ?");
		Map<String,String> params = new HashMap<String,String>();
		params.put("serverName", System.getProperty("os.name"));//任务可传入附加数据
		boolean success = DispatchRemoteManager.add(cronTaskConfig, 
													TestTaskImpl.class, 
													params, 
													true);
		if(success){
			System.out.println("任务添加成功");
		}else{
			System.out.println("任务添加失败");
		}
	}
 
