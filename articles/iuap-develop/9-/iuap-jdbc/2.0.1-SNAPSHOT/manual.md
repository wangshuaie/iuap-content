# 多数据库持久化组件介绍 #

iUAP平台采用iuap-jdbc作为数据持久化中间件，实现对对业务数据的持久化服务。

本组件对jdbc做了轻量封装，采用基于注解的ORM映射机制，简化代码配置。
使用简单，面向对象的方式编程，直接操作POJO，少量代码完成持久化操作。
组件能够自动识别数据源，提供不同数据库的基本语法转换的能力，做到一套语法，多种数据库兼容执行，目前支持MySQL，Oracle,PostgreSQL。

## Maven依赖 ##
	<dependency>
	  <groupId>com.yonyou.iuap</groupId>
	  <artifactId>iuap-jdbc</artifactId>
	  <version>2.0.1-SNAPSHOT</version>
	</dependency>

## 第一个例子
使用iuap-jdbc完成单表的增删改查。示例包括实体类、DAO类、Service类。其中dao类通过注入BaseDao类，并使用它对数据进行操作。

- user数据库表

		drop table if exists users;
		 
		create table users (
		  id char(36),
		  name varchar(20),
		  ts datetime,
		  dr int
		);


- 生成User.java对象

		@Entity(name = "User",namespace="your.package.name")
		@Table(name = "users")
		public class User extends BaseEntity{
	
		    @Id
		    @Column(name = "id")
		    @GeneratedValue(strategy = Stragegy.UUID, moudle = "users")
		    private String id;
		    
		    @Column(name = "name")
		    private String name;
		
		    @Column(name="ts")
		    private Date ts;
		   
		    @Column(name="dr")
		    private Integer dr;
		    
		    //省略getter,setter...
		    
		    public String getMetaDefinedName() {
		        return "User";
		    }
		    
		    public String getNamespace() {
		        return "your.package.name";
		    }
		}

-  在Spring中声明数据源和BaseDAO

    
	    <!-- 通用配置文件加载 -->
	    <context:property-placeholder system-properties-mode="OVERRIDE" ignore-unresolvable="true" location="classpath*:/application.properties"/>
	
	    <!-- 数据源配置, 使用Tomcat JDBC连接池 -->
	    <bean id="jdbcDataSource" class="org.apache.tomcat.jdbc.pool.DataSource" destroy-method="close">
	        <!-- Connection Info -->
	        <property name="driverClassName" value="${jdbc.driver}"/>
	        <property name="url" value="${jdbc.url}"/>
	        <property name="username" value="${jdbc.username}"/>
	        <property name="password" value="${jdbc.password}"/>
	        <property name="defaultCatalog" value="${jdbc.catalog}"/>
	
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
	    
	    <!-- iuap跨数据库数据库装饰类 -->
	    <bean id="crossDBDataSource" class="com.yonyou.iuap.persistence.bs.framework.ds.CrossdbDataSource">
	        <constructor-arg name="dataSource" ref="jdbcDataSource"/>
	    </bean>
	
	    <!-- spring 事务配置 -->
	    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
	        <property name="dataSource" ref="crossDBDataSource"/>
	    </bean>
	
	    <!-- 使用annotation定义事务 -->
	    <tx:annotation-driven transaction-manager="transactionManager" proxy-target-class="true"/>
	
	    <!-- 声明spring-jdbcTemplate -->
	    <bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
	        <property name="dataSource" ref="crossDBDataSource"></property>
	    </bean>
	   
	    <!-- 声明BaseDAO -->
	    <bean id="baseDAO" class="com.yonyou.iuap.persistence.bs.dao.BaseDAO">
	        <property name="jdbcTemplate" ref="jdbcTemplate"/>
	        <property name="dbMetaHelper" ref="dbMetaInfo"/>
	    </bean>
	
	    <!-- 声明数据源辅助类 -->
	    <bean id="dbMetaInfo" class="com.yonyou.iuap.persistence.bs.util.DBMetaHelper">
	        <property name="jdbcTemplate" ref="jdbcTemplate"/>
	    </bean>

