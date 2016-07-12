# 分布式锁组件 #

## 组件简介 ##
iuap-lock组件是利用Zookeeper的强一致特性，提供的分布式锁功能，解决了多JVM中对共享资源加锁解锁的需求。本组件支持对共享资源的互斥锁的加锁解锁、线程内锁重入、批量加解锁等功能。支持对Zookeeper的单机和集群方式连接。

## 配置和使用方式 ##
**1:在属性文件中，配置分布式锁组件使用的Zookeeper的连接url和方式，支持单机方式、集群方式**
	
	//配置锁组件连接zookeeper时候使用的连接类型，single、cluster
	zklock.connection.type=cluster
	zklock.url.single=127.0.0.1:2181
	zklock.url.cluster=172.20.12.20:2180,172.20.12.20:2181,172.20.12.20:2182

**2:Spring的配置文件中，声明iuap-lock中连接池建立的配置信息的bean,根据项目需要调整参数值**

	<bean id="zkPoolConfig" class="org.apache.commons.pool2.impl.GenericObjectPoolConfig">
		<property name="maxTotal" value="100"/>
		<property name="maxIdle" value="20"/>
		<property name="maxWaitMillis" value="60000"/>
		
		<property name="timeBetweenEvictionRunsMillis" value="60000"/>
		<property name="numTestsPerEvictionRun" value="-1"/>
		<property name="minEvictableIdleTimeMillis" value="30000"/>
	</bean>

**3:工程中引入对iuap-lock组件的依赖**

	<dependency>
		<groupId>com.yonyou.iuap</groupId>
		<artifactId>iuap-lock</artifactId>
		<version>2.0.1-SNAPSHOT</version>
	</dependency>	

**4:代码中调用组件提供的API，操作分布式锁的加锁和解锁，完成业务**

	@Test
	public void testDisLockCodeExample() throws InterruptedException {
		String lockpath = "iuapdislockpath_test";
		boolean islocked = false;
		try {
			islocked = DistributedLock.lock(lockpath);
			if(islocked){
				System.out.println("加锁2成功，业务处理!");
			}
		} catch (Exception e) {
			System.out.println("加锁2操作异常!");
		} finally {
			if(islocked){
				DistributedLock.unlock(lockpath);
				System.out.println("解锁2成功!");
			} 
		}
	}

**5:更多API操作和配置方式，请参考分布式锁对应的示例工程(DevTool/examples/example_iuap_lock)**