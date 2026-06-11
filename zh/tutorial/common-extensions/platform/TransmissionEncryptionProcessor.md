# TransmissionEncryptionProcessor

| 属性 | 值 |
| --- | --- |
| 接口类型 | extra-decision |
| 完整类名 | `com.fr.decision.fun.TransmissionEncryptionProcessor` |

## 背景与场景

早期决策平台在传输用户密码等敏感数据时未加密或仅使用 base64 编码。10.0 版本引入了 `TransmissionDecryptProvider` 接口支持加密传输，但由于加密是可逆操作，多个插件共存时解密顺序难以保证。2021 年 4 月，官方改为独占式的 `TransmissionEncryptionProcessor` 接口以提高可靠性，旧接口已废弃。

## 接口定义

```java
package com.fr.decision.fun;

import com.fr.stable.fun.mark.Immutable;
import com.fr.stable.fun.mark.WebCoalition;

public interface TransmissionEncryptionProcessor extends Immutable, WebCoalition {
    String MARK_STRING = "TransmissionEncryptionProcessor";
    int CURRENT_LEVEL = 1;

    String encrypt(String plainInfo);
    String decrypt(String encryptionInfo);
}
```

已废弃的旧接口（仅向后兼容保留）：

```java
package com.fr.decision.fun;

import com.fr.stable.fun.mark.WebCoalition;

@Deprecated
public interface TransmissionDecryptProvider extends WebCoalition {
    String MARK_STRING = "TransmissionDecryptProvider";
    int CURRENT_LEVEL = 2;

    String decrypt(String encryptionInfo);
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
    <TransmissionEncryptionProcessor class="your class name"/>
</extra-decision>
```

若需同时兼容旧版本（不推荐）：

```xml
<extra-decision>
    <TransmissionEncryptionProcessor class="your class name"/>
    <TransmissionDecryptProvider class="your class name"/>
</extra-decision>
```

## 原理说明

实现通过以下方式获取：
- `ExtraDecisionClassManager.getInstance().getSingle(TransmissionEncryptionProcessor.MARK_STRING)`
- `getArray(TransmissionDecryptProvider.MARK_STRING)`

系统通过 `TransmissionTool` 调用这些实现对传输中的敏感信息进行加密/解密。

`TransmissionEncryptionProcessor` 为 `Immutable`（排他性），环境中只能有一个插件实现生效。

## 常用链接

- Demo：[demo-transmission-decrypt-provider](https://code.fanruan.com/hugh/demo-transmission-decrypt-provider)

## 开源案例

> 免责声明：所有文档中的开源示例均为开发者自行开发并提供，仅供参考和学习使用。开发者和官方均无义务对开源案例进行教学和指导。禁止用于任何商业用途，若作为商用，一切后果责任由使用者自行承担。

- [decision-transmission-decrypt](https://code.fanruan.com/fanruan/decision-transmission-decrypt)
