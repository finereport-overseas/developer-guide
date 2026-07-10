# EmailSendProvider

| Property | Value |
| --- | --- |
| Interface Type | extra-decision |
| Full Class Name | `com.fr.decision.fun.EmailSendProvider` |

## Background and Use Cases

`EmailSendProvider` is primarily used to intercept emails before they are sent during report preview and scheduled task delivery. Applicable scenarios include email forwarding, monitoring, traffic management, and security auditing.

## Interface Definition

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

`EmailAttr` contains email properties: recipients (to/cc/bcc), sender, subject, body, whether to display the template, attachment list, and an extensible attribute map.

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
    <EmailSendProvider class="your class name"/>
</extra-decision>
```

## How It Works

The system retrieves plugin implementations via `ExtraDecisionClassManager.getInstance().getArray(EmailSendProvider.MARK_STRING)`. In `EmailDealWithAction` and `EmailPreviewHTML`, registered implementations are invoked before an email is sent.

**Limitation**: In scheduled task scenarios, `ResultWorkBookProvider` is `null`, so content modifications based on report results are only available in the preview scenario.

## Useful Links

- Demo: [demo-email-send-provider](https://code.fanruan.com/hugh/demo-email-send-provider)
