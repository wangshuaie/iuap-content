
<style>
   p {
   color:#fff;
   background-color: #999999; 
   padding: 20px;}
   code {
  		 color:#00688B;
   }
   ul li{
   		color:#00688B;
   		
   }
   li{
   		color:#00688B;
   		
   }
</style>
 **附件系统服务说明文档**

***
一 提供的功能说明： 

	1 提供附件的增、删、改、查、覆盖等基本功能。
	2 提供两种附件存储方式-阿里云、FastDFS。
	3 提供缩略图功能。
	4 提供直传功能。
	5 提供图片链接直接预览功能。
	6 提供CORS跨域访问能力
	7 提供基本js
***
二 配置说明：

1.application.properties：用户中心、阿里云、FastDFS 配置文件


    #用户中心得地址
    redis.session.url=direct://ip:端口?poolSize=pool大小&poolName=pool名称
    #超时时间
	sessionTimeout=3600
	#是否登录时候剔除当前用户在其他位置的登录,默认为不互踢
	sessionMutex=false
	#auth组件需要的
	context.name=/
	#系统id
	sysid=
    #----------------------------------------------------------------------------------------------
	#使用FastDfs文件系统时Fdfs系统的配置******
	storeType=FastDfs
	connect_timeout = 10
	network_timeout = 30
	charset = ISO8859-1
	tracker_server = ip:端口
	fdfsread_server = ip
	#----------------------------------------------------------------------------------------------
	#aliyun 采用阿里的文件系统配置（上面存在一个storeType，不用的需要注释掉）
	storeDir=/etc/filetest
	storeType=AliOss
	endpoint=oss-cn-beijing.aliyuncs.com
	#accessKeyId 是阿里附件系统提供的账户信息
	accessKeyId=keyid
	accessKeySecret=eIrMJETD8djWy6c25zAtiieddjaBjC
	#回调的服务器地址，使用直传的时候必须配置
	callbackTarget=http://ip:端口
    #回调方法:一般不用修改，开发人员扩展使用
	callbackUrl=/file/rewrite
	callbackBody=filename=${object}&bucket=${bucket}&size=${size}&groupname=${x:groupname}&filepath=${x:filepath}&permission=${x:permission}&modular=${x:modular}


2.iuapfile.properties：阿里云bucket信息配置文件

    #私有bucket
    defaultBucket=阿里文件存储的私有账户
    #可读bucket==公有
    defaultBucketRead=阿里文件存储的公有账户（read权限账户）

         
3.applicationContext.xml：属性文件加载配置，主要是数据库属性文件和阿里云用户中心文件的加载

    <?xml version="1.0" encoding="UTF-8"?>
	<beans xmlns="http://www.springframework.org/schema/beans" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:context="http://www.springframework.org/schema/context" xmlns:jdbc="http://www.springframework.org/schema/jdbc"  
	xmlns:aop="http://www.springframework.org/schema/aop" xmlns:jee="http://www.springframework.org/schema/jee" 
	xmlns:tx="http://www.springframework.org/schema/tx" xmlns:jpa="http://www.springframework.org/schema/data/jpa" 
	xmlns:mongo="http://www.springframework.org/schema/data/mongo" 
	xsi:schemaLocation="
		http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.0.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.0.xsd
		http://www.springframework.org/schema/jdbc http://www.springframework.org/schema/jdbc/spring-jdbc-4.0.xsd
		http://www.springframework.org/schema/jee http://www.springframework.org/schema/jee/spring-jee-4.0.xsd
		http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-4.0.xsd
		http://www.springframework.org/schema/data/jpa http://www.springframework.org/schema/data/jpa/spring-jpa-1.3.xsd
		http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.0.xsd
	    http://www.springframework.org/schema/data/mongo http://www.springframework.org/schema/data/mongo/spring-mongo-1.0.xsd"
        default-lazy-init="true">

	<description>Spring公共配置 </description>
	
	<bean id="propertyConfigurer" class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer">
        <property name="locations">
            <list>
                 <!--加载的两个属性文件的路径和名称-->
                <value>classpath:application.properties</value>
                <value>classpath:jdbc.properties</value>
            </list>
        </property>
        <property name="systemPropertiesMode" value="#{T(org.springframework.beans.factory.config.PropertyPlaceholderConfigurer).SYSTEM_PROPERTIES_MODE_OVERRIDE}"/>
    </bean>
    
    <!-- 使用annotation 自动注册bean, 并保证@Required、@Autowired的属性被注入 -->
	<context:component-scan base-package="com.yonyou.iuap">
		<context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
		<context:exclude-filter type="annotation" expression="org.springframework.web.bind.annotation.ControllerAdvice"/>
	</context:component-scan>
	
	</beans>        


