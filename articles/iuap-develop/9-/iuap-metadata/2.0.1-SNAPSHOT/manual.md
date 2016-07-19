**1:在属性文件中，配置redis的连接url和数据库连接属性**

	示例：
	
	redis.url=direct://localhost:6379?poolName=mypool&poolSize=100
	jdbc.driver=com.mysql.jdbc.Driver
	jdbc.url=jdbc:mysql://localhost:3306/tenant01?useUnicode=true&characterEncoding=utf-8
	jdbc.catalog=tenant01
	jdbc.username=root
	jdbc.password=1234

	

**2:Spring的配置文件中，调整如下配置**

	
	<context:component-scan base-package="com.yonyou.metadata.mybatis.service">
		<context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
	</context:component-scan>

	<bean id="saasCacheManager" class="com.yonyou.iuap.cache.SaasCacheManager">
		<property name="cacheManager" ref="cacheManager" />
	</bean>
	<bean id="metadataCache" class="com.yonyou.metadata.mybatis.util.MetadataCache">
		<property name="saasCacheMgr" ref="saasCacheManager" />
		<property name="cacheManager" ref="cacheManager" />
	</bean>	

**3:工程中引入对iuap-metadata组件的依赖**

	<dependency>
		<groupId>com.yonyou.metadata</groupId>
		<artifactId>com.yonyou.metadata.impl.mybatis</artifactId>
		<version>0.0.3-SNAPSHOT</version>
	</dependency>
	<dependency>
		<groupId>com.yonyou.metadata</groupId>
		<artifactId>com.yonyou.metadata.spi</artifactId>
		<version>0.0.3-SNAPSHOT</version>
	</dependency>	

**4:代码中调用组件提供的API，操作元数据**

	/**
	 * 测试通过组件ID获取名称空间
	 * 
	 * @throws Exception
	 */
	@Test
	 public void testgetNameSpaceByCompID() throws Exception {

    	InvocationInfoProxy.setTenantid("publishtest");
   		AbstractMetadataService service = (AbstractMetadataService) ServiceFinder.findMetadataService();
   		String en = service.getNameSpaceByCompID("05ab4927-03de-4fd0-b118-6f65df6378d0", null);
   		Assert.assertNotNull(en);

  }

**5:更多API操作和配置方式，请参考缓存对应的示例工程**