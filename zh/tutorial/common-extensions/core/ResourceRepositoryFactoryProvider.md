# ResourceRepositoryFactoryProvider

| 属性 | 值 |
| --- | --- |
| 接口类型 | extra-core |
| 完整类名 | `com.fr.io.fun.ResourceRepositoryFactoryProvider` |

## 背景与场景

在集群环境或多个报表系统共享模板和配置文件的场景下，决策系统提供了 FTP 和 SFTP 文件服务器支持。资产、细粒度报表、资源、计划任务、仪表板、宝藏等目录下的资源，均从文件服务器位置读取。`ResourceRepositoryFactoryProvider` 接口允许开发者将文件服务器功能扩展到 OSS、HDFS 等其他存储类型。

## 接口定义

```java
package com.fr.io.fun;

import com.fr.stable.fun.mark.Mutable;

public interface ResourceRepositoryFactoryProvider<T extends RepositoryConfig> extends Mutable {
    CalculatorKey KEY = CalculatorKey.createKey(ResourceRepositoryFactoryProvider.class.getName());
    String MARK_STRING = "ResourceRepositoryFactoryProvider";
    RepositoryFactoryProvider<T> getFactory();
}
```

```java
@Open
public interface RepositoryFactoryProvider<T extends RepositoryConfig> extends Serializable {
    String getIdentity();
    RepositoryConfigManagerProvider<T> getConfigManager();
    Class<? extends RepositoryProfile<T>> getProfileClass();
    Class<T> getConfigClass();
    boolean verifyConfig(T config);
    ResourceRepository produce(String repoName, String workRoot, T config);
    ResourceRepository produce(String repoName, String workRoot);
}
```

```java
public interface ResourceRepository extends RepositoryInfoProvider, WorkResource {
    String getSeparator();
    FineFileEntry getEntry(String path);
    FineFileEntry[] listEntry(String dir);
    URL getResource(String path);
    InputStream read(String file) throws ResourceIOException;
    String[] list(String dir, Filter<String> filter);
    String[] list(String dir);
    void write(String file, InputStream data) throws ResourceIOException;
    void appendWrite(String file, InputStream data) throws ResourceIOException;
    void appendWrite(String file, byte[] data) throws ResourceIOException;
    boolean copy(String origPath, String desPath) throws ResourceIOException;
    boolean isDirectory(String path);
    void shutDown();
    default boolean authentication() { return true; }
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
    <ResourceRepositoryFactoryProvider class="your class name"/>
</extra-core>
```

## 原理说明

文件服务器逻辑位于较底层，初始化和调用流程较为复杂。`ResourceRepositoryFactoryProvider` 作为插件入口，向决策平台提供文件服务器工厂。`RepositoryConfig` 表示自定义存储配置；`RepositoryProfile` 是平台内部的配置对象，其中包含 `RepositoryConfig`。

前端组件需实现 `getValue()` 和 `validation()` 方法，配置值通过 getter/setter 注解与后端配置映射。

## 常用链接

- Demo：[demo-resource-repository-factory-provider](https://code.fanruan.com/hugh/demo-resource-repository-factory-provider)