- 推荐新建一个实体类User的DAO

        @Repository
    	public class UserDao {
    	
    		//BaseDAO是iuap-jdbc的基础dao类，该类中包含了一些通用的数据库操作
    		@Autowired
   			private BaseDAO dao;
    	
    		public User queryByID(String id) throws DAOException {
    			return dao.queryByPK(User.class, id);
    		}
    		
    		public void save(User user) throws DAOException {
    			dao.insert(user);
    		}
    		
    		public void remove(User user) throws DAOException {
    			dao.remove(user);
    		}
    	
    		public void remove(List<User> users) throws DAOException {
    			dao.remove(users);
    		}
    		
    		public String update(User user) throws DAOException {
    			return dao.update(user);
    		}
    	
    	
    		public Page queryPage(String name,PageRequest pageRequest) throws 	DAOException {
       			SQLParameter parameter = new SQLParameter();
       			parameter.addParam(name+"%");
       			Page<User> page =dao.queryPage("select * from users " + "where name like ?", parameter, pageRequest, User.class);
       			return page;
    		}
    	}

- 创建实体类User的Service


		@Service
		public class UserService {
	
		    @Autowired
		    private UserDao userDao;
		
		    @Transactional(rollbackFor = DAOException.class)
		    public String save(User user) throws DAOException {
		        return userDao.save(user);
		    }
		
		    @Transactional(rollbackFor = DAOException.class)
		    public String update(User user) throws DAOException {
		        return userDao.update(user);
		    }
		    
		    @Transactional(rollbackFor = DAOException.class)
		    public String remove(String id) throws DAOException {
		        User user = new User();
		        user.setId(id);
		        return userDao.remove(user);
		    }
		    
		    public String queryById(String id) throws DAOException {
		        return userDao.queryByPK(User.class,id);
		    }
		    
		     public Page queryPage(String name,PageRequest pageRequest) throws DAOException {
		        return userDao.queryPage(name,pageRequest);
	   		}
		}


## 多数据库持久化组件特征 ##

### 多数据库语法适配
目前组件适配了`mysql`,`oracle`和`postgresql`的通用语法。

### 数据源 ###

多数据库持久化组件使用CrossdbDataSource装饰外部数据源，应用的数据源可以自由配置，组件在完成数据源的适配的同时，为多数据库的语法适配能力做铺垫。

### 自动修改时间戳 ###

细心的朋友可能发现了，在代码中并没有操作`User`对象的`ts`属性，但是每次修改操作ts属性都会变成服务器的当前时间。
组件的**sql增强功能**会自动在增加或修改的时候，在`sql`上增加`ts`字段，使得应用更加专注于业务相关的逻辑。

### 自动分页 ###

组件提供分页API，应用只需要传入查询条件sql，不必关心分页逻辑，组件自动帮您完成分页。详见`BaseDAO`的API `queryPage()`

### 主子表组合保存，修改 ###

组件提供主子表组合操作。API对应`BaseDAO`的`save()`,入参为一主多子。
**注意**
使用这个方法的时候，Entity的status属性必须设置。

status说明如下：


	status值        说明 
	0               本条记录未变更|
	1               本条记录被修改|
	2               本条记录为新增数据|
	3               本条记录被删除(主表数据状态不能为删除状态)

  
### 查询结果解析器 ###

组件提供了多种查询结果解析器。解析器类名以及说明如下：

