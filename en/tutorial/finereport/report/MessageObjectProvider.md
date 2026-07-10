# MessageObjectProvider

| Property | Value |
| --- | --- |
| Interface Type | provider |
| Module | extra-report |
| Full Class Name | `com.fr.report.fun.MessageObjectProvider` |
| Official Docs | [View Documentation](https://wiki.fanruan.com/display/PD/com.fr.report.fun.MessageObjectProvider) |

## Overview

Dedicated to template message plugins, this interface provides a unified message-sending integration point for third-party message platforms such as WeChat and DingTalk. By implementing this interface, report push messages can be routed to a custom messaging channel.

## Interface Definition

```java
public interface MessageObjectProvider extends Mutable {

    String MARK_STRING = "MessageObjectProvider";

    int CURRENT_LEVEL = 1;

    /**
     * Message type (identifies the messaging channel for this implementation)
     */
    MessageType getMessageType();

    /**
     * Embedded component content (configurable items to display in the configuration UI)
     */
    Pair<String, String>[] getValueEditorItems();

    /**
     * Sends a message
     *
     * @param messageContent Message configuration content
     */
    void sendMessage(MessageImage messageContent);
}
```

### `MessageImage` Related Interface

The `MessageImage` object received by `sendMessage()` contains all the context needed for the message:

| Method | Description |
| --- | --- |
| `getTitle()` | Message title |
| `getContent()` | Message body content |
| `getLink()` | Message redirect URL |
| `getImage()` | Message attachment image |
| `getSenderId()` | Recipient user ID (in array form; parse accordingly when using) |
| `getApplication()` | Application information |

## Usage

Register your implementation class in the plugin's `plugin.xml` using the `extra-report` tag:

```xml
<extra-report>
    <MessageObjectProvider class="com.fr.plugin.demo.message.WeChatMessageObject"/>
</extra-report>
```

## Example Code

```java
public class WeChatMessageObject implements MessageObjectProvider {

    @Override
    public MessageType getMessageType() {
        // Return the custom message type identifier
        return MessageType.WECHAT;
    }

    @Override
    public Pair<String, String>[] getValueEditorItems() {
        // Return the configurable items displayed in the UI (key-label pairs)
        return new Pair[]{
            new Pair<>("corpId", "Enterprise ID"),
            new Pair<>("agentId", "Application AgentID")
        };
    }

    @Override
    public void sendMessage(MessageImage messageContent) {
        String[] receivers = messageContent.getSenderId().split(",");
        for (String receiver : receivers) {
            // Call the Enterprise WeChat API to send a message to each recipient
            WeChatApi.send(receiver, messageContent.getTitle(), messageContent.getContent());
        }
    }
}
```

## Reference Examples

Official example source code: [demo-message-provider](http://cloud.finedevelop.com:2015/projects/FD/repos/demo-message-provider/browse)

## Notes

- `getSenderId()` returns recipients as a comma-separated string; split it into an array and send messages individually.
- This interface is only for template message push scenarios and is not suitable for other types of notification channels.
