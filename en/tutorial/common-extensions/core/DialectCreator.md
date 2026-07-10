# DialectCreator

| Property | Value |
| --- | --- |
| Interface Type | extra-core |
| Full Class Name | `com.fr.stable.fun.DialectCreator` |

## Background and Use Cases

FineReport supports connecting to mainstream databases via JDBC. Although JDBC is highly standardized, different database vendors differ in connection protocols, usage patterns, and SQL syntax — differences referred to as "database dialects" in FanRuan development. Developers can use the `DialectCreator` interface to provide adaptation support for a target database, enabling it to work correctly within FineReport.

## Interface Definition

```java
package com.fr.stable.fun;

import com.fr.stable.fun.mark.Mutable;

public interface DialectCreator extends Mutable {
    String XML_TAG = "DialectCreator";
    int CURRENT_LEVEL = 1;

    /**
     * Returns the corresponding Dialect adapter class based on the data source driver info
     */
    Class<?> generate(UrlDriver driver);

    /**
     * Returns the corresponding Dialect adapter class based on the database connection
     */
    Class<?> generate(Connection connection);
}
```

The `Dialect` interface contains 80+ methods covering database-specific operations:

- Table/Schema initialization and retrieval
- Column type conversion
- SQL LIMIT/OFFSET generation
- Stored procedure handling
- Foreign key management
- Sequence/auto-increment generation
- Transaction control
- Unique constraint exception handling

`AbstractDialect` provides a simplified adapter wrapper; all `Dialect` interface methods delegate to the default implementations in `DialectFactory`. `DefaultDialect` extends `AbstractDialect` and implements all methods using the key-value pattern from `DialectKeyConstants`.

## Supported Versions

| Product | Version | Support |
| --- | --- | --- |
| FR | 8.0 | Supported |
| FR | 9.0 | Supported |
| FR | 10.0 | Supported |
| FR | 11.0 | Supported |
| BI | 3.6 | Supported |
| BI | 4.0 | Supported |
| BI | 5.1 | Supported |
| BI | 5.1.2 | Supported |
| BI | 5.1.3 | Supported |

## Plugin Registration

Add the following node in `plugin.xml`:

```xml
<extra-core>
    <DialectCreator class="your class name"/>
</extra-core>
```

## How It Works

All dialects are created by `DialectFactory`. During initialization, the factory instantiates `DialectProviderImpl` (which implements `DialectProvider`). The two methods `getDialectFromExtraDriver` and `getDialectFromExtraMetadata` retrieve dialect instances from plugins and return the corresponding dialect class.

**Note:** The `DialectCreator#generate` interface method must return `null` when no matching dialect is found.

## Useful Links

- Demo: [demo-dialect-creator](https://code.fanruan.com/hugh/demo-dialect-creator)
