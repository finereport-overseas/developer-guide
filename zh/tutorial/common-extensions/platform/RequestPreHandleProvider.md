# RequestPreHandleProvider

| 属性 | 值 |
| --- | --- |
| 接口类型 | extra-decision |
| 完整类名 | `com.fr.decision.fun.RequestPreHandleProvider` |

## 背景与场景

`RequestPreHandleProvider` 是 10.0 早期版本中基于 Spring `HandlerInterceptorAdapter` 开放的请求预处理插件接口，用于请求信息补充、转换和过滤，类似 Filter 但在 Filter 之后执行。

> **注意**：该接口因设计缺陷已被标记为废弃（Deprecated），仅为向后兼容而保留。新开发建议使用 `EmbedRequestFilterProvider` 或 `GlobalRequestFilterProvider`。

## 接口定义

```java
package com.fr.decision.fun;

import com.fr.stable.fun.mark.Mutable;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

public interface RequestPreHandleProvider extends Mutable {
    String MARK_STRING = "RequestPreHandleProvider";
    int CURRENT_LEVEL = 1;

    /**
     * 请求是否需要预处理
     */
    boolean accept(HttpServletRequest req);

    /**
     * 请求预处理业务
     */
    boolean preHandle(HttpServletRequest req, HttpServletResponse res);
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
    <RequestPreHandleProvider class="your class name"/>
</extra-decision>
```

## 原理说明

MVC 框架初始化包含 `RequestPreHandleInterceptor` 的拦截器，该拦截器调用已实现的 `RequestPreHandleProvider` 接口方法。

## 注意事项

- 每个请求只有**一个实现实例**执行（第一个 `accept()` 成功的实例生效，其余跳过）
- 仅拦截 `/decision/*` 请求，**不包含** `URLAliasProvider` 短链（`/url/**`）和部署信息端点（`/v10/deployment/**`）
- 这是拦截器接口，**不能用于暴露新的 Web 服务端点**

## 常用链接

- Demo：[demo-request-pre-handler](https://code.fanruan.com/hugh/demo-request-pre-handler)
