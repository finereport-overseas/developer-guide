# MessageEventProvider

| Property | Value |
| --- | --- |
| Interface Type | extra-decision |
| Full Class Name | `com.fr.decision.fun.MessageEventProvider` |

## Background and Use Cases

The decision platform has a built-in user message notification feature. When a third-party system needs to synchronize messages bidirectionally with the platform, this interface can be used to listen for platform message send events, enabling forwarding to external systems or triggering additional callbacks.

## Interface Definition

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

The `message` parameter corresponds to a `Message` object containing the following fields: `userId`, `username`, `datetime`, `createTime`, `message` (message text), `url`, `toasted`, `readed`, `type`, `urlType`.

## Supported Versions

| Product Line | Version | Support |
| --- | --- | --- |
| FR | 10.0 | Supported |
| FR | 11.0 | Supported |
| BI | 5.1 | Supported |
| BI | 5.1.2 | Supported |
| BI | 5.1.3 | Supported |

## Plugin Registration

Add the following node to `plugin.xml`:

```xml
<extra-decision>
    <MessageEventProvider class="your class name"/>
</extra-decision>
```

## How It Works

All implementations are retrieved via `ExtraDecisionClassManager.getInstance().getArray(MessageEventProvider.MARK_STRING)`. `WebServiceActivator` listens for the `MessageEvent.HANDLE` event, which is triggered after `MessageService` saves a message.

## Notes

- Only the message **send** event can be monitored; events such as message read status changes are not observable

## Useful Links

- Demo: [demo-message-event-provider](https://code.fanruan.com/hugh/demo-message-event-provider)

## Open Source Examples

> Disclaimer: All open source examples in this documentation are developed and provided by individual developers for reference and learning purposes only. Neither the developers nor the official team are obligated to provide teaching or guidance on these examples. Commercial use is strictly prohibited; any consequences arising from commercial use are solely the responsibility of the user.

- [decision-message-event](https://code.fanruan.com/fanruan/decision-message-event)
