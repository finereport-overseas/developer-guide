# DSModifyProvider

| 属性 | 值 |
| --- | --- |
| 接口类型 | extra-core |
| 完整类名 | `com.fr.stable.fun.DSModifyProvider` |

## 背景与场景

该接口用于在所有数据集类型完成数据取用后，对数据进行二次加工处理。无论是产品内置数据集还是第三方插件数据集，均支持后处理。常见应用场景包括：数据拆分（JSON/XML 字段细化）、数据脱敏、基本聚合处理、执行监控等。

## 接口定义

```java
package com.fr.stable.fun;

import com.fr.base.TableData;
import com.fr.general.data.DataModel;
import com.fr.script.Calculator;
import com.fr.stable.fun.mark.Mutable;

public interface DSModifyProvider extends Mutable {

    String MARK_STRING = "DSModifyProvider";

    /**
     * 数据集格式验证，判断是否需要进行二次处理
     * 支持多个实现并存
     */
    boolean accept(TableData ds, Calculator cal, DataModel old);

    /**
     * 数据集二次处理
     * 对初始结果进行加工，返回修改后的数据集
     */
    DataModel modify(TableData ds, Calculator cal, DataModel old);
}
```

## 支持版本

| 产品线 | 版本 | 支持情况 |
| --- | --- | --- |
| FR | 8.0 | 支持 |
| FR | 9.0 | 支持 |
| FR | 10.0 | 支持 |
| FR | 11.0 | 支持 |
| BI | 3.6 | 支持 |
| BI | 4.0 | 支持 |
| BI | 5.1 | 支持 |
| BI | 5.1.2 | 支持 |
| BI | 5.1.3 | 支持 |

## 插件注册

在 `plugin.xml` 中添加以下节点：

```xml
<extra-core>
    <DSModifyProvider class="your class name"/>
</extra-core>
```

## 原理说明

所有数据集计算均经过两个方法：`EmbeddedTableData#createDataModel`（设计器预览）和 `SynchronizedLiveDataModelUtils.getLiveDataModel`（服务端计算）。系统通过 `PluginModule` 获取所有已注册的适配器，依次根据 `accept()` 方法的判断结果，按顺序应用修改逻辑。

## 常用链接

- Demo：[demo-ds-modify-provider](https://code.fanruan.com/hugh/demo-ds-modify-provider)
- 开源参考：[demo-tabledata-modify](https://code.fanruan.com/fanruan/demo-tabledata-modify)
