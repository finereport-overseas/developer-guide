# Beginner Tutorial - Filter

## Introduction
This provider is to provide DAO for FineDB, the built-in database of FineReport. With this, we can store our data generated during the plugin lifecycle into the database.
```java
package com.fr.db.fun;
   
import com.fr.plugin.db.accessor.DBAccessible;
import com.fr.stable.fun.mark.Mutable;
   
/**
 * For a plugin to register custom tables
 */
public interface DBAccessProvider extends Mutable, DBAccessible {
   
    String XML_TAG = "DBAccessProvider";
   
    int CURRENT_LEVEL = 1;
}
```
The methods of *DBAccessProvider* are inherited from *DBAccessible* and *DBInjectable*:
```java
package com.fr.plugin.db.accessor;
  
import com.fr.stable.db.dao.DAOProvider;
  
public interface DBAccessible extends DBInjectable {
    DAOProvider[] registerDAO();
}
```
```java
package com.fr.plugin.db.accessor;
  
import com.fr.stable.db.accessor.DBAccessor;
  
public interface DBInjectable {
    void onDBAvailable(DBAccessor dbAccessor);
}
```
DBAccessProvider#registerDAO: to register custom DAO, the main method to be focused on.

DBAccessProvider#onDBAvailable：to do some initialization with *DBAccessor*.

To register the provider:
```xml
<extra-core>
    <DBAccessProvider class="com.fr.plugin.dao.DemoDBAccessProvider"/>
</extra-core>
```

## Register a table and DAO
The *resigterDAO* method mentioned above returns an array of *DAOProvider*. Below is *DAOProvider*:
```java
package com.fr.stable.db.dao;
   
import com.fr.stable.db.entity.BaseEntity;
   
public interface DAOProvider<T extends BaseEntity> {
   
    Class<T> getEntityClass();
   
    Class<? extends BaseDAO<T>> getDAOClass();
}
```
DAOProvider#getEntityClass: to get an entity that describes the structure of a table.

DAOProvider#getDAOClass: to get a class that implements the detailed data operation to a table.

### Entity
Declare the structure of a table with annotation. In most cases, we extend *BaseEntity*, which already includes the 'id' column, to add our columns.

@Entity: to declare the class as an entity.

@Table: to declare the table mapped with this clas. The attribute 'name' is to set the table name.

@Column: to declare the mapping between the property and the field.

Commonly used attributes:

| Attribute | Description | Default |
| ---- | ---- | ---- |
| name | the field name | empty |
| unique | whether the field value must be unique | false |
| nullable | whether the null value is acceptable | true |
| length | the maximum length of the value | 255 |
| precision | the number of digits when the field type is Number | 0 |
| scale | the number of decimals when the field type is Number | 0 |

Here is a demo for an Entity:
```java
package com.fr.plugin.dao;
  
import com.fr.stable.db.data.DataRecord;
import com.fr.stable.db.entity.BaseEntity;
import com.fr.third.javax.persistence.Column;
import com.fr.third.javax.persistence.Entity;
import com.fr.third.javax.persistence.Table;
  
@Entity
@Table(name = "fine_plugin_demo")
public class DemoEntity extends BaseEntity {
    @Column(name="name")
    private String name;
    @Column(name="value")
    private String value;
    @Column(name="num",precision = 10,scale = 2)
    private double num;
  
    public void setName(String name) {
        this.name = name;
    }
  
    public void setValue(String value) {
        this.value = value;
    }
  
    public String getName() {
        return name;
    }
  
    public String getValue() {
        return value;
    }
  
    public double getNum() {
        return num;
    }
    public void setNum(double num) {
        this.num = num;
    }
}
```

### DAO
```java
package com.fr.plugin.dao;
  
import com.fr.stable.db.dao.BaseDAO;
import com.fr.stable.db.session.DAOSession;
  
public class DemoDao extends BaseDAO<DemoEntity> {
    public DemoDao(DAOSession daoSession) {
        super(daoSession);
    }
  
    @Override
    protected Class<DemoEntity> getEntityClass() {
        return DemoEntity.class;
    }
}
```

