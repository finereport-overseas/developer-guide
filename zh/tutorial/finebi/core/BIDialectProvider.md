# BIDialectProvider

| 属性 | 值 |
| --- | --- |
| 接口类型 | provider |
| 所属模块 | extra-core |
| 完整类名 | `com.finebi.api.dialect.BIDialectProvider` |
| 官方文档 | 暂无 |

## 简介

为 FineBI 自定义数据库方言的接入点。通过提供从平台 `Dialect` 到自定义 `BIDialect` 的映射函数，使 FineBI 能够针对特定数据库生成正确的 SQL。

> **注意**：插件启用后需要**重启服务**才能生效。

相关需求背景可参考：KERNEL-15316 《BI 支持自定义方言》

## 接口定义

```java
@Open
public interface BIDialectProvider extends Mutable {

    String XML_TAG = "BIDialectProvider";

    int CURRENT_LEVEL = 1;

    /**
     * 返回一个函数：通过平台 Dialect 获取对应的自定义 BIDialect
     * 若当前 Dialect 不需要自定义处理，返回 Optional.empty()
     */
    Function<Dialect, Optional<BIDialect>> getBIDialect();
}
```

### BIDialect 核心接口

`BIDialect` 是方言适配的核心，推荐继承 `AbstractBIDialect` 并按需重写以下方法：

| 方法 | 说明 |
| --- | --- |
| `getDSLContext()` | **核心方法**：返回 jooq DSLContext，用于 SQL 生成 |
| `getFieldInfoSql()` | 获取结果集字段信息 SQL，返回 `empty` 使用默认实现 |
| `getDialectResultSet()` | 包装 ResultSet，做取数过程中的方言适配 |
| `isSupportCTE()` | 是否支持 CTE |
| `windowFunctionNotSupportXxx()` | 各聚合函数的开窗支持（SUM/AVG/MAX/MIN/Variance 等） |
| `isTopNConditionByExists()` | TopN 是否通过 Exists subquery 实现 |
| `isTopNUseWindowFunction()` | TopN 是否通过开窗函数实现 |
| `isRowNumberWithoutOrderBy()` | row_number 是否可省略 ORDER BY |
| `castIntegerWithOverFlow()` | SUM 等函数整数溢出时的显式类型转换 |
| `castIntegerWithPrecision()` | AVG 整数精度问题的类型转换 |
| `checkGroups()` | 自定义 GroupBy 处理逻辑 |
| `isSortNullFirst()` / `isSortNullLast()` | 排序时 NULL 值位置 |
| `isUseAliasInOrder()` | 排序是否使用别名（如 Hive） |
| `alwaysUseCrossJoin()` | 始终使用 CROSS JOIN 语法 |
| `needNullCast()` | NULL 值是否需要显式 CAST |
| `isEqualsWithNullAndEmptyString()` | NULL 与空字符串是否等价 |
| `onlySupportCountAll()` | 是否只支持 COUNT(*) |
| `previewRemoveLastColumn()` | SQL 预览是否删除最后一列（如 Oracle 的 rn） |

## 使用方式

在插件的 `plugin.xml` 中，通过 `extra-core` 标签注册实现类：

```xml
<extra-core>
    <BIDialectProvider class="com.fr.plugin.xxx.YourBIDialectProvider"/>
</extra-core>
```

## 示例代码

以下为崖山数据库（YashanDB）方言适配的参考实现：

```java
// 1. 实现 BIDialectProvider，将崖山 Dialect 映射到自定义 BIDialect
public class YashanBIDialectProvider implements BIDialectProvider {

    @Override
    public Function<Dialect, Optional<BIDialect>> getBIDialect() {
        return dialect -> {
            // 判断是否是崖山数据库的 Dialect
            if (dialect instanceof YashanDialect) {
                return Optional.of(new YashanBIDialect(dialect));
            }
            return Optional.empty();
        };
    }
}

// 2. 继承 AbstractBIDialect，按需重写方言行为
public class YashanBIDialect extends AbstractBIDialect {

    public YashanBIDialect(Dialect dialect) {
        super(dialect);
    }

    @Override
    public boolean windowFunctionNotSupportVariance() {
        return true;  // 崖山不支持 Variance 开窗
    }

    @Override
    public boolean windowFunctionNotSupportStandardDeviation() {
        return true;  // 崖山不支持 StandardDeviation 开窗
    }

    @Override
    public boolean isEqualsWithNullAndEmptyString() {
        return true;  // 崖山将 NULL 和 "" 视为等价
    }

    @Override
    public boolean previewRemoveLastColumn() {
        return false;
    }

    @Override
    public DSLContext getDSLContext() {
        // 基于 Oracle 方言，自定义 SQL 生成逻辑
        return DSL.using(new CustomConfiguration(SQLDialect.ORACLE,
            new DefaultSqlCreator(SQLDialect.ORACLE) { /* 自定义函数实现 */ }
        ));
    }
}
```

## 注意事项

- `getDSLContext()` 是 jooq SQL 生成的核心，实现时需对 jooq 有一定了解，可参考平台其他数据库方言的实现。
- `getBIDialect()` 返回的函数中，只对目标 `Dialect` 类型返回 `Optional.of(...)`，其他类型返回 `Optional.empty()` 以避免影响其他数据库。
- 插件安装或升级后必须**重启 FineBI 服务**才能生效。
- 继承 `AbstractBIDialect` 可以获得大多数方法的默认实现，只需按数据库差异重写必要的方法。
