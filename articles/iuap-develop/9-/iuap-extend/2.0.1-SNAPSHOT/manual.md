#JPA持久化扩展组件#
##简介##
本组件主要是对Spring data JPA和iuap-persistence组件中的JPA部分做扩展，扩展了查询条件、数据库方言识别、和事务的添加范围等功能。如果使用默认的Spring data JPA设置没有扩展的需求，可以不依赖此组件。

##配置和使用方式##
**1:如果需要扩展JPA对事务的控制，在工程上依赖组件**

    根据项目的需求，排除冲突的版本，需要调整exclusions.
    <dependency>
        <groupId>com.yonyou.iuap</groupId>
        <artifactId>iuap-extend</artifactId>
        <version>${iuap.modules.version}</version>
        <exclusions>
            <exclusion>
                <artifactId>guava</artifactId>
                <groupId>com.google.guava</groupId>
            </exclusion>
            <exclusion>
                <artifactId>cglib</artifactId>
                <groupId>cglib</groupId>
            </exclusion>
        </exclusions>
    </dependency>


**2:在对应的spring配置文件中，调整如下配置**

    <!-- Spring Data Jpa配置 -->
    <jpa:repositories base-package="com.yonyou.iuap.repository"
                      transaction-manager-ref="transactionManager"
                      factory-class="org.springframework.data.jpa.repository.support.CustomJpaRepositoryFactoryBean"
                      entity-manager-factory-ref="entityManagerFactory"/>
	
**3:在服务调用需要控制事务的方法上，使用Spring的事务控制**

**4:其他详细配置和参数，请参考示例工程和官网文档**