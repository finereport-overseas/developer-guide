# UniversalConnectionProvider

| 属性 | 值 |
| --- | --- |
| 接口类型 | extra-decision |
| 完整类名 | `com.fr.decision.fun.UniversalConnectionProvider` |

## 背景与场景

数据源将配置细节从多个数据集中抽象出来。通过该接口，自定义连接类型（如 Redis、自定义 JDBC 池）可在平台端统一管理，无需在每个数据集中重复嵌入连接配置。

## 接口定义

```java
package com.fr.decision.fun;

import com.fr.json.JSONObject;
import com.fr.stable.fun.mark.Mutable;
import com.fr.stable.fun.mark.WebCoalition;

public interface UniversalConnectionProvider<T extends Connection>
        extends Mutable, WebCoalition {

    String MARK_STRING = "UniversalConnectionProvider";
    int CURRENT_LEVEL = 1;

    String nameForConnection();

    String iconPathForConnection();

    Class<T> classForConnection();

    JSONObject serialize(T t);

    T deserialize(T oldConnection, JSONObject object);
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
    <UniversalConnectionProvider class="your class name"/>
</extra-decision>
```

## 注意事项

- `deserialize()` 必须返回新实例，不得返回旧的连接引用
- `nameForConnection()` 返回值必须与前端注册中的 `databaseType` 完全匹配
- 目前前端界面不支持直接修改连接名称

## 常用链接

- Demo：[demo-connection-provider](https://code.fanruan.com/hugh/demo-connection-provider)

## 开源案例

> 免责声明：所有文档中的开源示例均为开发者自行开发并提供，仅供参考和学习使用。开发者和官方均无义务对开源案例进行教学和指导。禁止用于任何商业用途，若作为商用，一切后果责任由使用者自行承担。

- [demo-tabledata-redis](https://code.fanruan.com/fanruan/demo-tabledata-redis)
