# FunctionDefContainer

| 属性 | 值 |
| --- | --- |
| 接口类型 | extra-core |
| 完整类名 | `com.fr.stable.fun.FunctionDefContainer` |

## 背景与场景

帆软报表提供了丰富的内置函数用于数据处理。当内置函数无法满足特定业务需求时，可通过插件接口扩展自定义函数。有两种主要扩展方式：

- `AbstractFunction`：扩展单个函数
- `FunctionDefContainer`：批量注册一组业务相关函数

当需要在特定业务逻辑下扩展多个相关函数时，推荐使用 `FunctionDefContainer` 进行分组管理。

## 接口定义

```java
package com.fr.stable.fun;

import com.fr.stable.fun.mark.Mutable;
import com.fr.stable.script.FunctionDef;

public interface FunctionDefContainer extends Mutable {
    int CURRENT_LEVEL = 1;
    String MARK_STRING = "FunctionGroup";

    FunctionDef[] getFunctionDefs();
    String getGroupName();
}
```

`FunctionDef` 包含函数名、描述和类名属性，支持 XML 序列化。

`AbstractFunction` 是抽象基类，实现了 `evalExpression()` 方法处理 Node 参数，委托给抽象方法 `run(Object[])`，并处理错误上报。

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
    <FunctionGroup class="your class name"/>
</extra-core>
```

> 注意：注册标签为 `<FunctionGroup>`，与接口类名 `FunctionDefContainer` 不同（`MARK_STRING = "FunctionGroup"`）。

## 原理说明

插件加载时，`ExtraClassManager` 将 XML 属性转换为缓存的 `FunctionDef` 对象，完成函数定义的初始化。函数通过注册到 `DefaultNameSpace` 融入计算体系，查找优先级为：内置函数 → 自定义扩展 → 单函数扩展 → 分组函数扩展。

## 常用链接

- Demo：[demo-function-def-container](https://code.fanruan.com/hugh/demo-function-def-container)

## 开源案例

> 免责声明：所有文档中的开源示例均为开发者自行开发并提供，仅供参考和学习使用。开发者和官方均无义务对开源案例进行教学和指导。禁止用于任何商业用途，若作为商用，一切后果责任由使用者自行承担。

- [demo-function-fibonacci](https://code.fanruan.com/fanruan/demo-function-fibonacci)
- [open-JSD-7837](https://code.fanruan.com/hugh/open-JSD-7837)
- [open-JSD-7615](https://code.fanruan.com/hugh/open-JSD-7615)