<table style="border-collapse:collapse">
	<tr><th>解析器类名</th><th>说明</th></tr>
	<tr><td>BeanProcessor</td><td>值对象集合处理器，返回一个ArrayList集合，集合中的每一个元素是一个javaBean,每个javaBean对应结果集合中一行数据</td></tr>
	<tr><td>BeanListProcessor</td><td>值对象集合处理器，返回一个ArrayList集合，集合中的每一个元素是一个javaBean,每个javaBean对应结果集合中一行数据</td></tr>
	<tr><td>ColumnProcessor</td><td>列值处理器，返回一个Java对象，结果集中只有一行数据，该对象对应与结果集中某一列的值，该处理器通过结果集列的序号或名称来确定列</td></tr>
	<tr><td>ColumnListProcessor</td><td>列值处理器，返回一个ArrayList对象，结果集中有多行数据，该对象对应与结果集中某一列的值，该处理器通过结果集列的序号或名称来确定列</td></tr>
	<tr><td>MapProcessor</td><td>HashMap处理器，返回一个HashMap, 结果集中只有一行数据，其中结果集合中每一列的列名和列值对应HashMap的一个关键字和相应的值</td></tr>
    <tr><td>MapListProcessor</td><td>HashMap集合处理器，返回一个ArrayList集合，集合中的每一个元素是一个HashMap，每个HashMap对应结果集中的一行数据,其中结果集合中每一列的列名和列值对应HashMap的一个关键字和相应的值</td></tr>

    <tr><td>ArrayProcessor</td><td>数组处理器，返回一个对象数组，结果集中只有一行数据，其中结果集中每一列对应数组的一个元素</td></tr>
    <tr><td>ArrayListProcessor</td><td>数组集合处理器，返回一个ArrayList集合，集合中的每一个元素是一个数组，每个数组对应结果集中的一行数据，其中结果集中每一列对应数组的一个元素</td></tr>
</table>


### 函数转换对应表 ###

- `mysql`

<table style="border-collapse:collapse">
	<thead>
		<tr>
			<th>iuap-jdbc函数</th>
			<th>mysql函数</th>
		</tr>
	</thead>
	<tbody>
		<tr>
			<td>PI()</td>
			<td>3.1415926535897931</td>
		</tr>
		<tr>
			<td>%(模运算) a % b</td>
			<td>mod(a,b)</td>
		</tr>
		<tr>
			<td>getdate()</td>
			<td>to_char(now(),'yyyy-mm-dd hh24:mi:ss')</td>
		</tr>
		<tr>
			<td>square</td>
			<td>power(num,2)</td>
		</tr>
		<tr>
			<td>&amp;&amp; (a&amp;&amp;b&amp;&amp;c)</td>
			<td>, (a,b,c)</td>
		</tr>
		<tr>
			<td>top n</td>
			<td>select * from table limit n</td>
		</tr>
		<tr>
			<td>patindex('pattern',column)</td>
			<td>instr('pattern',column)</td>
		</tr>
		<tr>
			<td>dateadd(dateType,num,date)</td>
			<td>参数为类型(year,month,day)，数值，时间</td>
		</tr>
		<tr>
			<td>dateadd(yy/yyyy/year,num,date)</td>
			<td>DATE_ADD( date_format(date,'%Y-%m-%d') , INTERVAL num YEAR)</td>
		</tr>
		<tr>
			<td>dateadd(mm/m/month,num,date)</td>
			<td>DATE_ADD( date_format('date','%Y-%m-%d') , INTERVAL num MONTH)</td>
		</tr>
		<tr>
			<td>dateadd(other,num,date)</td>
			<td>DATE_ADD( date_format(date,'%Y-%m-%d') , INTERVAL num DAY)</td>
		</tr>
		<tr>
			<td>datediff(dateType,start,end)</td>
			<td>参数类型(同上)，开始，结束</td>
		</tr>
		<tr>
			<td>datediff(year/yyyy/yy,start,end)</td>
			<td>PERIOD_DIFF(date_format(start,'%Y'),date_format(end,'%Y'))</td>
		</tr>
		<tr>
			<td>datediff(m/mm/month,start,end)</td>
			<td>PERIOD_DIFF(date_format(start,'%Y%m'),date_format(end,'%Y%m'))</td>
		</tr>
		<tr>
			<td>datediff(year/yyyy/yy,start,end)</td>
			<td>PERIOD_DIFF(date_format(start,'%Y%m%d'),date_format(end,'%Y%m%d'))</td>
		</tr>
		<tr>
			<td>case when then else end</td>
			<td>语法支持，未处理</td>
		</tr>
		<tr>
			<td>select column as b from table as alias</td>
			<td>select column b from table alias</td>
		</tr>
		<tr>
			<td>isnull</td>
			<td>ifnull</td>
		</tr>
		<tr>
			<td>len</td>
			<td>length</td>
		</tr>
		<tr>
			<td>count ( * )</td>
			<td>count(*)</td>
		</tr>
		<tr>
			<td>ltrim_case</td>
			<td>ltrim</td>
		</tr>
		<tr>
			<td>rtrim_case</td>
			<td>rtrim</td>
		</tr>
		<tr>
			<td>left(str,n)</td>
			<td>语法支持，未处理</td>
		</tr>
		<tr>
			<td>right(str,n)</td>
			<td>语法支持，未处理</td>
		</tr>
		<tr>
			<td>coalesce(xx,xxx...,notnull)</td>
			<td>语法支持，未处理</td>
		</tr>
		<tr>
			<td>round(str)</td>
			<td>语法支持，未处理</td>
		</tr>
		<tr>
			<td>convert(日期类型,xxx)</td>
			<td>语法支持，未处理</td>
		</tr>
		<tr>
			<td>cast</td>
			<td>语法支持，未处理</td>
		</tr>
	</tbody>
