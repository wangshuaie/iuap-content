# 元数据持久化组件使用向导 #

## maven依赖 ##

```
	<dependency>
	  <groupId>com.yonyou.iuap</groupId>
	  <artifactId>metadata-jdbc</artifactId>
	  <version>2.0.1-SNAPSHOT</version>
	</dependency>

```

## spring配置示例 ##

```

    <bean id="metadataCache" class="com.yonyou.metadata.mybatis.util.MetadataCache">
        <property name="saasCacheMgr">
            <bean id="saasCacheManager" class="com.yonyou.iuap.cache.SaasCacheManager">
                <property name="cacheManager">
                    <bean class="com.yonyou.iuap.cache.CacheManager">
                        <property name="jedisTemplate" ref="jedisTemplate"/>
                    </bean>
                </property>
            </bean>
        </property>
        <property name="cacheManager">
            <bean class="com.yonyou.iuap.cache.CacheManager">
                <property name="jedisTemplate" ref="jedisTemplate"/>
            </bean>
        </property>
    </bean>
	</beans>

```

## 元数据持久化组件特征 ##

说明，元数据定义相关请参考工具使用手册

### 支持基于元数据定义的单表数据的增删改查 ###

元数据持久化组件的增删改查逻辑to

```

@Service
public class UserService {

    @Autowired
    private MetadataDAO baseDAO;

    @Transactional(rollbackFor = DAOException.class)
    public String save(User user) throws DAOException {
        return baseDAO.insert(user);
    }

    @Transactional(rollbackFor = DAOException.class)
    public String update(User user) throws DAOException {
        return baseDAO.update(user);
    }

    @Transactional(rollbackFor = DAOException.class)
    public String remove(String id) throws DAOException {
        User user = new User();
        user.setId(id);
        return baseDAO.remove(user);
    }

    public String queryById(String id) throws DAOException {
        return baseDAO.queryByPK(User.class,id);
    }

     public Page queryPage(String name,PageRequest pageRequest) throws DAOException {
        SQLParameter parameter = new SQLParameter();
        parameter.addParam(name+"%");
        Page<IpuQuotation> page =
                baseDAO.queryPage("select * from users " + "where name like ?", parameter, pageRequest, IpuQuotation.class);
        return page;
    }
}
```

### 支持基于元数据定义的扩展表数据的增删改查功能  ###

如果元数据定义了数据的扩展，元数据持久化组件的api没有变化，针对于扩展属性，需要调用`setAttribute`方法设置值。

### 支持基于元数据定义的关联关系的查询功能 ###

关联查询功能使用`DASFacade`类来实现，类似代码如下:

```
     String[] paths =
                new String[] {"name", "count", "fk_employee.e_name", "fk_employee.e_code", "fk_bu.b_name",
                        "fk_bu.b_code", "fk_id_oderdt.name", "fk_id_oderdt.to_people", "fk_id_oderdt.from_people",
                        "fk_id_oderdt.ref_org.o_name", "fk_id_oderdt.ref_org.o_code"};
        SQLParameter sqlParameter = new SQLParameter();
        sqlParameter.addParam("1");
        sqlParameter.addParam("2");
        sqlParameter.addParam("3");

        List<ODerDt> dtList =
                metadataDAO.queryForList("select * from t_oddt where od_id in (?,?,?)", sqlParameter,
                        new BeanListMetaProcessor(ODerDt.class));
        Map<String, Map<String, Object>> resultList =
                DASFacade.getAttributeValueAsPKMap(paths, dtList.toArray(new ODerDt[] {}));

```

### 支持基于元数据操作的数据变更通知，默认提供日志记录 ###

元数据持久化组件对于元数据操作接口提供了数据变更通知功能，需要在`MetaDAO`的属性中设置`dataChangeNotifier`属性，组件默认提供了日志记录的功能，如果应用需要自定义扩展，需要实现`IDataChangeListener`接口，并做如下配置

```

 <bean id="dataChangeNotifier" class="com.yonyou.iuap.persistence.bs.notifier.DataChangeNotifier">
        <property name="dataChangeListenerList">
            <list>
                <bean class="xx.xxx.xx.CustomLister"/>
            </list>
        </property>
    </bean>

```

### 支持基于元数据定义，删除主表数据，级联删除子表数据的功能 ###

如果定义了关联关系，删除主表数据，持久化组件会自动删除所有子表数据。

### 支持基于元数据定义，删除数据时，如果数据被引用，不允许删除的功能 ###

如果定义了引用关系，删除数据时，如果该数据被其他数据引用，则不允许删除

### 支持基于元数据定义的应用数据缓存功能 ###

如果元数据定义了缓存属性，该元数据对应的应用数据会被缓存。

### 支持查询应用自定义缓存功能 ###

如果应用希望使用自己的缓存实现，元数据持久化组件提供了 `IMetaCache`接口，应用实现该接口完成自定义缓存的查询功能。

### 支持自定义接口功能 ###

元数据持久化组件支持应用数据自定义接口功能，使用`BizObject`类完成数据和自定义接口转换功能。