4.jdbc.properties：数据库配置文件
     
    #驱动
    jdbc.driver=com.mysql.jdbc.Driver
    #url地址
	jdbc.url=jdbc:mysql://localhost:3306/ssm
    #数据库用户
	jdbc.username=mysql
	#数据库密码
	jdbc.password=mysql
	#定义初始连接数
	jdbc.initialSize=1
	#定义最大连接数
	jdbc.maxActive=20
	#定义最大空闲
	jdbc.maxIdle=20
	#定义最小空闲
	jdbc.minIdle=1
	#定义最长等待时间
	jdbc.maxWait=60000
	
	
	

5.fileserver-spring-mybatis.xml：MYbatis配置文件一般不用修改，配合jdbc.properties文件使用

		<?xml version="1.0" encoding="UTF-8"?>
		<beans xmlns="http://www.springframework.org/schema/beans"
			xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
			xmlns:p="http://www.springframework.org/schema/p"
			xmlns:context="http://www.springframework.org/schema/context"
			xmlns:mvc="http://www.springframework.org/schema/mvc"
			xmlns:tx="http://www.springframework.org/schema/tx"
			xmlns:aop="http://www.springframework.org/schema/aop"
			xsi:schemaLocation="http://www.springframework.org/schema/beans  
		                        http://www.springframework.org/schema/beans/spring-beans-3.1.xsd  
		                        http://www.springframework.org/schema/context  
		                        http://www.springframework.org/schema/context/spring-context-3.1.xsd  
		                        http://www.springframework.org/schema/mvc  
		                        http://www.springframework.org/schema/mvc/spring-mvc-4.0.xsd 
		                        http://www.springframework.org/schema/tx
		                        http://www.springframework.org/schema/tx/spring-tx-3.1.xsd
		                        http://www.springframework.org/schema/aop
		                        http://www.springframework.org/schema/aop/spring-aop-3.1.xsd">              
			<!-- 自动扫描 -->
			<context:component-scan base-package="filesystem"/>
		
			<bean id="dataSource" class="org.apache.commons.dbcp.BasicDataSource"
				destroy-method="close">
				<property name="driverClassName" value="${jdbc.driver}" />
				<property name="url" value="${jdbc.url}" />
				<property name="username" value="${jdbc.username}" />
				<property name="password" value="${jdbc.password}" />
				<!-- 初始化连接大小 -->
				<property name="initialSize" value="${jdbc.initialSize}"></property>
				<!-- 连接池最大数量 -->
				<property name="maxActive" value="${jdbc.maxActive}"></property>
				<!-- 连接池最大空闲 -->
				<property name="maxIdle" value="${jdbc.maxIdle}"></property>
				<!-- 连接池最小空闲 -->
				<property name="minIdle" value="${jdbc.minIdle}"></property>
				<!-- 获取连接最大等待时间 -->
				<property name="maxWait" value="${jdbc.maxWait}"></property>
			</bean>
		
			<!-- spring和MyBatis完美整合，不需要mybatis的配置映射文件 -->
			<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
				<property name="dataSource" ref="dataSource" />
				<!-- 自动扫描mapping.xml文件 -->
				<property name="mapperLocations" value="classpath:AttachVOMapper.xml"></property>
			</bean>
		
			<!-- DAO接口所在包名，Spring会自动查找其下的类 -->
			<bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
				<property name="basePackage" value="filesystem.idao" />
				<property name="sqlSessionFactoryBeanName" value="sqlSessionFactory"></property>
			</bean>
		
			<!-- (事务管理)transaction manager, use JtaTransactionManager for global tx -->
			<bean id="transactionManager"
				class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
				<property name="dataSource" ref="dataSource" />
			</bean>
			<tx:annotation-driven transaction-manager="transactionManager" proxy-target-class="true"/>
			</beans>




