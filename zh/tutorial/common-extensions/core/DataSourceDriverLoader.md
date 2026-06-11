# DataSourceDriverLoader

| 属性 | 值 |
| --- | --- |
| 接口类型 | extra-core |
| 所属模块 | extra-core |
| 完整类名 | `com.fr.stable.fun.DataSourceDriverLoader` |

## 接口作用

为解决大数据套件的冲突问题，提供一个拦截器接口，用于拦截目标驱动的 ClassLoader 并直接设置到 DataSource 中。

## 接口内容

### 主要接口

```java
package com.fr.stable.fun;

import com.fr.stable.fun.mark.Mutable;

/**
 * 提供给插件用的classLoader拦截器
 */
public interface DataSourceDriverLoader extends Mutable {

    // 判断是否命中目标驱动
    boolean isInterceptAllowed(String databaseType, String url);

    // 获取具体的classLoader
    ClassLoader getClassLoader();
}
```

### 关联接口

调用该接口的工具类：

```java
package com.fr.stable.fun.impl;

import com.fr.plugin.ExtraClassManager;
import com.fr.stable.fun.DataSourceDriverLoader;

import java.util.Set;

/**
 * 具体调用接口的工具类
 */
public class DataSourceDriverLoaderUtils {

    public static ClassLoader getClassLoader(String databaseType, String url) {
        Set<DataSourceDriverLoader> dataSourceDriverLoaders = getDataSourceDriverLoader();
        // 不是空的话说明插件成功加载
        if (dataSourceDriverLoaders != null && !dataSourceDriverLoaders.isEmpty()) {
            FineLoggerFactory.getLogger().debug("====== loading classloader plugin ======");
            for (DataSourceDriverLoader datasourceDriverLoader : dataSourceDriverLoaders) {
                // 判断是否是目标driver，改为url和databaseType属性用于更加精确的划分
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

### 接口接入

```xml
<extra-core>
    <DataSourceDriverLoader class="com.fr.plugin.dialect.classloader.{$数据库名}ClassLoaderHandler"/>
</extra-core>
```

## 接口示例

示例源码：[demo-driver-loader](https://git.fanruan.com/fanruan/demo-driver-loader)

> Demo 中在成功命中目标驱动后设置了日志信息输出。

## 注意事项

1. 仅当实现的 ClassLoader 不为空时，才会 set 进 DataSource 中
2. 最新的 2.0 版本隔离插件需要使用 10.15 及以上的 release 5.1.2 版本
