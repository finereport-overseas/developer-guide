# LogInOutEventProvider

| 属性 | 值 |
| --- | --- |
| 接口类型 | extra-decision |
| 完整类名 | `com.fr.decision.fun.LogInOutEventProvider` |

## 背景与场景

该接口用于监控和管理用户登录/登出行为，支持**事件监听、消息推送和登出后重定向到指定登录页**等场景。

## 接口定义

```java
package com.fr.decision.fun;

import com.fr.stable.fun.mark.Mutable;

public interface LogInOutEventProvider extends Mutable {
    String MARK_STRING = "LogInOutEventProvider";
    int CURRENT_LEVEL = 2;

    void loginAction(LogInOutResultInfo result);

    String logoutAction(LogInOutResultInfo result);
}
```

```java
public class LogInOutResultInfo {
    private HttpServletRequest request;
    private HttpServletResponse response;
    private String username;
    private boolean result;
    // 构造方法及 getter/setter 省略
}
```

## 支持版本

| 产品线 | 版本 | 支持情况 | 备注 |
| --- | --- | --- | --- |
| FR | 10.0 | 支持 | 需 10.0.3+；更早版本使用 LoginEventProvider |
| FR | 11.0 | 支持 |
| BI | 5.1 | 支持 | — |
| BI | 5.1.2 | 支持 | — |
| BI | 5.1.3 | 支持 | — |

## 插件注册

在 `plugin.xml` 中添加以下节点：

```xml
<extra-decision>
    <LogInOutEventProvider class="your class name"/>
</extra-decision>
```

## 原理说明

`WebServiceActivator` 在启动时读取所有声明的实现并注册到 LOGIN/LOGOUT 事件。在 `LoginResource` 执行标准登录或验证码登录/登出方法后触发。

**注意**：不要在 `logoutAction` 中使用响应重定向来实现登录后跳转——登出请求是 AJAX 调用，重定向不会生效。

## 常用链接

- Demo：[demo-login-out-event-provider](https://code.fanruan.com/hugh/demo-login-out-event-provider)

## 开源案例

> 免责声明：所有文档中的开源示例均为开发者自行开发并提供，仅供参考和学习使用。开发者和官方均无义务对开源案例进行教学和指导。禁止用于任何商业用途，若作为商用，一切后果责任由使用者自行承担。

- [open-JSD-7957](https://code.fanruan.com/hugh/open-JSD-7957)
- [open-JSD-7858](https://code.fanruan.com/hugh/open-JSD-7858)
- [open-JSD-7706](https://code.fanruan.com/hugh/open-JSD-7706)
