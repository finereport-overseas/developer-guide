# EmailServiceProvider

| Property | Value |
| --- | --- |
| Interface Type | extra-core |
| Full Class Name | `com.fr.stable.fun.EmailServiceProvider` |

## Background and Use Cases

This interface is used to replace the product's built-in email sending logic. It is similar in function to `EmailSendProvider`, but `EmailServiceProvider` can only replace the product's own email sending logic, making it more focused. This interface is recommended over `EmailSendProvider`.

## Interface Definition

```java
package com.fr.stable.fun;

import com.fr.stable.email.EmailAttachmentProvider;
import com.fr.stable.fun.mark.Selectable;

import javax.mail.MessagingException;

/**
 * @author Cloud.Liu
 * @version 10.0
 * Created by Cloud.Liu on 2020/5/20
 */
public interface EmailServiceProvider extends Selectable {

    int CURRENT_LEVEL = 1;

    String XML_TAG = "EmailServiceProvider";

    /**
     * Send an email
     *
     * @param toAddress       recipient address
     * @param ccAddress       CC address
     * @param bccAddress      BCC address
     * @param fromAddress     sender address
     * @param subject         subject
     * @param bodyContent     body content
     * @param attaches        attachments
     * @param format          format
     * @param contentAttaches attachments displayed in the email body
     * @throws MessagingException exception
     */
    void send(String toAddress, String ccAddress, String bccAddress, String fromAddress,
              String subject, String bodyContent, EmailAttachmentProvider[] attaches,
              String format, EmailAttachmentProvider[] contentAttaches) throws MessagingException;
}
```

## Supported Versions

| Product | Version | Support |
| --- | --- | --- |
| FR | 10.0 | Supported |
| FR | 11.0 | Supported |
| BI | 5.1.3 | Supported |

## Plugin Registration

Add the following node in `plugin.xml`:

```xml
<extra-core>
    <EmailServiceProvider class="your class name"/>
</extra-core>
```

## How It Works

`EmailManager` implements the selection logic: if no `EmailServiceProvider` implementation is registered, standard email sending is performed; if one is registered, the selector-matched provider handles the email sending; if no match is found, an error is logged.

## Useful Links

- Demo: [demo-email-service-provider](https://code.fanruan.com/hugh/demo-email-service-provider)

## Open Source Examples

> Disclaimer: All open source examples in the documentation are developed and provided by developers for reference and learning purposes only. Developers and the official team have no obligation to provide instruction or guidance on open source examples. Commercial use is strictly prohibited; any consequences from commercial use are the sole responsibility of the user.

- [open-JSD-8153](https://code.fanruan.com/hugh/open-JSD-8153)