### Register the table and DAO defined
```java
@Override
public DAOProvider[] registerDAO() {
    return new DAOProvider[]{new DAOProvider() {
        @Override
        public Class getEntityClass() {
            return DemoEntity.class;
        }
        @Override
        public Class<? extends BaseDAO> getDAOClass() {
            return DemoDao.class;
        }
    }};
}
```

## Database Access Implementation
For now, we have implemented the registration of our table and DAO. The next thing is to define how to access the table and do CRUD.

First, let's look at the defined *DBAccessProvider*:
```java
public class DemoDBAccessProvider extends AbstractDBAccessProvider {
    private static DBAccessor dbAccessor = null;
    public static DBAccessor getDbAccessor() {
        return dbAccessor;
    }
    @Override
    public DAOProvider[] registerDAO() {
        return new DAOProvider[]{new DAOProvider() {
            @Override
            public Class getEntityClass() {
                return DemoEntity.class;
            }
            @Override
            public Class<? extends BaseDAO> getDAOClass() {
                return DemoDao.class;
            }
        }};
    }
  
    @Override
    public void onDBAvailable(DBAccessor dbAccessor) {
        DemoDBAccessProvider.dbAccessor=dbAccessor;
    }
}
```
Generally, we use *DBAccessor* to do access the database and do data operation. The methods are:

*runQueryAction*: to query data (without transaction).

*runDMLAction*: to add/delete/update data (with transaction).

The arguments for these methods are *DBAction*:
```java
package com.fr.stable.db.action;
  
import com.fr.stable.db.dao.DAOContext;
  
public interface DBAction<T> {
    T run(DAOContext daoContext) throws Exception;
}
```

### Data Query
This is a demo of how to query data from the table defined in *DemoEntity*:
```java
try {
    List<DemoEntity> values  =  DemoDBAccessProvider.getDbAccessor().runQueryAction(new DBAction<List<DemoEntity>>() {
        @Override
        public List<DemoEntity> run(DAOContext daoContext) throws Exception {
            daoContext.getDAO(DemoDao.class).find(QueryFactory.create().addRestriction(RestrictionFactory.eq("name", "Joe")));
            return null;
        }
    });
} catch (Exception e) {
    e.printStackTrace();
}
```
The argument of *find()* is of *QueryCondition* type. We can define the query condition or restriction with it.
```java
QueryCondition queryCondition= QueryFactory.create();
queryCondition.setCount(1);
queryCondition.setSkip(1);
queryCondition.addRestriction(RestrictionFactory.eq("num",1111.45));
queryCondition.addGroupBy("id");
queryCondition.addSort("name");
```

### Data Modification
Add record:
```java
try {
      DemoDBAccessProvider.getDbAccessor().runDMLAction(new DBAction<DemoEntity>() {
        @Override
        public DemoEntity run(DAOContext daoContext) throws Exception {
            DemoEntity entity=new DemoEntity();
            entity.setId(UUID.randomUUID().toString());
            entity.setName("Joe");
            entity.setNum(123);
            daoContext.getDAO(DemoDao.class).add(entity);
            return null;
        }
    });
} catch (Exception e) {
    e.printStackTrace();
}
```
Delete record:
```java
try {
    DemoDBAccessProvider.getDbAccessor().runDMLAction(new DBAction<DemoEntity>() {
        @Override
        public DemoEntity run(DAOContext daoContext) throws Exception {
            DemoEntity entity=new DemoEntity();
            entity.setId("123123");
            daoContext.getDAO(DemoDao.class).remove("id");// delete by id
            daoContext.getDAO(DemoDao.class).remove(new QueryConditionImpl().addRestriction(RestrictionFactory.eq("id", entity.getId())));// delete by queryCondition
            return null;
        }
    });
} catch (Exception e) {
    e.printStackTrace();
}
```
Update record:
```java
try {
    DemoDBAccessProvider.getDbAccessor().runDMLAction(new DBAction<DemoEntity>() {
        @Override
        public DemoEntity run(DAOContext daoContext) throws Exception {
            DemoEntity entity=new DemoEntity();
            entity.setId("123123");
            daoContext.getDAO(DemoDao.class).update(entity);// update by id
            daoContext.getDAO(DemoDao.class).addOrUpdate(entity);// add or update
            return null;
        }
    });
} catch (Exception e) {
    e.printStackTrace();
}
```