</table>

- `oracle`

<table style="border-collapse:collapse">
	<thead>
		<tr>
			<th>iuap-jdbc函数</th>
			<th>oracle函数</th>
		</tr>
	</thead>
	<tbody>
		<tr>
			<td>PI()</td>
			<td>3.1415926535897931</td>
		</tr>
		<tr>
			<td>%(模运算) a % b</td>
			<td>mod(a,b)</td>
		</tr>
		<tr>
			<td>getdate()</td>
			<td>sysdate</td>
		</tr>
		<tr>
			<td>&amp;&amp; (a&amp;&amp;b&amp;&amp;c)</td>
			<td>, (a,b,c)</td>
		</tr>
		<tr>
			<td>top n</td>
			<td>where rownum&lt;n+1</td>
		</tr>
		<tr>
			<td>case when then else end</td>
			<td>decode()</td>
		</tr>
		<tr>
			<td>select column as b from table as alias</td>
			<td>select column b from table alias</td>
		</tr>
		<tr>
			<td>left(str,n)</td>
			<td>substr(str,1,n)</td>
		</tr>
		<tr>
			<td>right(str,n)</td>
			<td>substr(str,length(str)-n+1,n)</td>
		</tr>
		<tr>
			<td>coalesce(xx,xxx...,notnull)</td>
			<td>nvl()嵌套 nvl(nvl(1,2),3)</td>
		</tr>
		<tr>
			<td>square</td>
			<td>power(num,2)</td>
		</tr>
		<tr>
			<td>patindex('pattern',column)</td>
			<td>instr ( column, 'pattern', 1, 1 )</td>
		</tr>
		<tr>
			<td>len(str)</td>
			<td>length(rtrim(str,' '))</td>
		</tr>
		<tr>
			<td>ltrim(str)</td>
			<td>ltrim(str,' ')</td>
		</tr>
		<tr>
			<td>rtrim(str)</td>
			<td>rtrim(str,' ')</td>
		</tr>
		<tr>
			<td>round(str)</td>
			<td>rtrim(str,' ')</td>
		</tr>
		<tr>
			<td>cast(日期类型,xxx)</td>
			<td>to_date(xxx,\'yyyy-mm-dd hh24:mi:ss')</td>
		</tr>
		<tr>
			<td>cast(char(len),xxx)</td>
			<td>substr(to_char(xxx),1,len)</td>
		</tr>
		<tr>
			<td>cast(char,xxx)</td>
			<td>to_char(xxx)</td>
		</tr>
		<tr>
			<td>cast(其他类型,xxx)</td>
			<td>cast(xxx as 其他类型)</td>
		</tr>
		<tr>
			<td>convert()</td>
			<td>同cast</td>
		</tr>
		<tr>
			<td>getdate()</td>
			<td>sysdate</td>
		</tr>
		<tr>
			<td>dateadd(dateType,num,date)</td>
			<td>参数为类型(year,month,day)，数值，时间</td>
		</tr>
		<tr>
			<td>dateadd中的date</td>
			<td>to_date(substr(date,1,10),'yyyy-mm-dd')</td>
		</tr>
		<tr>
			<td>dateadd(yy/yyyy/year,num,date)</td>
			<td>add_months( to_date(substr(date,1,10),'yyyy-mm-dd'), (num*12))</td>
		</tr>
		<tr>
			<td>dateadd(mm/m/month,num,date)</td>
			<td>add_months( to_date(substr(date,1,10),'yyyy-mm-dd'), num)</td>
		</tr>
		<tr>
			<td>dateadd(other,num,date)</td>
			<td>to_date(substr(date,1,10),'yyyy-mm-dd')+num</td>
		</tr>
		<tr>
			<td>datediff(dateType,start,end)</td>
			<td>参数类型(同上)，开始，结束</td>
		</tr>
		<tr>
			<td>datediff(year/yyyy/yy,start,end)</td>
			<td>extract(year from to_date(substr(end,1,10),'yyyy-mm-dd')) - extract(year from to_date(substr(start,1,10),'yyyy-mm-dd'))</td>
		</tr>
		<tr>
			<td>datediff(m/mm/month,start,end)</td>
			<td>months_between( to_date(substr(end,1,10),'yyyy-mm-dd'), to_date(substr(start,1,10),'yyyy-mm-dd'))</td>
		</tr>
		<tr>
			<td>datediff(year/yyyy/yy,start,end)</td>
			<td>(to_date(substr(end,1,10),'yyyy-mm-dd')-to_date(substr(start,1,10),'yyyy-mm-dd'))</td>
		</tr>
		<tr>
			<td>substring</td>
			<td>substring(xxx,start,end)</td>
		</tr>
		<tr>
			<td>substring</td>
			<td>substr</td>
		</tr>
		<tr>
			<td>isnull</td>
			<td>nvl</td>
		</tr>
		<tr>
			<td>ceiling</td>
			<td>ceil</td>
		</tr>
		<tr>
			<td>ltrim_case</td>
			<td>ltrim</td>
		</tr>
		<tr>
			<td>rtrim_case</td>
			<td>rtrim</td>
		</tr>
	</tbody>
