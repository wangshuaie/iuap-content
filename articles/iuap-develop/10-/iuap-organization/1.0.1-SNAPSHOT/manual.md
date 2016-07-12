#组织组件使用说明

##一，简要说明
组织组件主要是提供组织的核心模型和基本服务，支持通过组织职能的方式对组织进行扩展。


##二，使用方法

1，	引入组织组件jar包：

    <dependency>
        <groupId>com.yonyou.iuap</groupId>
        <artifactId>iuap-organization</artifactId>
        <version>0.0.1-SNAPSHOT</version>
    </dependency>

2，	配置spring文件，参考示例工程：organization-applicationContext.xml

3，字段结构如下：
#####（1）组织表
<table>
   <tr>
      <td>名称</td>
      <td>代码</td>
      <td>注释</td>
   </tr>
   <tr>
      <td>组织Id</td>
      <td>id</td>
      <td></td>
   </tr>
   <tr>
      <td>组织编码</td>
      <td>code</td>
      <td></td>
   </tr>
   <tr>
      <td>组织名称</td>
      <td>name</td>
      <td></td>
   </tr>
   <tr>
      <td>上级组织Id</td>
      <td>fatherid</td>
      <td></td>
   </tr>
   <tr>
      <td>组织描述</td>
      <td>description</td>
      <td>对组织的简要描述</td>
   </tr>
   <tr>
      <td>租户Id</td>
      <td>tenantid</td>
      <td></td>
   </tr>
   <tr>
      <td>应用系统Id</td>
      <td>sysid</td>
      <td></td>
   </tr>
   <tr>
      <td>内部编码</td>
      <td>innercode</td>
      <td>构建编码树，用于查询某个组织的所有下级</td>
   </tr>
   <tr>
      <td>组织类型一</td>
      <td>orgtype1</td>
      <td>组织类型，目前是十个，支持扩展</td>
   </tr>
   <tr>
      <td>组织类型二</td>
      <td>orgtype2</td>
      <td></td>
   </tr>
   <tr>
      <td>组织类型三</td>
      <td>orgtype3</td>
      <td></td>
   </tr>
   <tr>
      <td>组织类型四</td>
      <td>orgtype4</td>
      <td></td>
   </tr>
   <tr>
      <td>组织类型五</td>
      <td>orgtype5</td>
      <td></td>
   </tr>
   <tr>
      <td>组织类型六</td>
      <td>orgtype6</td>
      <td></td>
   </tr>
   <tr>
      <td>组织类型七</td>
      <td>orgtype7</td>
      <td></td>
   </tr>
   <tr>
      <td>组织类型八</td>
      <td>orgtype8</td>
      <td></td>
   </tr>
   <tr>
      <td>组织类型九</td>
      <td>orgtype9</td>
      <td></td>
   </tr>
   <tr>
      <td>组织类型十</td>
      <td>orgtype10</td>
      <td></td>
   </tr>
</table>
 
#####（2）组织类型表
<table>
   <tr>
      <td>名称</td>
      <td>代码</td>
      <td>注释</td>
   </tr>
   <tr>
      <td>组织类型id</td>
      <td>id</td>
      <td></td>
   </tr>
   <tr>
      <td>组织类型编码</td>
      <td>code</td>
      <td></td>
   </tr>
   <tr>
      <td>组织类型名称</td>
      <td>name</td>
      <td></td>
   </tr>
   <tr>
      <td>租户id</td>
      <td>tenantid</td>
      <td></td>
   </tr>
   <tr>
      <td>应用系统id</td>
      <td>sysid</td>
      <td></td>
   </tr>
   <tr>
      <td>组织类型的字段名</td>
      <td>fieldname</td>
      <td>字段名代码的是组织的类型，如orgtype1</td>
   </tr>
   <tr>
      <td>组织类型的服务类</td>
      <td>serviceclass</td>
      <td>每个组织类型扩展一张表，这个字段用于注册服务类</td>
   </tr>
   <tr>
      <td>组织类型的实体类</td>
      <td>entityclassname</td>
      <td>每个组织类型的实体类，若是组织类型为公司，就是公司的实体类</td>
   </tr>
</table>
注：基本数据库表见jar包中resource目录下的sql>mysql。

4，服务接口类

（1）IOrgService，主要提供组织相关的接口：

    public interface IOrgService {
	
		public void save(OrgEntity orgEntity);

		public void deleteById(String id);
	
		public OrgEntity queryById(String id);
	
		/**
	 	* 具有某种组织类型的组织
	 	* @param orgtype  传入字符串类似为orgtype1
		 * @return
		 */
		public List<OrgEntity> queryOrgsByOrgType(String orgtype);
	
		/**
		 * 查询具有某几种组织类型的组织
		 * @param orgtypeArray  传入参数为例如new String[]{"orgtype1","orgtype2",...}
	 	* @return
	 	*/
		public List<OrgEntity> queryOrgsByOrgTypeArray(String[] orgtypeArray);

		public OrgEntity queryParentOrg(String id);

		public List<OrgEntity> queryChildrenOrgs(String id);
	
		public List<OrgEntity> queryAllOrgs();
	
		public List<OrgEntity> queryOrgsByIds(String[] ids);
	
		public List<OrgEntity> queryOrgsByIdList(List<String> idList);
    }


（2）IOrgTypeService，主要提供组织职能的相关接口：

    public interface IOrgTypeService {

	    public void save(OrgTypeEntity orgTypeEntity);
		
	    public void deleteById(String id);
	
	    public OrgTypeEntity queryOrgTypebyId(String id);
	
	    public OrgTypeEntity queryOrgTypeByCode(String code);
	
	    public OrgTypeEntity queryOrgTypeByName(String name);
	
		public OrgTypeEntity queryOrgTypeByFieldName(String fieldname);
	
		public OrgTypeEntity queryOrgTypeByServiceClassName(String serviceclass);
	
		public OrgTypeEntity queryOrgTypeByEntityClassName(String entityclassname);
	
		public List<OrgTypeEntity> queryOrgTypesByIds(String[] ids);

		public OrgTypeEntity[] queryOrgTypeByOrg(OrgEntity orgEntity); 

		public OrgTypeEntity[] queryOrgTypeByOrgID(String orgId);
		
		/**
		 *判断该OrgEntity中的对应orgtype是否被选中
	 	* @param orgEntity
	 	* @param orgtypeId
		 * @return
	 	*/
		public boolean isTypeOf(OrgEntity orgEntity, String orgtypeId);
	
		/**
		 * 判断该orgId对应的OrgEntity中的对应orgtype是否被选中
		 * @param orgId
		 * @param orgtypeId
		 * @return
		 */
		public boolean isTypeOfByOrgId(String orgId, String orgtypeId);
	
    }
# 扩展开发说明
## 1.扩展组织基本信息：
## 2.组织职能扩展:


