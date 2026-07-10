# BIDialectProvider

| Property | Value |
| --- | --- |
| Interface Type | provider |
| Module | extra-core |
| Full Class Name | `com.finebi.api.dialect.BIDialectProvider` |
| Official Docs | N/A |

## Overview

An extension point for integrating custom database dialects into FineBI. By providing a mapping function from the platform `Dialect` to a custom `BIDialect`, FineBI can generate correct SQL for a specific database.

> **Note**: The FineBI service must be **restarted** after the plugin is enabled for changes to take effect.

For background on related requirements, see: KERNEL-15316 "BI Support for Custom Dialects".

## Interface Definition

```java
@Open
public interface BIDialectProvider extends Mutable {

    String XML_TAG = "BIDialectProvider";

    int CURRENT_LEVEL = 1;

    /**
     * Returns a function that maps a platform Dialect to a custom BIDialect.
     * Return Optional.empty() if the current Dialect does not require custom handling.
     */
    Function<Dialect, Optional<BIDialect>> getBIDialect();
}
```

### Core BIDialect Interface

`BIDialect` is the core of dialect adaptation. It is recommended to extend `AbstractBIDialect` and override the following methods as needed:

| Method | Description |
| --- | --- |
| `getDSLContext()` | **Core method**: Returns a jooq DSLContext used for SQL generation |
| `getFieldInfoSql()` | Returns the SQL for retrieving result set field info; return `empty` to use the default implementation |
| `getDialectResultSet()` | Wraps a ResultSet to apply dialect adaptations during data retrieval |
| `isSupportCTE()` | Whether CTE is supported |
| `windowFunctionNotSupportXxx()` | Window function support for each aggregate (SUM/AVG/MAX/MIN/Variance, etc.) |
| `isTopNConditionByExists()` | Whether TopN is implemented via an EXISTS subquery |
| `isTopNUseWindowFunction()` | Whether TopN is implemented via a window function |
| `isRowNumberWithoutOrderBy()` | Whether `row_number` can omit ORDER BY |
| `castIntegerWithOverFlow()` | Explicit type cast for integer overflow in SUM and similar functions |
| `castIntegerWithPrecision()` | Type cast for AVG integer precision issues |
| `checkGroups()` | Custom GROUP BY handling logic |
| `isSortNullFirst()` / `isSortNullLast()` | Position of NULL values during sorting |
| `isUseAliasInOrder()` | Whether to use aliases in ORDER BY (e.g., Hive) |
| `alwaysUseCrossJoin()` | Always use CROSS JOIN syntax |
| `needNullCast()` | Whether NULL values require an explicit CAST |
| `isEqualsWithNullAndEmptyString()` | Whether NULL and empty string are treated as equivalent |
| `onlySupportCountAll()` | Whether only `COUNT(*)` is supported |
| `previewRemoveLastColumn()` | Whether to remove the last column in SQL preview (e.g., Oracle's `rn`) |

## Usage

Register your implementation class in the plugin's `plugin.xml` via the `extra-core` tag:

```xml
<extra-core>
    <BIDialectProvider class="com.fr.plugin.xxx.YourBIDialectProvider"/>
</extra-core>
```

## Example Code

The following is a reference implementation for YashanDB dialect adaptation:

```java
// 1. Implement BIDialectProvider to map the Yashan Dialect to a custom BIDialect
public class YashanBIDialectProvider implements BIDialectProvider {

    @Override
    public Function<Dialect, Optional<BIDialect>> getBIDialect() {
        return dialect -> {
            // Check whether this is a YashanDB Dialect
            if (dialect instanceof YashanDialect) {
                return Optional.of(new YashanBIDialect(dialect));
            }
            return Optional.empty();
        };
    }
}

// 2. Extend AbstractBIDialect and override dialect behaviors as needed
public class YashanBIDialect extends AbstractBIDialect {

    public YashanBIDialect(Dialect dialect) {
        super(dialect);
    }

    @Override
    public boolean windowFunctionNotSupportVariance() {
        return true;  // YashanDB does not support Variance window functions
    }

    @Override
    public boolean windowFunctionNotSupportStandardDeviation() {
        return true;  // YashanDB does not support StandardDeviation window functions
    }

    @Override
    public boolean isEqualsWithNullAndEmptyString() {
        return true;  // YashanDB treats NULL and "" as equivalent
    }

    @Override
    public boolean previewRemoveLastColumn() {
        return false;
    }

    @Override
    public DSLContext getDSLContext() {
        // Based on Oracle dialect with custom SQL generation logic
        return DSL.using(new CustomConfiguration(SQLDialect.ORACLE,
            new DefaultSqlCreator(SQLDialect.ORACLE) { /* custom function implementations */ }
        ));
    }
}
```

## Notes

- `getDSLContext()` is the core of jooq SQL generation. Implementation requires familiarity with jooq; refer to the platform's existing database dialect implementations for guidance.
- In the function returned by `getBIDialect()`, return `Optional.of(...)` only for the target `Dialect` type and `Optional.empty()` for all others to avoid affecting other databases.
- The FineBI service **must be restarted** after installing or upgrading the plugin for it to take effect.
- Extending `AbstractBIDialect` provides default implementations for most methods; only override methods where the database behavior differs.
