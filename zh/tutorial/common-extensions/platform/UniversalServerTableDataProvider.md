# UniversalServerTableDataProvider

| 属性 | 值 |
| --- | --- |
| 接口类型 | extra-decision |
| 完整类名 | `com.fr.decision.fun.UniversalServerTableDataProvider` |

## 背景与场景

决策平台 10.0 引入了数据集和数据源的平台端配置管理功能，解决了之前必须通过设计器远程连接或导入导出才能管理数据集的问题。该接口为插件扩展的数据集类型提供平台端管理能力。

## 接口定义

```java
package com.fr.decision.fun;

import com.fr.base.TableData;
import com.fr.json.JSONObject;
import com.fr.stable.fun.mark.Mutable;
import com.fr.stable.fun.mark.WebCoalition;

public interface UniversalServerTableDataProvider<T extends TableData>
        extends WebCoalition, Mutable {

    String MARK_STRING = "UniversalServerTableDataProvider";
    int CURRENT_LEVEL = 1;

    Class<T> classForTableData();
    String nameForTableData();
    JSONObject serialize(T tableData);
    T deserialize(T oldDataSet, JSONObject object);
}
```

## 支持版本

| 产品线 | 版本 | 支持情况 |
| --- | --- | --- |
| FR | 10.0 | 支持 |
| FR | 11.0 | 支持 |
| BI | 5.1 | 支持 |
| BI | 5.1.2 | 支持 |
| BI | 5.1.3 | 支持 |

## 插件注册

在 `plugin.xml` 中添加以下节点：

```xml
<extra-decision>
    <UniversalServerTableDataProvider class="your class name"/>
</extra-decision>
```

## 原理说明

`DataSetProcessorFactory` 初始化时加载所有插件声明的平台服务端数据集接口，`DataSetResource` 通过 `/{version}/dataset` 路由管理增删改查操作。

## 注意事项

- `classForTableData` 必须返回 TableData 接口；建议继承 `AbstractParameterTableData`
- `nameForTableData` 需与前端数据集类型声明匹配
- `serialize`/`deserialize` 负责数据集实例与 JSON 表示之间的互转
- 前端 `cardType` 引用自定义 FineUI 组件进行 UI 绑定

## 常用链接

- Demo：[demo-table-data-define](https://code.fanruan.com/hugh/demo-table-data-define)
