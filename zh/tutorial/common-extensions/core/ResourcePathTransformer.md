# ResourcePathTransformer

| 属性 | 值 |
| --- | --- |
| 接口类型 | extra-core |
| 完整类名 | `com.fr.stable.fun.ResourcePathTransformer` |

## 背景与场景

该接口主要用于产品中资源文件的替换。常见应用场景包括：OEM 场景下需要修改图标、报表和资源文件路径迁移、替换内置 HTML 模板等。

## 接口定义

```java
package com.fr.stable.fun;

import com.fr.stable.fun.mark.Mutable;

/**
 * @author richie
 * @date 2015-06-09
 * @since 8.0
 * 资源文件读取和转换，主要用于OEM场景
 */
public interface ResourcePathTransformer extends Mutable {

    String MARK_STRING = "ResourcePathTransformer";
    int CURRENT_LEVEL = 1;

    boolean accept(String path);

    /**
     * 转换资源读取路径
     * @param path 需要转换的路径
     * @return 新的资源文件路径
     */
    String transform(String path);
}
```

## 支持版本

| 产品线 | 版本 | 支持情况 |
| --- | --- | --- |
| FR | 8.0 | 支持 |
| FR | 9.0 | 支持 |
| FR | 10.0 | 支持 |
| FR | 11.0 | 支持 |

## 插件注册

在 `plugin.xml` 中添加以下节点：

```xml
<extra-core>
    <ResourcePathTransformer class="your class name"/>
</extra-core>
```

## 原理说明

接口通过 `StableFactory` 方法获取实现。系统通过 `IOUtils.readResource()` 和 `IconUtils` 加载所有资源文件，并在读取前逐一检查转换器，确定路径是否需要转换。

**限制说明**：插件引擎启动前加载的资源不会经过转换处理。

## 常用链接

- Demo：[demo-resource-path-transformer](https://code.fanruan.com/hugh/demo-resource-path-transformer)
