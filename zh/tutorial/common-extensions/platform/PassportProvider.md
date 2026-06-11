# PassportProvider

| 属性 | 值 |
| --- | --- |
| 接口类型 | extra-decision |
| 完整类名 | `com.fr.decision.fun.PassportProvider` |

## 背景与场景

帆软决策平台提供三种默认认证方式：平台内置认证、LDAP 认证和 HTTP 认证。`PassportProvider` 接口允许开发者扩展完全自定义的认证方式，以满足特定业务需求。

## 接口定义

`PassportProvider` 定义了三个核心方法：

- `passportType()`：返回认证类型标识符
- `classForPassportBean()`：注册业务对象类
- `classForPassportConfig()`：注册数据库配置类

`Passport` 接口处理认证逻辑，通过 `checkTicket()` 方法验证用户名、输入密码、保存密码和哈希密码。

`PassportBean` 是管理认证配置的抽象类，包含类型和用户创建来源属性，以及抽象方法 `markType()`、`createPassportBean()` 和 `createPassport()`。

## 支持版本

| 产品线 | 版本 | 支持情况 | 备注 |
| --- | --- | --- | --- |
| FR | 10.0 | 支持 | — |
| FR | 11.0 | 支持 |
| BI | 5.1 | 支持 | 不支持仪表板 |
| BI | 5.1.2 | 支持 | 不支持仪表板 |
| BI | 5.1.3 | 支持 | 不支持仪表板 |

## 插件注册

在 `plugin.xml` 中添加以下节点：

```xml
<extra-decision>
    <WebResourceProvider class="your classname"/>
    <PassportProvider class="your classname"/>
</extra-decision>
```

## 原理说明

插件通过 `ExtraDecisionClassManager.getInstance().getArray()` 加载。前端配置流转到后端存储后，系统优先匹配内置类型，再匹配插件声明的扩展类型。

## 注意事项

- `passportType()`、`markType()` 的返回值必须与前端配置标识符完全匹配
- 该接口仅处理认证，不涉及单点登录（SSO）
- 实现该接口后，用户同步和密码管理功能将不可用

## 常用链接

- Demo：[demo-passport-provider](https://code.fanruan.com/hugh/demo-passport-provider)

## 开源案例

> 免责声明：所有文档中的开源示例均为开发者自行开发并提供，仅供参考和学习使用。开发者和官方均无义务对开源案例进行教学和指导。禁止用于任何商业用途，若作为商用，一切后果责任由使用者自行承担。

- [demo-custom-passport](https://code.fanruan.com/fanruan/demo-custom-passport)
- [demo-ldaps-passport](https://code.fanruan.com/fanruan/demo-ldaps-passport)
