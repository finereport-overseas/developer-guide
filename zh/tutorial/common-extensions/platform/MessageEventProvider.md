# MessageEventProvider

| 属性 | 值 |
| --- | --- |
| 接口类型 | extra-decision |
| 完整类名 | `com.fr.decision.fun.MessageEventProvider` |

## 背景与场景

决策平台内置了用户消息提醒功能。当第三方系统需要与平台进行双向消息同步时，可通过该接口监听平台的消息发送事件，实现转发至外部系统或触发回调等附加处理。

## 接口定义

```java
package com.fr.decision.fun;

import com.fr.stable.fun.mark.Mutable;
import java.util.Map;

public interface MessageEventProvider extends Mutable {
    String MARK_STRING = "MessageEventProvider";
    int CURRENT_LEVEL = 1;

    void handleMessage(Map<String, Object> message);
}
```

`message` 参数对应 `Message` 对象，包含以下字段：`userId`、`username`、`datetime`、`createTime`、`message`（消息文本）、`url`、`toasted`、`readed`、`type`、`urlType`。

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
    <MessageEventProvider class="your class name"/>
</extra-decision>
```

## 原理说明

通过 `ExtraDecisionClassManager.getInstance().getArray(MessageEventProvider.MARK_STRING)` 获取所有实现。`WebServiceActivator` 监听 `MessageEvent.HANDLE` 事件，该事件在 `MessageService` 保存消息后触发。

## 注意事项

- 仅能监听消息**发送**事件，消息已读状态等处理事件不可观测

## 常用链接

- Demo：[demo-message-event-provider](https://code.fanruan.com/hugh/demo-message-event-provider)

## 开源案例

> 免责声明：所有文档中的开源示例均为开发者自行开发并提供，仅供参考和学习使用。开发者和官方均无义务对开源案例进行教学和指导。禁止用于任何商业用途，若作为商用，一切后果责任由使用者自行承担。

- [decision-message-event](https://code.fanruan.com/fanruan/decision-message-event)
