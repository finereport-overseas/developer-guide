# TableDataBuildProvider

| 属性 | 值 |
| --- | --- |
| 接口类型 | extra-core |
| 完整类名 | `com.fr.stable.fun.TableDataBuildProvider` |

## 背景与场景

该接口适用于数据集执行监控和转换场景，例如：将大量同类型数据集转换为更优化、更安全的数据集类型进行计算，或对所有数据集实现统一的参数预处理和执行监控。

## 接口定义

```java
package com.fr.stable.fun;

import com.fr.base.TableData;
import com.fr.stable.fun.mark.Mutable;

public interface TableDataBuildProvider<T extends TableData> extends Mutable {

    int CURRENT_LEVEL = 1;
    String MARK_STRING = "TableDataBuildProvider";

    boolean matchPattern(String tdClassName);
    Class<? extends TableData> getAttrClass();
    Class<T> getBuildClass();
    T newInstance() throws Exception;
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
    <TableDataBuildProvider class="your class name"/>
</extra-core>
```

## 原理说明

该接口在模板文件加载数据集配置时的 XML 转对象过程中生效。系统获取所有已注册的数据集转换提供者，通过 `matchPattern` 识别需要转换的数据集，然后调用 `newInstance` 创建新的数据集实例。

## 常用链接

- Demo：[demo-table-data-build-provider](https://code.fanruan.com/hugh/demo-table-data-build-provider)
