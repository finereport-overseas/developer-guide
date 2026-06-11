# TransferDataSetManagerProvider

| 属性 | 值 |
| --- | --- |
| 接口类型 | extra-decision |
| 完整类名 | `com.fr.decision.fun.TransferDataSetManagerProvider` |

## 背景与场景

FineReport 传统上需要在本地设计后再部署到服务器。对于没有远程设计功能的用户，数据连接和服务器数据集的管理需要重复手工操作。10.0 引入了数据集导入导出功能，该接口使自定义数据集插件也能支持导入导出。

## 接口定义

```java
package com.fr.decision.fun;

import com.fr.stable.fun.mark.Mutable;

public interface TransferDataSetManagerProvider<T extends TableData>
        extends Mutable, TransferDataSetManager<T> {

    String XML_TAG = "TransferDataSetManagerProvider";
    int CURRENT_LEVEL = 1;
}
```

```java
package com.fr.decision.webservice.v10.datasource.dataset.manage;

public interface TransferDataSetManager<T extends TableData> {

    Class<? extends TableData> getDataSetClass();

    String serialize(T t);

    T deserialize(String str);

    String[] getDependencyPaths(T t);
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
<extra-decision>
    <TransferDataSetManagerProvider class="your class name"/>
</extra-decision>
```

## 原理说明

`TransferDataSetFactory` 维护数据集类到 manager 的映射注册表。序列化时可复用已有的 `readXML`/`writeXML` 实现：

```java
GeneralXMLTools.writeXMLableAsString(ds)
GeneralXMLTools.readStringAsXMLable(config, ds)
```

`getDependencyPaths` 方法仅导出数据集所依赖的必要文件路径。

## 常用链接

- Demo：[demo-transfer-data-set-manager-provider](https://code.fanruan.com/hugh/demo-transfer-data-set-manager-provider)
