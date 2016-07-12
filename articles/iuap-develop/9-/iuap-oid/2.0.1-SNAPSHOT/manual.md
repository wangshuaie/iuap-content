# 对象OID组件 #

## 组件简介 ##
iuap-oid组件提供对实体唯一标识生成的统一封装，包含普通的UUID、基于Redis的自增ID、和snowflake、uapoid算法等。组件提供统一接口对OID进行生成，通过配置的方式实现对几种策略的切换。同时支持对自定义ID生成方案的扩展。

采用Redis自增方式，支持为不同模块设置不同的起始值。

## 配置和使用方式 ##
**1:在工程的classpath中加入对oid组件的依赖**
	<dependency>
		<groupId>com.yonyou.iuap</groupId>
		<artifactId>iuap-oid</artifactId>
		<version>2.0.1-SNAPSHOT</version>
	</dependency>

**2:在属性配置文件中，加入oid的使用类型配置**

	#idtype=uuid
	#idtype=redis
	#idtype=snowflake
	idtype=uapoid
	#idproviderclass=com.yonyou.iuap.persistence.oid.CustomIdProvider

	//idtype为需要使用的ID生成类型，目前包括UUID、redis自增、snowflake、uapoid几种类型

**3:使用redis自增主键时候，需要配置redis对应的文件，请参考cache组件**

	<bean id="redisPool" class="com.yonyou.iuap.cache.redis.RedisPoolFactory"
		scope="prototype" factory-method="createJedisPool">
		<constructor-arg value="${redis.url}" />
	</bean>
	
	<bean id="jedisTemplate" class="org.springside.modules.nosql.redis.JedisTemplate">
		<constructor-arg ref="redisPool"></constructor-arg>
	</bean> 

	//属性文件
	redis.url=direct://172.20.6.48:6379?poolSize=50&poolName=mypool

	idtype=redis
	//设置特殊模块自增起始值
	IUAP_PRIMARY_MYMODULE_START_VALUE=10000

**4:使用uapoid类型时候，连接数据库，并初始化数据表，stepSize可根据项目指定，为每次取到本地JVM中自增ID的范围**

 	<bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
		<property name="dataSource" ref="dataSource"></property>
        </bean>	
    
        <bean id="uapOidJdbcService" class="com.yonyou.iuap.persistence.oid.UapOidJdbcService">
    	    <!--生产态配置项目中使用的jdbcTemplate和Datasource即可-->
    	    <property name="jdbcTemplate" ref="jdbcTemplate" />
    	    <property name="stepSize" value="5000" />
        </bean>

	注意:建表和初始化sql语句请参考示例工程，最终生成的为20位的字符串，前八位为用户指定的schema名。

**5:代码调用ID生成示例**

	//参数根据选择的不同ID生成类型意义不同，请根据项目需求判断，其中redis的为自增模块号，uapoid为对应的数据库的schema名
	IDGenerator.generateObjectID(param);
	
**6:更多API操作和配置方式，请参考对应的示例工程(DevTool/examples/example_iuap_oid)**