6.applicationContext-shiro.xm：用户中心得auth组件配置文件。要保证其他应用的auth组件配置和附件服务的auth组件配置保持一致。（具体应用可以参考auth组件说明）
	
	<?xml version="1.0" encoding="UTF-8"?>
	<beans xmlns="http://www.springframework.org/schema/beans"
		xmlns:util="http://www.springframework.org/schema/util" xmlns:aop="http://www.springframework.org/schema/aop"
		xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
		xsi:schemaLocation="
	       http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
	       http://www.springframework.org/schema/util http://www.springframework.org/schema/util/spring-util.xsd
	       http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd">
	
		<bean id="statelessRealm" class="com.yonyou.iuap.auth.shiro.StatelessRealm">
			<property name="cachingEnabled" value="false" />
		</bean>
	
		<!-- Subject工厂 -->
		<bean id="subjectFactory"
			class="com.yonyou.iuap.auth.shiro.StatelessDefaultSubjectFactory" />
		<bean id="webTokenProcessor" class="com.yonyou.iuap.auth.token.DefaultTokenPorcessor">
			<property name="id" value="web"></property>
			<!-- 
			<property name="domain" value="www.yonyou.com"></property> 
			-->
			<property name="path" value="${context.name}"></property> 
			<property name="expr" value="3600"></property>
			<property name="exacts">
				<list>
				     <!--这里容易出问题，这里的配置必须和其他部署环境的配置一致-->
					<value type="java.lang.String">tenantid</value>
				</list>
			</property>
		</bean>
		<bean id="maTokenProcessor" class="com.yonyou.iuap.auth.token.DefaultTokenPorcessor">
			<property name="id" value="ma"></property>
			<!-- 
			<property name="domain" value="www.yonyou.com"></property> 
			-->
			<property name="path" value="${context.name}"></property> 
			<property name="expr" value="-1"></property>
			<property name="exacts">
				<list>
				    <!--这里容易出问题，这里的配置必须和其他部署环境的配置一致-->
					<value type="java.lang.String">tenantid</value>
				</list>
			</property>
		</bean>	
		<bean id="tokenFactory" class="com.yonyou.iuap.auth.token.TokenFactory">
			<property name="processors">
				<list>
					<ref bean="webTokenProcessor" />
					<ref bean="maTokenProcessor" />
				</list>
			</property>
		</bean>
		<!-- 会话管理器 -->
		<bean id="sessionManager" class="org.apache.shiro.session.mgt.DefaultSessionManager">
			<property name="sessionValidationSchedulerEnabled" value="false" />
		</bean>
		<!-- 安全管理器 -->
		<bean id="securityManager" class="org.apache.shiro.web.mgt.DefaultWebSecurityManager">
			<property name="realms">
				<list>
					<ref bean="statelessRealm" />
				</list>
			</property>
			<property name="subjectDAO.sessionStorageEvaluator.sessionStorageEnabled"
				value="false" />
			<property name="subjectFactory" ref="subjectFactory" />
			<property name="sessionManager" ref="sessionManager" />
		</bean>
		<!-- 相当于调用SecurityUtils.setSecurityManager(securityManager) -->
		<bean
			class="org.springframework.beans.factory.config.MethodInvokingFactoryBean">
			<property name="staticMethod"
				value="org.apache.shiro.SecurityUtils.setSecurityManager" />
			<property name="arguments" ref="securityManager" />
		</bean>
		<bean id="statelessAuthcFilter" class="com.yonyou.iuap.auth.shiro.StatelessAuthcFilter">
			<property name="sysid" value="${sysid}" />
			<property name="tokenFactory" ref="tokenFactory" />
		</bean>
		<bean id="logout" class="com.yonyou.iuap.auth.shiro.LogoutFilter"></bean>
		<!-- Shiro的Web过滤器 -->
		<bean id="shiroFilter" class="org.apache.shiro.spring.web.ShiroFilterFactoryBean">
			<property name="securityManager" ref="securityManager" />
			<property name="loginUrl" value="/login" />
			<property name="successUrl" value="/" />
			<property name="filters">
				<util:map>
					<entry key="statelessAuthc" value-ref="statelessAuthcFilter" />
				</util:map>
			</property>
			<property name="filterChainDefinitions">
				<value>
					/file/** = statelessAuthc
					/jsp/** = anon
				</value>
			</property>
		</bean>
		<!-- Shiro生命周期处理器 -->
		<bean id="lifecycleBeanPostProcessor" class="org.apache.shiro.spring.LifecycleBeanPostProcessor" />
		<bean id="sessionJedisPool" class="com.yonyou.iuap.cache.redis.RedisPoolFactory" scope="prototype" factory-method="createJedisPool">
			<constructor-arg value="${redis.session.url}" />
		</bean>    
	    <bean id="sessionMgr" class="com.yonyou.iuap.auth.session.SessionManager">
	        <property name="sessionJedisPool" ref="sessionJedisPool"/>
	        <property name="sessionMutex" value="${sessionMutex}"/>
	    </bean>	
	</beans>



7.pub_filesystem.sql：建表sql语句，需要在数据库里执行的sql。
	 
	 CREATE TABLE `pub_filesystem` (
	  `id` varchar(36) NOT NULL ,
	  `pkfile` varchar(100) NOT NULL,
	  `filename` varchar(100) NOT NULL,
	  `filepath` varchar(100) NOT NULL,
	  `filesize` varchar(100) NOT NULL,
	  `groupname` varchar(100) NOT NULL,
	   `permission` varchar(20) NOT NULL,
	  `uploader` varchar(36),
	  `uploadtime` varchar(100),
	  `sysid` varchar(100),
	  `tenant` varchar(100),
	  `modular` varchar(100),
	   `url` varchar(1000),
	  PRIMARY KEY (`id`)
	  ) ENGINE=InnoDB AUTO_INCREMENT=35 DEFAULT CHARSET=utf8;


***
三.示例工程说明：
       
 1.文件说明：
 
      index.jsp ---增删改查、直传等等代码操作在其中都有例子，并且例子中存在部分说明信息，请一定要查看此文件。
      ossupload.js ---直传的js文件
      ajaxfileupload.js ---附件上传插件js,不支持跨域。跨域模式在index.jsp中存在例子
      interface.file.js ---ajax访问方式 接口文件，里面是需要实现的方法
      interface.file.impl.js ---是interface.file.js的实现，包括所有的基本js
      【以上js文件使用的时候需要同时引用这4个js,顺序为ossupload.js、ajaxfileupload.js、interface.file.js、interface.file.impl.js】
		
 2.扩展说明：
 
     1.线程绑定变量参数的截取器 :fileserver-spring-mvc.xml文件中下面代码为拦截器配置代码。
       当你进行上传等基本操作的时候，拦截器会将cookies中的tenantid、usercode、userid、sysid
       写到线程变量中，供后台使用这些公共信息
		<mvc:interceptors>  
	      <!-- session超时 -->  
	      <mvc:interceptor>  
	        <mvc:mapping path="/*/*"/>  
	        <bean class="com.yonyou.iuap.generic.adapter.CookiesInterceptor">
	          <property name="exclude">  
	            <list>  
	              <!-- 如果请求中包含以下路径，则不进行拦截 -->  
	              <value>/login</value>  
	              <value>/js</value>  
	              <value>/css</value>  
	              <value>/image</value>  
	              <value>/images</value>  
	            </list>  
	          </property>  
	        </bean>  
	      </mvc:interceptor>  
	    </mvc:interceptors> 
		
	 2.CORS跨域框架:在web 中添加了下面的代码，cors使用方式自己参考网上api,可自己按需求修改
		 <filter>
	    <filter-name>CORS</filter-name>
	    <filter-class>com.thetransactioncompany.cors.CORSFilter</filter-class>
	    <init-param>
	      <param-name>cors.allowOrigin</param-name>
	      <param-value>*</param-value>
	    </init-param>
	    <init-param>
	      <param-name>cors.supportedMethods</param-name>
	      <param-value>GET, POST, HEAD, PUT, DELETE</param-value>
	    </init-param>
	    <init-param>
	      <param-name>cors.supportedHeaders</param-name>
	      <param-value>Accept, Origin, X-Requested-With, Content-Type, Last-Modified</param-value>
	    </init-param>
	    <init-param>
	      <param-name>cors.exposedHeaders</param-name>
	      <param-value>Set-Cookie</param-value>
	    </init-param>
	    <init-param>
	      <param-name>cors.supportsCredentials</param-name>
	      <param-value>true</param-value>
	    </init-param>
	  </filter>
	  
	  3.iuap-filesystem.jar包的使用，此包是附件系统的核心包。所有的核心代码都在里面（配置文件不在其中）
	  	<dependency>
			  <groupId>com.yonyou.iuap</groupId>
			  <artifactId>iuap-filesystem</artifactId>
			  <version>1.0.0-SNAPSHOT</version>
		</dependency>
	  
		
