## 1、断言

```java
@Transactional(rollbackFor = Exception.class)  
@Override  
public boolean saveOrUpdate(T entity) {  
    if (null != entity) {  
        Class<?> cls = entity.getClass();  
        TableInfo tableInfo = TableInfoHelper.getTableInfo(cls);  
        Assert.notNull(tableInfo, "error: can not execute. because can not find cache of TableInfo for entity!");  
        String keyProperty = tableInfo.getKeyProperty();  
        Assert.notEmpty(keyProperty, "error: can not execute. because can not find column for id from entity!");  
        Object idVal = ReflectionKit.getFieldValue(entity, tableInfo.getKeyProperty());  
        return StringUtils.checkValNull(idVal) || Objects.isNull(getById((Serializable) idVal)) ? save(entity) : updateById(entity);  
    }  
    return false;  
}
```
## 2、反射