</table>

- `postgresql`

<table style="border-collapse:collapse">
	<thead>
		<tr>
			<th>iuap-jdbc函数</th>
			<th>pg函数</th>
		</tr>
	</thead>
	<tbody>
		<tr>
			<td>PI()</td>
			<td>3.1415926535897931</td>
		</tr>
		<tr>
			<td>%(模运算) a % b</td>
			<td>mod(a,b)</td>
		</tr>
		<tr>
			<td>getdate()</td>
			<td>to_char(now(),'yyyy-mm-dd hh24:mi:ss')</td>
		</tr>
		<tr>
			<td>&amp;&amp; (a&amp;&amp;b&amp;&amp;c)</td>
			<td>, (a,b,c)</td>
		</tr>
		<tr>
			<td>top n</td>
			<td>select * from table limit n</td>
		</tr>
		<tr>
			<td>case when then else end</td>
			<td>decode()</td>
		</tr>
		<tr>
			<td>select column as b from table as alias</td>
			<td>select column b from table alias</td>
		</tr>
		<tr>
			<td>left(str,n)</td>
			<td>substring(str,1,n)</td>
		</tr>
		<tr>
			<td>right(str,n)</td>
			<td>substring(str,length(str)-n+1,n)</td>
		</tr>
		<tr>
			<td>coalesce(xx,xxx...,notnull)</td>
			<td>nvl()嵌套 nvl(nvl(1,2),3)</td>
		</tr>
		<tr>
			<td>square</td>
			<td>power(num,2)</td>
		</tr>
		<tr>
			<td>patindex('pattern',column)</td>
			<td>position ( 'pattern' in clumn )</td>
		</tr>
		<tr>
			<td>len(str)</td>
			<td>length(rtrim(str,' '))</td>
		</tr>
		<tr>
			<td>round(str)</td>
			<td>rtrim(str,' ')</td>
		</tr>
		<tr>
			<td>convert(日期类型,xxx)</td>
			<td>to_date(xxx,'yyyy-mm-dd')</td>
		</tr>
		<tr>
			<td>convert(char(len),xxx)</td>
			<td>substring(to_char(xxx),1,len)</td>
		</tr>
		<tr>
			<td>convert(char,xxx)</td>
			<td>to_char(xxx)</td>
		</tr>
		<tr>
			<td>convert(其他类型,xxx)</td>
			<td>cast(xxx as 其他类型)</td>
		</tr>
		<tr>
			<td>getdate()</td>
			<td>to_char(now(),\'yyyy-mm-dd hh24:mi:ss')</td>
		</tr>
		<tr>
			<td>dateadd(dateType,num,date)</td>
			<td>参数为类型(year,month,day)，数值，时间</td>
		</tr>
		<tr>
			<td>dateadd(yy/yyyy/year,num,date)</td>
			<td>( to_date(to_char(date,\'yyyy-mm-dd'), 'yyyy-mm-dd') + interval 'n years')</td>
		</tr>
		<tr>
			<td>dateadd(mm/m/month,num,date)</td>
			<td>( to_date(to_char(date,'yyyy-mm-dd'), 'yyyy-mm-dd') + interval 'n months')</td>
		</tr>
		<tr>
			<td>dateadd(other,num,date)</td>
			<td>( to_date(to_char(date,'yyyy-mm-dd'), 'yyyy-mm-dd') + interval 'n days')</td>
		</tr>
		<tr>
			<td>datediff(dateType,start,end)</td>
			<td>参数类型(同上)，开始，结束</td>
		</tr>
		<tr>
			<td>datediff(year/yyyy/yy,start,end)</td>
			<td>extract(year from to_date(substring(end,1,10), 'yyyy-mm-dd')) - extract(year from to_date(substring(start,1,10), 'yyyy-mm-dd'))</td>
		</tr>
		<tr>
			<td>datediff(m/mm/month,start,end)</td>
			<td>extract(year from age(to_date(substring(end,1,10), 'yyyy-mm-dd'),to_date(substring(start,1,10), 'yyyy-mm-dd'))) * 12 + extract(month from age(to_date(substring(end,1,10), 'yyyy-mm-dd'),to_date(substring(start,1,10), 'yyyy-mm-dd')))</td>
		</tr>
		<tr>
			<td>datediff(year/yyyy/yy,start,end)</td>
			<td>(to_date(substring(end,1,10), 'yyyy-mm-dd')-to_date(substring(start,1,10), 'yyyy-mm-dd'))</td>
		</tr>
		<tr>
			<td>isnull</td>
			<td>coalesce</td>
		</tr>
		<tr>
			<td>len</td>
			<td>length</td>
		</tr>
		<tr>
			<td>ltrim_case</td>
			<td>ltrim</td>
		</tr>
		<tr>
			<td>rtrim_case</td>
			<td>rtrim</td>
		</tr>
	</tbody>
</table>

**更多详细的使用方法，请参考示例工程(DevTool/examples/example-iuap-jdbc)**
