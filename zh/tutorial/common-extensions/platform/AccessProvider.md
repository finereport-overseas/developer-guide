# AccessProvider

| 属性 | 值 |
| --- | --- |
| 接口类型 | extra-decision |
| 完整类名 | `com.fr.decision.fun.AccessProvider` |

## 背景与场景

`AccessProvider` 接口用于扩展第三方认证方式，通过隐式登录的方式接入外部身份验证。主要面向登录场景（而非单点登录 SSO），例如：移动端第三方唤起登录集成，或直接调用第三方授权接口完成认证。

## 接口定义

```java
package com.fr.decision.fun;

import com.fr.stable.fun.mark.Mutable;

public interface AccessProvider extends Mutable {
    String MARK_STRING = "AccessProvider";
    int CURRENT_LEVEL = 1;

    /**
     * 自定义登录验证方法
     * @param username 用户名
     * @param password 密码
     * @param ticket   第三方认证凭证
     * @return 是否通过登录验证
     */
    boolean access(String username, String password, String ticket);

    /**
     * 自定义登录验证方法
     * @param username 用户名
     * @param password 密码
     * @param ticket   第三方认证凭证
     * @return 用户名
     */
    String auth(String username, String password, String ticket);
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
    <AccessProvider class="your class name"/>
</extra-decision>
```

## 原理说明

平台的登录服务入口为 `LoginResource`，包含 `POST /login/third/auth` 端点，接受 `ThirdAuthInfoBean`。该端点调用 `LoginService#login()`，传入凭证和 token，随后调用所有已注册的 `AccessProvider` 实例进行认证验证。

## 常用链接

- Demo：[demo-access-provider](https://code.fanruan.com/hugh/demo-access-provider)
