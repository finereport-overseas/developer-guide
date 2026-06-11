# EmailSendProvider

| 属性 | 值 |
| --- | --- |
| 接口类型 | extra-decision |
| 完整类名 | `com.fr.decision.fun.EmailSendProvider` |

## 背景与场景

`EmailSendProvider` 主要用于在报表预览和定时调度发送邮件前进行拦截，适用于邮件转发、监控、流量和安全监控等场景。

## 接口定义

```java
package com.fr.decision.fun;

import com.fr.decision.basic.EmailAttr;
import com.fr.report.ResultWorkBookProvider;
import com.fr.stable.fun.mark.Mutable;
import org.jetbrains.annotations.Nullable;

public interface EmailSendProvider extends Mutable {
    String MARK_STRING = "EmailSendProvider";
    int CURRENT_LEVEL = 1;

    boolean sendMail(@Nullable ResultWorkBookProvider resultWorkBook,
                     EmailAttr emailAttr) throws Exception;

    boolean accept(@Nullable ResultWorkBookProvider resultWorkBook);

    boolean contentFormatRequired(ContentFormat format);

    enum ContentFormat {
        HTML,
        IMAGE
    }
}
```

`EmailAttr` 包含邮件属性：收件人（to/cc/bcc）、发件人、主题、正文、是否显示模板、附件列表及可扩展属性映射等。

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
    <EmailSendProvider class="your class name"/>
</extra-decision>
```

## 原理说明

系统通过 `ExtraDecisionClassManager.getInstance().getArray(EmailSendProvider.MARK_STRING)` 获取插件实现。在 `EmailDealWithAction` 和 `EmailPreviewHTML` 中，邮件发送前会触发已注册实现的调用。

**限制说明**：在定时调度场景中，`ResultWorkBookProvider` 的值为 `null`，因此基于报表结果的内容修改仅限于预览场景。

## 常用链接

- Demo：[demo-email-send-provider](https://code.fanruan.com/hugh/demo-email-send-provider)
