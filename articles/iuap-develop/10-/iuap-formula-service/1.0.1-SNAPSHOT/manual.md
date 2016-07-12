

# 公式服务使用说明

## 一 提供的功能说明： 

    1 提供基本类型、数学函数、字符串函数、日期函数等基本功能。
	2 提供自定义函数扩展功能。
	3 本组件提供通过resetAPI的方式直接执行公式的能力
	
## 二 部署说明：

### 1:部署公式服务到web服务器

	下载 iuap-formula-service.war 作为独立应用部署	

## 三 Rest服务调用说明	

	
### 1:公式Rest服务的get调用

	服务接口：
	uap-formula-service/formula/execute4get?formula={"variables":{"d1":3,"d2":4,"i1":6},"formulas":["f1->d1*d2*i1"]}
	
	参数以json 格式传入
	
	返回值格式如下：{"f1":[72]}



### 2:公式Rest服务的post调用

	服务接口：
	/iuap-formula-service/formula/execute
	
	参数格式：
	String param ="{\"formulas\":[\"f1->d1*d2*i1\"],\"variables\":{\"d1\":3,\"d2\":4,\"i1\":6}}";
	
	返回值格式如下：{"f1":[72]}



## 四 其他使用说明

提供哪些公式以及具体公式使用方法，包括自定义函数扩展功能，请参见iuap-formula组件文档说明文档
