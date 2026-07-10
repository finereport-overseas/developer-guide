# DataSourceDriverLoader

| Property | Value |
| --- | --- |
| Interface Type | extra-core |
| Module | extra-core |
| Full Class Name | `com.fr.stable.fun.DataSourceDriverLoader` |

## Interface Purpose

To resolve conflicts in big data suites, this interface provides an interceptor that intercepts the ClassLoader of the target driver and sets it directly into the DataSource.

## Interface Definition

### Main Interface

```java
package com.fr.stable.fun;

import com.fr.stable.fun.mark.Mutable;

/**
 * ClassLoader interceptor provided for plugins
 */
public interface DataSourceDriverLoader extends Mutable {

    // Determines whether the target driver is matched
    boolean isInterceptAllowed(String databaseType, String url);

    // Returns the specific ClassLoader
    ClassLoader getClassLoader();
}
```

### Related Interfaces

Utility class that calls this interface:

```java
package com.fr.stable.fun.impl;

import com.fr.plugin.ExtraClassManager;
import com.fr.stable.fun.DataSourceDriverLoader;

import java.util.Set;

/**
 * Utility class that invokes the interface
 */
public class DataSourceDriverLoaderUtils {

    public static ClassLoader getClassLoader(String databaseType, String url) {
        Set<DataSourceDriverLoader> dataSourceDriverLoaders = getDataSourceDriverLoader();
        // Non-null means the plugin was loaded successfully
        if (dataSourceDriverLoaders != null && !dataSourceDriverLoaders.isEmpty()) {
            FineLoggerFactory.getLogger().debug("====== loading classloader plugin ======");
            for (DataSourceDriverLoader datasourceDriverLoader : dataSourceDriverLoaders) {
                // Check if it matches the target driver using url and databaseType for more precise matching
                if (datasourceDriverLoader.isInterceptAllowed(databaseType, url)) {
                    ClassLoader classLoader = datasourceDriverLoader.getClassLoader();
                    return classLoader;
                }
            }
        }
        return null;
    }

    private static Set<DataSourceDriverLoader> getDataSourceDriverLoader() {
        return ExtraClassManager.getInstance().getArray(DataSourceDriverLoader.XML_TAG);
    }
}
```

### Plugin Registration

```xml
<extra-core>
    <DataSourceDriverLoader class="com.fr.plugin.dialect.classloader.{$DatabaseName}ClassLoaderHandler"/>
</extra-core>
```

## Interface Example

Example source code: [demo-driver-loader](https://git.fanruan.com/fanruan/demo-driver-loader)

> The demo outputs log information when the target driver is successfully matched.

## Notes

1. The ClassLoader is only set into the DataSource if the implemented ClassLoader is non-null
2. The latest 2.0 isolation plugin requires FR 10.15 or above (release 5.1.2 or later)
