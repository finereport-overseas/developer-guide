# DBAccessProvider

| 属性 | 值 |
| --- | --- |
| 接口类型 | extra-core |
| 完整类名 | `com.fr.db.fun.DBAccessProvider` |

## 背景与场景

FineReport 和决策平台的管理数据存储在 finedb 中（支持外接数据库）。通过 `DBAccessProvider` 接口，插件开发者可以扩展 finedb 中的数据表，无需用户手动建表。

## 接口定义

```java
package com.fr.db.fun;

import com.fr.plugin.db.accessor.DBAccessible;
import com.fr.stable.fun.mark.Mutable;

public interface DBAccessProvider extends Mutable, DBAccessible {
    String XML_TAG = "DBAccessProvider";
    int CURRENT_LEVEL = 1;
}
```

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
    void onDBAvailable(DBAccessor accessor);
}
```

```java
package com.fr.stable.db.dao;

import com.fr.stable.db.data.DataRecord;

public interface DAOProvider<T extends DataRecord> {
    Class<T> getEntityClass();
    Class<? extends BaseDAO<T>> getDAOClass();
}
```

```java
package com.fr.stable.db.accessor;

import com.fr.stable.db.action.DBAction;

public interface DBAccessor {
    <T> T runQueryAction(DBAction<T> action) throws Exception;
    <T> T runDMLAction(DBAction<T> action) throws Exception;
}
```

```java
package com.fr.stable.db.action;

import com.fr.stable.db.dao.DAOContext;

public interface DBAction<T> {
    T run(DAOContext context) throws Exception;
}
```

## 支持版本

| 产品线 | 版本 | 支持情况 |
| --- | --- | --- |
| FR | 10.0 | 支持 |
| FR | 11.0 | 支持 |

## 插件注册

在 `plugin.xml` 中添加以下节点：

```xml
<extra-core>
    <DBAccessProvider class="your class name"/>
</extra-core>
```

## 原理说明

系统在 `PluginDBManager` 初始化时，加载所有声明的 `DBAccessProvider` 接口实例。该管理器通过 `DBContext` 为业务单元提供持久化调用入口。

## 常用链接

- Demo：[demo-db-access-provider](https://code.fanruan.com/hugh/demo-db-access-provider)

## 开源案例

> 免责声明：所有文档中的开源示例均为开发者自行开发并提供，仅供参考和学习使用。开发者和官方均无义务对开源案例进行教学和指导。禁止用于任何商业用途，若作为商用，一切后果责任由使用者自行承担。

- [open-JSD-8016](https://code.fanruan.com/hugh/open-JSD-8016)
- [open-JSD-7957](https://code.fanruan.com/hugh/open-JSD-7957)
- [open-JSD-7858](https://code.fanruan.com/hugh/open-JSD-7858)
- [open-JSD-7843](https://code.fanruan.com/hugh/open-JSD-7843)
- [open-JSD-7868](https://code.fanruan.com/hugh/open-JSD-7868)
- [open-JSD-7747](https://code.fanruan.com/hugh/open-JSD-7747)
- [demo-db-access](https://code.fanruan.com/fanruan/demo-db-access)