四.代码示例说明：

   	第一步：导入js文件
		<script type="text/javascript" src="<%=request.getContextPath()%>/resources/js/ossupload.js"></script>
		<script type="text/javascript" src="<%=request.getContextPath()%>/resources/js/ajaxfileupload.js"></script>
		<script type="text/javascript" src="<%=request.getContextPath()%>/resources/js/interface.file.js"></script>
		<script type="text/javascript" src="<%=request.getContextPath()%>/resources/js/interface.file.impl.js"></script>
		
		
    第二步：基本操作代码编写
	1.回调函数：以下只是例子
		/** * 回调函数--返回结果*/
		 var callback = function(data){
			 if(-1 == data.status){//后台校验信息状态
				 var warn = "";
				 for(var k in  data.message){
					 data.message[k].ObjectName    //校验的对象名称
					 data.message[k].Field         //校验的字段名称
					 data.message[k].RejectedValue //校验的错误原因		 
					 data.message[k].DefaultMessage; //校验的错误提示信息
					 warn +=data.message[k].ObjectName+"对象的"+ data.message[k].Field+"属性不能为："+data.message[k].RejectedValue +"\n"; //自己拼的方式
					 warn +=data.message[k].DefaultMessage +"\n"//后台的默认信息
					 
					 /*这只是例子，具体验证信息想如何展示自己处理  */
				 }
				 alert(warn);
		
			 }else if(1 == data.status){//上传成功状态
				 
				 alert(JSON.stringify(data))
				 /* 自己看data处理 */
				 
			 }else if(0 == data.status){//上传失败状态
				 /* 自己看data处理 */
				   alert(JSON.stringify(data))
			 }else{//error 或者加載js錯誤
				 /* 自己看着处理 */
				  alert(data);
			 }
 	};
 	
 	
 	2.基本方法：使用添加附件举例
 		/*上传附件-异步--不支持跨顶级域名--采用ajaxfileupload的原因*/
		function upload(){
			 var par = { 
					 fileElementId: "uploadbatch_id",  //【必填】文件上传空间的id属性  <input type="file" id="id_file" name="file" />,可以修改，主要看你使用的 id是什么 
					 filepath: "code",   //【必填】单据相关的唯一标示，一般包含单据ID，如果有多个附件的时候由业务自己制定规则
					 groupname: "single",//【必填】分組名称,未来会提供树节点
					 permission: "read", //【选填】 read是可读=公有     private=私有     当这个参数不传的时候会默认private
					 url: true,          //【选填】是否返回附件的连接地址，并且会存储到数据库
					 thumbnail :  "500w",//【选填】缩略图--可调节大小，和url参数配合使用，不会存储到数据库
					 //cross_url : "http://localhost:8080/iuap-filesystem-service/" //【选填】跨iuap-saas-fileservice-base 时候必填
					 isreplace : false,
					 }
			 var f = new interface_file();
			 f.filesystem_upload(par,callback);//callback是上面定义的回调函数
		 }
		 
		 
	3.页面编写：下面是例子
		 <input type="file" name="addfile" id = "uploadbatch_id" multiple="multiple"/>
		 <input type="button" value="上传" onclick="upload()">
	 	