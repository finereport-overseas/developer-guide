# MessageObjectProvider

| 属性 | 值 |
| --- | --- |
| 接口类型 | provider |
| 所属模块 | extra-report |
| 完整类名 | `com.fr.report.fun.MessageObjectProvider` |
| 官方文档 | [查看文档](https://wiki.fanruan.com/display/PD/com.fr.report.fun.MessageObjectProvider) |

## 简介

专用于模板消息插件，为微信、钉钉等第三方消息平台提供统一的消息发送接入点。通过实现该接口，可将报表推送消息对接到自定义的消息渠道。

## 接口定义

```java
public interface MessageObjectProvider extends Mutable {

    String MARK_STRING = "MessageObjectProvider";

    int CURRENT_LEVEL = 1;

    /**
     * 消息类型（用于标识当前实现对应的消息渠道）
     */
    MessageType getMessageType();

    /**
     * 需要嵌入的组件内容（用于在配置界面中显示可编辑项）
     */
    Pair<String, String>[] getValueEditorItems();

    /**
     * 实现发送消息
     *
     * @param messageContent 消息配置内容
     */
    void sendMessage(MessageImage messageContent);
}
```

### `MessageImage` 关联接口

`sendMessage()` 方法接收的 `MessageImage` 对象包含消息所需的全部上下文：

| 方法 | 说明 |
| --- | --- |
| `getTitle()` | 消息标题 |
| `getContent()` | 消息正文内容 |
| `getLink()` | 消息跳转链接 |
| `getImage()` | 消息附图 |
| `getSenderId()` | 收件人用户 ID（数组形式，使用时注意解析） |
| `getApplication()` | 应用信息 |

## 使用方式

在插件的 `plugin.xml` 中，通过 `extra-report` 标签注册实现类：

```xml
<extra-report>
    <MessageObjectProvider class="com.fr.plugin.demo.message.WeChatMessageObject"/>
</extra-report>
```

## 示例代码

```java
public class WeChatMessageObject implements MessageObjectProvider {

    @Override
    public MessageType getMessageType() {
        // 返回自定义消息类型标识
        return MessageType.WECHAT;
    }

    @Override
    public Pair<String, String>[] getValueEditorItems() {
        // 返回在配置界面中展示的可编辑项（key-label 对）
        return new Pair[]{
            new Pair<>("corpId", "企业 ID"),
            new Pair<>("agentId", "应用 AgentID")
        };
    }

    @Override
    public void sendMessage(MessageImage messageContent) {
        String[] receivers = messageContent.getSenderId().split(",");
        for (String receiver : receivers) {
            // 调用企业微信 API 向每位收件人发送消息
            WeChatApi.send(receiver, messageContent.getTitle(), messageContent.getContent());
        }
    }
}
```

## 参考示例

官方示例源码：[demo-message-provider](http://cloud.finedevelop.com:2015/projects/FD/repos/demo-message-provider/browse)

## 注意事项

- `getSenderId()` 返回的收件人为逗号分隔的字符串，实现时需按数组处理，逐一发送消息。
- 该接口仅用于模板消息推送场景，不适用于其他类型的通知渠道。
