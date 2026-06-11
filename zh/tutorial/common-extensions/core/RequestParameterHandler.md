# RequestParameterHandler

| 属性 | 值 |
| --- | --- |
| 接口类型 | extra-core |
| 完整类名 | `com.fr.stable.fun.RequestParameterHandler` |

## 背景与场景

系统通过 `WebUtils` 工具类获取请求参数，支持参数解析、转换、添加/删除及权限过滤。`RequestParameterHandler` 主要用于报表参数的自定义处理场景。

## 接口定义

```java
package com.fr.stable.fun;

import com.fr.stable.fun.mark.Immutable;
import javax.servlet.http.HttpServletRequest;

public interface RequestParameterHandler extends Immutable {

    String XML_TAG = "RequestParameterHandler";
    int CURRENT_LEVEL = 1;

    Object getParameterFromRequest(HttpServletRequest req, String name);
    Object getParameterFromRequestInputStream(HttpServletRequest req, String name);
    Object getParameterFromAttribute(HttpServletRequest req, String name);
    Object getParameterFromJSONParameters(HttpServletRequest req, String name);
    Object getParameterFromSession(HttpServletRequest req, String name);
    Object getParameterFromHeader(HttpServletRequest req, String name);
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
    <RequestParameterHandler class="your class name"/>
</extra-core>
```

## 原理说明

`WebUtils` 是读取请求信息和参数的标准工具类，通过 `NetworkHelper` 获取 `RequestParameterHandler` 实例；若无注册，则使用 `DefaultRequestParameterHandler`。

**注意**：建议继承 `DefaultRequestParameterHandler` 而非 `AbstractRequestParameterHandler`，通过适配器模式只覆盖必要的方法。所有请求信息的访问必须使用 `WebUtils` 或 finekit 方法，以保证兼容性。

## 常用链接

- Demo：[demo-request-parameter-handler](https://code.fanruan.com/hugh/demo-request-parameter-handler)
