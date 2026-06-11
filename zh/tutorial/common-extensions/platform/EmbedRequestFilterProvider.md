# EmbedRequestFilterProvider

| 属性 | 值 |
| --- | --- |
| 接口类型 | extra-decision |
| 完整类名 | `com.fr.decision.fun.EmbedRequestFilterProvider` |

## 背景与场景

该接口作为 `RequestPreHandleProvider` 的补充，用于请求预处理。它提供了**几乎等同于 Servlet Filter（`javax.servlet.Filter`）的效果**，可直接在插件代码中使用，无需配置 `web.xml`，且支持热重载。

## 接口定义

```java
package com.fr.decision.fun;

import com.fr.stable.fun.mark.Mutable;
import javax.servlet.FilterChain;
import javax.servlet.FilterConfig;
import javax.servlet.ServletException;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

public interface EmbedRequestFilterProvider extends Mutable {
    String MARK_STRING = "EmbedRequestFilterProvider";
    int CURRENT_LEVEL = 2;

    void init(FilterConfig filterConfig);

    void filter(HttpServletRequest req, HttpServletResponse res)
            throws IOException, ServletException;

    void destroy();
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
    <EmbedRequestFilterProvider class="your class name"/>
</extra-decision>
```

## 原理说明

平台的 Servlet 初始化模块在启动时注入一个 Filter，监听插件生命周期以动态缓存 `EmbedRequestFilterProvider` 实例，在 Filter 执行时按顺序调用。

## 注意事项

- 仅过滤 `/decision` 路由（内部 Servlet）
- 无法终止后续逻辑执行（除非抛出异常）
- 仅限请求预处理（Headers、参数调整），**不能重定向、转发或输出响应**
- 无法指定跨插件的相同接口执行顺序

## 常用链接

- Demo：[demo-embed-request-filter](https://code.fanruan.com/hugh/demo-embed-request-filter)

## 开源案例

> 免责声明：所有文档中的开源示例均为开发者自行开发并提供，仅供参考和学习使用。开发者和官方均无义务对开源案例进行教学和指导。禁止用于任何商业用途，若作为商用，一切后果责任由使用者自行承担。

- [open-JSD-7449](https://code.fanruan.com/hugh/open-JSD-7449)
- [open-JSD-7747](https://code.fanruan.com/hugh/open-JSD-7747)
