# Mybatis持久化组件 #

## 简介 ##

iuap使用iuap-mybatis作为MyBatis持久化的支持。iuap-mybatis提供了统一的Spring扫描注解和分页插件。

在iuap-persistence组件中已经对mybatis的基础使用做了说明，iuap-persistence中依赖了基础的spring data jpa和mybatis相关，如果项目上明确使用Mybatis，则不需要引入iuap-persistence组件，直接依赖iuap-mybatis组件即可，注意要和iuap其他组件的版本保持一致。

## 分页支持说明 ##

该插件目前支持以下数据库的<b>物理分页</b>:

 1. `Oracle`
 2. `Mysql`

配置`dialect`属性时，可以使用小写形式：

`oracle`,`mysql`

## Mybatis版本支持 ##

分页插件支持MyBatis3.2.0~3.3.0(包含)


# 使用说明 #

## Maven依赖配置 ##

	<dependency>
	    <groupId>com.yonyou.iuap</groupId>
	    <artifactId>iuap-mybatis</artifactId>
	    <version>${iuap.modules.version}</version>
	</dependency>

## 典型配置 ##

    <!-- 使用annotation定义事务 -->
    <aop:aspectj-autoproxy/>

	<!-- 单独使用mybatis时候，可以使用DataSourceTransactionManager -->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource"/>
    </bean>

    <tx:annotation-driven transaction-manager="transactionManager"/>

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

## 版本迁移 ##

如果您正在使用其他分页插件，想迁移到iuap-mybatis分页插件，iuap-mybatis为您提供了方便的迁移方案。

- 查询条件转换接口 `RequestConvertor`
  应用通过该接口实现类，将自定义的查询条件转换为iuap-mybatis插件的分页条件，包括数据获取区间，排序条件等等。
  

	    <property name="requestConvertor">
	        <bean class="xxx.xxx.xxxRequestConvertor"/>
	    </property>


- 查询结果转换接口 `ResultConvertor`
  应用通过实现该接口，将iuap-mybatis的返回结果PageResult转换为应用自己的page对象。
 
         <property name="resultConvertor">
             <bean class="xxx.xxx.xxxResultConvertor"/>
         </property>
         
## 示例工程说明 ##

开发工具包中包含对iuap-mybatis组件的使用示例(DevTool/examples/example-iuap-mybatis),请参考示例工程中的具体配置和使用示例。