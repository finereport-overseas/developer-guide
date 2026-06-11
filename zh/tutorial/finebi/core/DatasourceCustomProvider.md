# DatasourceCustomProvider

| 属性 | 值 |
| --- | --- |
| 所属模块 | extra-core (FineBI) |
| 官方文档 | [查看文档](https://wiki.fanruan.com/pages/viewpage.action?pageId=158402885) |

---

## 接口作用

数据源接入BI接口，提供扩展数据源，自定义实现转sql逻辑。详情可以参考文档 [数据源接入BI的可行方案](https://kms.fineres.com/pages/viewpage.action?pageId=271257188) 中的方案二。

## 接口内容

### 主要接口


```java
package com.finebi.api.sql;


import com.finebi.api.dialect.DialectGenerator;
import com.finebi.api.sql.database.DatabaseSqlTranslator;
import com.finebi.api.sql.temporary.CrossLockManager;
import com.finebi.api.sql.temporary.TemporaryDialect;
import com.finebi.base.tuple.Pair;
import com.fr.common.annotations.Open;
import com.fr.data.core.db.dialect.Dialect;
import com.fr.stable.fun.mark.Mutable;

import java.util.List;
import java.util.function.BiFunction;
import java.util.function.Predicate;

/**
 * @author kris
 * @version 5.1.3
 * Created by kris on 2021/5/26
 */
@Open
public interface DatasourceCustomProvider extends Mutable {

    String XML_TAG = "DatasourceCustomProvider";

    int CURRENT_LEVEL = 1;

    /**
     * Sql转化器
     */
    BaseSqlTranslator getBaseSqlTranslator();

    /**
     * 临时表功能
     * Predicate<Dialect>: 方言匹配
     * BiFunction<Dialect, CrossLockManager, TemporaryDialect> 根据Dialect创建TemporaryDialect的方法。CrossLockManager可以使用默认实现CrossLockFactory.getSingleton()
     */
    List<Pair<Predicate<Dialect>, BiFunction<Dialect, CrossLockManager, TemporaryDialect>>> getTemporaryDialectList();


    /**
     * db表sql转化器
     *
     * @return
     */
    DatabaseSqlTranslator getDatabaseSqlTranslator();

    /**
     * 根据连接信息获取方言功能
     *
     * @return
     */
    DialectGenerator getDialectGenerator();
}
  
```

下面依次说明下涉及到的接口。


```java
@Open
public interface BaseSqlTranslator {

    /**
     * 根据BaseCriteria接口描述 转SQL
     *
     * @param dialect      方言
     * @param baseCriteria baseCriteria接口描述
     * @return sql
     */
    String translate(Dialect dialect, BaseCriteria baseCriteria);

    /**
     * baseCriteria接口描述的转sql支持的方言判断
     *
     * @param dialect 方言
     * @return 是否支持
     */
    boolean supportWithBaseCriteria(Dialect dialect);

    /**
     * 根据Table接口描述 转SQL
     *
     * @param dialect 方言
     * @param table   table接口描述
     * @return sql
     */
    String translate(Dialect dialect, Table table);

    /**
     * table接口描述转sql支持的方言判断
     *
     * @param dialect 方言
     * @return 是否支持
     */
    boolean supportWithTable(Dialect dialect);

}
```

BaseSqlTranslator是转sql的主要接口，数据分析的每个场景都有对应的BaseCriteria接口来描述，BaseSqlTranslator就是负责将BaseCriteria转成对应的sql（Table同BaseCriteria）。

BaseCriteria接口介绍参考文档：[直连接口调用](https://kms.fineres.com/pages/viewpage.action?pageId=13665979)，里面介绍了BaseCriteria的结构以及定义。


```java
@Open
public interface TemporaryDialect {
    /**
     * 检查临时表是否可用，不可用需要重新抽取，可用则直接跳过
     */
    boolean checkTableValid(CrossConnect connection, String tableName) throws SQLException;

    /**
     * 查询临时表是否存在
     */
    boolean checkTableExist(CrossConnect connection, String tableName) throws SQLException;

    /**
     * SQLSERVER临时表需要在前面加##，其他方言默认加前缀就行了
     * 注意：这里的tableName是没有前缀的
     * 其他方法的tableName都是经过这里转换后的
     */
    String handleTemporaryTableName(String tableName) throws Exception;

    /**
     * 字段转化成sql描述，例如：省份 -> "省份"
     */
    String column2SQL(String columnName);

    /**
     * 表名转化成sql描述，例如：T_A -> `T_A`
     */
    String table2SQL(String tableName);

    /**
     * 处理字符串中的特殊字符，例如：' -> ''
     */
    String escapeStringConstant(String constant);

    /**
     * 是否支持事务，优先开启事务，不支持事务。简单处理就是不执行
     */
    boolean supportTransaction();

    /**
     * 创建临时表
     */
    void createTempTable(CrossConnect connection, String tableName, List<Field> fields, String collate) throws SQLException;

    /**
     * 获取数据库的默认的排序规则，改规则会用来创建临时表
     */
    @Nullable
    String analysisCollate(CrossConnect connection) throws SQLException;

    /**
     * 抽取excel表的数据
     */
    void insertData(CrossConnect connection, String tableName, List<Field> fields, RowIterator rowIterator) throws SQLException;

    /**
     * 获取生命周期监听器
     */
    default TemporaryLifeCircle lifeCircle() {
        return new TemporaryLifeCircle() {
        };
    }
}
```

临时表接口TemporaryDialect，是用来定义直连如何使用临时表的方案来实现Excel数据集和数据库表创建关联的场景的。接口定义了表名到sql中的表名等处理方式，这个查看接口说明即可，这里不一一说明。

同时该接口需要数据源支持临时表功能，否则不能使用。如果没有这种场景需求，可以不用实现该接口。


```java
@Open
public interface DatabaseSqlTranslator {

    /**
     * 添加db表的默认sql转换器
     *
     * @param connection  数据连接(接口中不关闭connection，需要在调用时自行关闭)
     * @param schema      数据源模式
     * @param dbTableName 数据库表名
     * @return
     */
    String translate(Connection connection, String schema, String dbTableName);

    /**
     * 是否支持当前方言
     *
     * @param dialect
     * @return
     */
    boolean supportWithDialect(Dialect dialect);

}
```

DatabaseSqlTranslator定义了如何根据表名和schema，转化成对应的获取表所有明细数据的SQL，比如 select * from tableName。


```java
@Open
public interface DialectGenerator {

    /**
     * 根据Connection返回方言
     *
     * @param connection
     * @return
     */
    Dialect getDialectFromConnection(Connection connection);

}
```

DialectGenarator定义如何从JDBC中生成Dialect方言。

可以看出，每个接口基本上都涉及到了Dialect这个接口，Dialect接口就是BI认识的方言接口。所以，当扩展一个数据源的时候，必定需要有其对应的Dialect实现。

Dialect有两个比较主要的类DefaultDialect和SchemaDialect，分别对应无schema和有schema两种情况，可以通过继承这两个类中的其中一个来实现Dialect的扩展。

详细的Dialect扩展开发，请参考：[Dialect Development Kit Dialect开发指南](https://kms.fineres.com/pages/viewpage.action?pageId=17721991)

## 接口接入


```xml
<extra-core>
   <DatasourceCustomProvider class="插件自定义实现包全名" />
</extra-core>
```

## 提供给插件的抽象类


```java
package com.finebi.provider.api.sql;

import com.finebi.api.sql.DatasourceCustomProvider;
import com.fr.stable.fun.mark.API;

/**
 * 转sql数据源适配插件接入点
 *
 * @author kris
 * @version 5.1.3
 * Created by kris on 2021/6/16
 */
@API(level = DatasourceCustomProvider.CURRENT_LEVEL)
public abstract class AbstractDatasourceCustomProvider implements DatasourceCustomProvider {

    @Override
    public int currentAPILevel() {
        return DatasourceCustomProvider.CURRENT_LEVEL;
    }

    @Override
    public String mark4Provider() {
        return getClass().getName();
    }
}
  
```
