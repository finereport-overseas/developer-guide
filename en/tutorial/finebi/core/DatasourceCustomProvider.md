# DatasourceCustomProvider

| Property | Value |
| --- | --- |
| Module | extra-core (FineBI) |
| Official Docs | [View Documentation](https://wiki.fanruan.com/pages/viewpage.action?pageId=158402885) |

---

## Purpose

Interface for integrating custom data sources into BI. Provides support for extending data sources and implementing custom SQL translation logic. Refer to Option 2 in [Feasible Approaches for Integrating Data Sources into BI](https://kms.fineres.com/pages/viewpage.action?pageId=271257188) for more details.

## Interface Content

### Main Interface

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
     * SQL translator
     */
    BaseSqlTranslator getBaseSqlTranslator();

    /**
     * Temporary table support.
     * Predicate<Dialect>: dialect matching
     * BiFunction<Dialect, CrossLockManager, TemporaryDialect>: method to create a TemporaryDialect from a Dialect.
     * CrossLockManager can use the default implementation CrossLockFactory.getSingleton()
     */
    List<Pair<Predicate<Dialect>, BiFunction<Dialect, CrossLockManager, TemporaryDialect>>> getTemporaryDialectList();


    /**
     * DB table SQL translator
     *
     * @return
     */
    DatabaseSqlTranslator getDatabaseSqlTranslator();

    /**
     * Generates a dialect from connection information
     *
     * @return
     */
    DialectGenerator getDialectGenerator();
}
```

The following sections describe each of the interfaces involved.

```java
@Open
public interface BaseSqlTranslator {

    /**
     * Translates a BaseCriteria description to SQL
     *
     * @param dialect      dialect
     * @param baseCriteria BaseCriteria description
     * @return sql
     */
    String translate(Dialect dialect, BaseCriteria baseCriteria);

    /**
     * Checks whether the dialect supports translating BaseCriteria descriptions to SQL
     *
     * @param dialect dialect
     * @return whether supported
     */
    boolean supportWithBaseCriteria(Dialect dialect);

    /**
     * Translates a Table description to SQL
     *
     * @param dialect dialect
     * @param table   Table description
     * @return sql
     */
    String translate(Dialect dialect, Table table);

    /**
     * Checks whether the dialect supports translating Table descriptions to SQL
     *
     * @param dialect dialect
     * @return whether supported
     */
    boolean supportWithTable(Dialect dialect);

}
```

`BaseSqlTranslator` is the primary interface for SQL translation. Each data analysis scenario has a corresponding `BaseCriteria` interface description, and `BaseSqlTranslator` is responsible for translating `BaseCriteria` into the corresponding SQL (the same applies to `Table`).

For documentation on the `BaseCriteria` interface, refer to [Direct-Connect API Usage](https://kms.fineres.com/pages/viewpage.action?pageId=13665979), which describes the structure and definition of `BaseCriteria`.

```java
@Open
public interface TemporaryDialect {
    /**
     * Checks whether a temporary table is valid; if not, data needs to be re-extracted
     */
    boolean checkTableValid(CrossConnect connection, String tableName) throws SQLException;

    /**
     * Checks whether a temporary table exists
     */
    boolean checkTableExist(CrossConnect connection, String tableName) throws SQLException;

    /**
     * SQL Server temporary tables require a ## prefix; other dialects use a regular prefix.
     * Note: the tableName here does not include the prefix.
     * All other methods receive the tableName after this transformation.
     */
    String handleTemporaryTableName(String tableName) throws Exception;

    /**
     * Converts a column name to its SQL representation, e.g.: Province -> "Province"
     */
    String column2SQL(String columnName);

    /**
     * Converts a table name to its SQL representation, e.g.: T_A -> `T_A`
     */
    String table2SQL(String tableName);

    /**
     * Escapes special characters in a string constant, e.g.: ' -> ''
     */
    String escapeStringConstant(String constant);

    /**
     * Whether transactions are supported. Transactions are preferred; if not supported, simply skip transaction execution.
     */
    boolean supportTransaction();

    /**
     * Creates a temporary table
     */
    void createTempTable(CrossConnect connection, String tableName, List<Field> fields, String collate) throws SQLException;

    /**
     * Returns the database's default collation, which is used when creating temporary tables
     */
    @Nullable
    String analysisCollate(CrossConnect connection) throws SQLException;

    /**
     * Inserts data from an Excel table
     */
    void insertData(CrossConnect connection, String tableName, List<Field> fields, RowIterator rowIterator) throws SQLException;

    /**
     * Returns the lifecycle listener
     */
    default TemporaryLifeCircle lifeCircle() {
        return new TemporaryLifeCircle() {
        };
    }
}
```

`TemporaryDialect` defines how a direct-connect data source uses temporary tables to support scenarios such as joining Excel datasets with database tables. The interface defines how table names are represented in SQL, among other aspects — refer to the interface documentation for details.

This interface requires the data source to support temporary table functionality; if this scenario is not needed, you do not need to implement it.

```java
@Open
public interface DatabaseSqlTranslator {

    /**
     * Adds a default SQL translator for DB tables
     *
     * @param connection  database connection (do not close the connection inside this interface; close it at the call site)
     * @param schema      data source schema
     * @param dbTableName database table name
     * @return
     */
    String translate(Connection connection, String schema, String dbTableName);

    /**
     * Whether the current dialect is supported
     *
     * @param dialect
     * @return
     */
    boolean supportWithDialect(Dialect dialect);

}
```

`DatabaseSqlTranslator` defines how to generate the SQL that retrieves all detail data from a table given the table name and schema, for example: `select * from tableName`.

```java
@Open
public interface DialectGenerator {

    /**
     * Returns the dialect for a given Connection
     *
     * @param connection
     * @return
     */
    Dialect getDialectFromConnection(Connection connection);

}
```

`DialectGenerator` defines how to generate a `Dialect` from a JDBC connection.

As you can see, nearly every interface involves the `Dialect` interface — `Dialect` is the dialect interface recognized by BI. Therefore, when extending a data source, you must have a corresponding `Dialect` implementation.

`Dialect` has two main classes: `DefaultDialect` and `SchemaDialect`, corresponding to scenarios without and with a schema, respectively. You can extend `Dialect` by inheriting from one of these two classes.

For detailed `Dialect` extension development, refer to: [Dialect Development Kit — Dialect Developer Guide](https://kms.fineres.com/pages/viewpage.action?pageId=17721991).

## Registration

```xml
<extra-core>
   <DatasourceCustomProvider class="fully qualified class name of your plugin implementation" />
</extra-core>
```

## Abstract Class Provided to Plugins

```java
package com.finebi.provider.api.sql;

import com.finebi.api.sql.DatasourceCustomProvider;
import com.fr.stable.fun.mark.API;

/**
 * Extension point for SQL-translating data source adapter plugins
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
