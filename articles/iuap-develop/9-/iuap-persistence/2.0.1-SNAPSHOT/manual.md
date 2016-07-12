# 持久化组件 #

## 组件简介 ##
本组件提供对通用持久化功能的集成，包括Spring data JPA、Mybatis、基础的Spring jdbc等，同时集成数据库连接池Apache tomcat-jdbc。

工程搭建时，可以引入iuap-persistence组件，快速的对持久化层进行集成，使用示例工程中提供的Spring配置文件模板，对工程的持久化方案进行筛选。

同时，iuap-persistence组件也是后续的动态数据源组件的基础。

## 配置和使用方式 ##
**1.maven工程配置对持久化组件的依赖**

        <dependency>
            <groupId>com.yonyou.iuap</groupId>
            <artifactId>iuap-persistence</artifactId>
            <version>2.0.1-SNAPSHOT</version>
        </dependency>

**2.spring配置文件中，增加对连接池、数据源的依赖**

    <!-- 数据源配置, 使用Tomcat JDBC连接池 -->
    <bean id="dataSource" class="org.apache.tomcat.jdbc.pool.DataSource" destroy-method="close">
        <!-- Connection Info -->
        <property name="driverClassName" value="${jdbc.driver}"/>
        <property name="url" value="${jdbc.url}"/>
        <property name="username" value="${jdbc.username}"/>
        <property name="password" value="${jdbc.password}"/>

        <!-- Connection Pooling Info -->
        <property name="maxActive" value="${jdbc.pool.maxActive}"/>
        <property name="maxIdle" value="${jdbc.pool.maxIdle}"/>
        <property name="minIdle" value="0"/>
        <property name="maxWait" value="${jdbc.pool.maxWait}"/>
        <property name="defaultAutoCommit" value="true"/>

        <property name="minEvictableIdleTimeMillis" value="${jdbc.pool.minEvictableIdleTimeMillis}"/>
        <property name="removeAbandoned" value="${jdbc.pool.removeAbandoned}"/>
        <property name="removeAbandonedTimeout" value="${jdbc.pool.removeAbandonedTimeout}"/>
    </bean>

**3.如果使用JPA方式进行持久化，spring配置文件中，增加JPA的配置**

    <!-- Jpa Entity Manager 配置 -->
    <bean id="entityManagerFactory" class="org.springframework.orm.jpa.LocalContainerEntityManagerFactoryBean">
        <property name="dataSource" ref="dataSource"/>
        <property name="jpaVendorAdapter" ref="hibernateJpaVendorAdapter"/>
        <property name="packagesToScan" value="com.yonyou.iuap.entity"/>
        <property name="jpaProperties">
            <props>
                <!-- 命名规则 My_NAME->MyName -->
                <prop key="hibernate.ejb.naming_strategy">org.hibernate.cfg.ImprovedNamingStrategy</prop>
                <prop key="hibernate.show_sql">true</prop>
            </props>
        </property>
    </bean>

    <bean id="hibernateJpaVendorAdapter" class="org.springframework.orm.jpa.vendor.HibernateJpaVendorAdapter">
        <property name="databasePlatform">
            <bean factory-method="getDialect" class="org.springside.modules.persistence.Hibernates">
                <constructor-arg ref="dataSource"/>
            </bean>
        </property>
    </bean>

    <!-- Jpa 事务配置 -->
    <bean id="transactionManager" class="org.springframework.orm.jpa.JpaTransactionManager">
        <property name="entityManagerFactory" ref="entityManagerFactory"/>
    </bean>

    <!-- Spring Data Jpa配置 -->
    <jpa:repositories base-package="com.yonyou.iuap.repository"
                      transaction-manager-ref="transactionManager"
                      entity-manager-factory-ref="entityManagerFactory"/>

**4.如果项目中使用Mybatis方式进行持久化，增加Mybatis的配置**

    <!-- MyBatis配置 -->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="dataSource"/>
        <!-- 自动扫描entity目录, 省掉Configuration.xml里的手工配置 -->
        <property name="typeAliasesPackage" value="com.yonyou.iuap.entity"/>
        <!-- 显式指定Mapper文件位置 -->
        <property name="mapperLocations" value="classpath:/mybatis/**/*Mapper.xml"/>
        <property name="plugins">
            <array>
                <bean id="paginationInterceptor"
                      class="com.yonyou.iuap.persistence.mybatis.plugins.PaginationInterceptor">
                    <property name="properties">
                        <props>
                            <prop key="dbms">mysql</prop>
                            <prop key="sqlRegex">.*retrievePage.*</prop>
                        </props>
                    </property>
                </bean>
            </array>
        </property>
    </bean>
    <!-- 扫描basePackage下所有以@MyBatisRepository标识的 接口-->
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <property name="basePackage" value="com.yonyou.iuap.repository"/>
        <property name="annotationClass" value="com.yonyou.iuap.persistence.mybatis.anotation.MyBatisRepository"/>
        <property name="sqlSessionFactoryBeanName" value="sqlSessionFactory"/>
    </bean>

**5.增加对事务的控制**

    <!-- 使用annotation定义事务 -->
    <aop:aspectj-autoproxy/>

    <tx:annotation-driven transaction-manager="transactionManager"/>

    <!-- 注意！！！单独使用mybatis时候事务配置可以采用DataSourceTransactionManager,如果和jpa一起使用，配置JpaTransactionManager -->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource"/>
    </bean>

**6.JPA方式接口示例**

	public interface DemoEntityJpaDao extends PagingAndSortingRepository<DemoEntity, String> , JpaSpecificationExecutor<DemoEntity>

**7.Mybatis方式接口示例**

	@MyBatisRepository
	public interface IpuQuotationMapper extends PageMapper<IpuQuotation> {

	    int deleteByPrimaryKey(String id);

	    int insert(IpuQuotation record);

	    IpuQuotation selectByPrimaryKey(String id);

	    IpuQuotation selectChildrenByPrimaryKey(String id);

	    int updateByPrimaryKeySelective(IpuQuotation record);

	    int updateByPrimaryKey(IpuQuotation record);

	    PageResult<IpuQuotation> retrievePage(PageRequest pageRequest, @Param("subject") String subject);
	}


**8.对应service的业务方法上，增加事务注解**

	@Transactional
	public int complexQuotationOperate(IpuQuotation quotation){

	//事务的传播机制根据用户的需求来配置，如新启子事务
	@Transactional(propagation=Propagation.REQUIRES_NEW)
	
**9:更多API操作和配置方式，请参考对应的示例工程(DevTool/examples/example_iuap_persistence)**
