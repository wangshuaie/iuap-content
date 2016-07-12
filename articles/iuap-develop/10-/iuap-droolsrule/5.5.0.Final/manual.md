# 规则引擎 #

## 功能简介 
 
Drools规则引擎是通过规则文件的配置实现动态的业务需求，比如某购物商场中根据消费积分的多少来划分用户等级，随着业务变更，划分用户等级的消费积分的多少会有所变更，以前是1000积分就是银牌会员，现在要消费5000积分才能成为银牌会员。使用Drools只需修改规则文件，即可实现业务变化，无须更改代码。
Drools通过普通的java bean（专业术语称Fact）与规则进行相互传参和传值，规则文件中的规则可以对当前的对象进行任何的读写操作，调用该对象提供的方法等。
## 使用方法 ##
结合示例工程（根据消费积分的多少来划分用户等级）的代码介绍Drools的基本使用方法
### 定义实体类 ###
参见例子工程中的com.toolkit.drools.bean.User类，包括属性和一些set get方法：

		/** 
		*用户积分
	 	*/
		private long credit;
		/**
		*用户等级
	 	*/
		private int rank;

### 定义drl规则文件 ###
参见例子工程rank-rule.drl文件：

	package com.sample
	import com.toolkit.drools.bean.User;
	// 钻石会员,积分大于50000
	rule "RANK_DIAMOND"
	no-loop  true
	lock-on-active true
	activation-group "user rank"
	
    when
        $user : User( credit >= 50000 ) 
    then
		$user.setRank(99);	
	end

规则文件包括三部分，package，import，rule。

1. package 
规则文件的逻辑区分，必须定义且放在规则文件第一行。
2. import  
导入规则文件需要使用到的外部变量，可以是一个类，也可以是类中的某一个可访问的静态方法 	
3. rule
定义一个规则，包括三部分：  
 - 属性部分：规则名称，是否重复执行，规则触发的优先级、组设置，过期时间、生效时间等。
 - 条件部分：定义当前规则的条件，when...... then......。
 - 结果部分： 当前规则条件满足后执行的操作，可以直接调用Fact对象的方法来操作应用。
 
### 定义外部调用接口和实现 ###
外部接口和实现中调用Drools规则引擎将drl配置文件和Fact实体解析出来，并提供给java程序直接调用的功能方法。
参见示例工程中
com.toolkit.drools.UserRule
com.toolkit.drools.UserRuleImpl类中的public int getUserRank(long credit)方法。

  
