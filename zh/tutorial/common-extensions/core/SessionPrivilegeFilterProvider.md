# SessionPrivilegeFilterProvider

| 属性 | 值 |
| --- | --- |
| 接口类型 | extra-core |
| 完整类名 | `com.fr.stable.fun.SessionPrivilegeFilterProvider` |

## 背景与场景

该接口在早期 FineReport 产品中边界较为模糊，涵盖了多种场景：sessionID 暴力破解防护、响应增强、Cookie 加密/解密以及报表请求预处理等。在 10.0 版本中，sessionID 已具备更强的抗破解能力，响应增强和 Cookie 操作已迁移至其他接口，报表预处理也有了替代实现。

## 接口定义

```java
package com.fr.stable.fun;

import com.fr.stable.fun.mark.Mutable;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

public interface SessionPrivilegeFilterProvider extends Mutable {
    String XML_TAG = "SessionPrivilegeFilterProvider";
    int CURRENT_LEVEL = 1;

    void addSecurityResponseHeader(HttpServletResponse res);
    void filterSession(HttpServletRequest req, HttpServletResponse res);
    String encodeSessionID(String sessionID);
    String encodeCookie(String cookieStr);
    String decodeCookie(String encodeStr);
}
```

## 支持版本

| 产品线 | 版本 | 支持情况 | 备注 |
| --- | --- | --- | --- |
| FR | 8.0 | 支持 | — |
| FR | 9.0 | 支持 | — |
| FR | 10.0 | 支持 | 仅部分方法生效 |
| FR | 11.0 | 支持 |

## 插件注册

在 `plugin.xml` 中添加以下节点：

```xml
<extra-core>
    <SessionPrivilegeFilterProvider class="your class name"/>
</extra-core>
```

## 原理说明

通过 `PluginModule.getAgent(PluginModule.ExtraCore).getArray()` 获取实现。在 sessionInfo 创建和操作请求分发时激活（9.0 及更早版本）。

**10.0 版本限制**：仅以下两个方法生效：
- `filterSession()`：在 Session 操作请求分发时调用
- `encodeSessionID()`：在 Session 创建时、sessionID 绑定前调用

## 常用链接

- Demo：[demo-session-privilege-filter-provider](https://code.fanruan.com/hugh/demo-session-privilege-filter-provider)
