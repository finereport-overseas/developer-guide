# ResourceRepositoryFactoryProvider

| Property | Value |
| --- | --- |
| Interface Type | extra-core |
| Full Class Name | `com.fr.io.fun.ResourceRepositoryFactoryProvider` |

## Background and Use Cases

In cluster environments or scenarios where multiple report systems share templates and configuration files, the decision-making system provides FTP and SFTP file server support. Resources in directories such as assets, fine-grained reports, resources, scheduled tasks, dashboards, and treasures are all read from the file server location. The `ResourceRepositoryFactoryProvider` interface allows developers to extend the file server functionality to other storage types such as OSS and HDFS.

## Interface Definition

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

## Supported Versions

| Product | Version | Support |
| --- | --- | --- |
| FR | 10.0 | Supported |
| FR | 11.0 | Supported |

## Plugin Registration

Add the following node in `plugin.xml`:

```xml
<extra-core>
    <ResourceRepositoryFactoryProvider class="your class name"/>
</extra-core>
```

## How It Works

The file server logic sits at a lower layer, and the initialization and invocation flow is relatively complex. `ResourceRepositoryFactoryProvider` serves as the plugin entry point, providing a file server factory to the decision-making platform. `RepositoryConfig` represents the custom storage configuration; `RepositoryProfile` is the platform's internal configuration object, which contains a `RepositoryConfig`.

Frontend components need to implement `getValue()` and `validation()` methods, with configuration values mapped to backend configuration via getter/setter annotations.

## Useful Links

- Demo: [demo-resource-repository-factory-provider](https://code.fanruan.com/hugh/demo-resource-repository-factory-provider)
