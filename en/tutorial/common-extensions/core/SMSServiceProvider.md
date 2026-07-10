# SMSServiceProvider

| Property | Value |
| --- | --- |
| Interface Type | extra-core |
| Full Class Name | `com.fr.stable.fun.SMSServiceProvider` |

## Background and Use Cases

FanRuan's decision-making platform introduced SMS service functionality starting from version 9.0. However, some users already have their own SMS service providers and cannot use FanRuan's built-in SMS service directly. For this reason, version 10.0 opened the `SMSServiceProvider` interface to support integration with third-party SMS services.

## Interface Definition

```java
package com.fr.stable.fun;

import com.fr.json.JSONArray;
import com.fr.json.JSONObject;
import com.fr.stable.fun.mark.Selectable;
import java.util.List;
import java.util.Map;

public interface SMSServiceProvider extends Selectable {

    int CURRENT_LEVEL = 1;
    String XML_TAG = "SMSServiceProvider";

    /**
     * Maps FanRuan SMS template IDs to third-party platform templates
     */
    Map<String, String> mapping();

    /**
     * Sends a test SMS
     */
    Response sendTest(String mobile);

    /**
     * Sends an SMS with template parameter substitution
     */
    Response send(String template, String mobile, JSONObject para, String receiver) throws Exception;

    /**
     * Sends SMS messages in bulk
     */
    Response batchSendSMS(String template, List<String> mobiles, JSONArray params,
                          List<String> receivers) throws Exception;

    class Response {
        public final static String RES_STATUS_SUCCESS = "success";
        public final static String RES_STATUS_FAILED = "failed";

        private String status;
        private String msg;
        private JSONObject content;

        // getters/setters and factory method omitted
    }

    interface Listener {
        void beforeSend(String text, List<String> mobiles, JSONArray params,
                        List<String> receivers);
        void afterSend(String text, List<String> mobiles, JSONArray params,
                       List<String> receivers, Response response);
    }
}
```

The `mapping()` method must map FanRuan's built-in SMS template IDs to the corresponding templates on the third-party platform. Common template ID examples:

| Feature | Template ID | Default Template Content |
| --- | --- | --- |
| Login verification code | 20 | Verification code: #verifiecode# |
| Scheduled task failure alert | 10 | Task #taskname# failed at #time# |
| Test SMS | 18 | SMS service configured successfully |
| Platform alert | 17 | #webname# memory alert |

## Supported Versions

| Product | Version | Support |
| --- | --- | --- |
| FR | 10.0 | Supported |
| FR | 11.0 | Supported |

## Plugin Registration

Add the following node in `plugin.xml`:

```xml
<extra-core>
    <SMSServiceProvider class="your class name"/>
</extra-core>
```

## How It Works

All third-party SMS extensions are retrieved via:

```java
Set<SMSServiceProvider> providers = PluginModule.ExtraCore.getAgent()
    .getArray(SMSServiceProvider.XML_TAG);
```

The SMS Manager serves as the main entry point, checking plugin extensions first and falling back to the built-in service if none are found.

**Notes:**
1. A working FanRuan SMS service connection is required
2. A complete template ID mapping must be provided in `mapping()`
3. The before/after listener events only take effect for third-party services

## Useful Links

- Demo (10.0): [demo-sms-provider](https://code.fanruan.com/hugh/demo-sms-provider)
- Demo (11.0): [demo-third-sms-v2](https://code.fanruan.com/pioneer/demo-third-sms-v2)

## Open Source Examples

> Disclaimer: All open source examples in the documentation are developed and provided by developers for reference and learning purposes only. Developers and the official team have no obligation to provide instruction or guidance on open source examples. Commercial use is strictly prohibited; any consequences from commercial use are the sole responsibility of the user.

- [open-JSD-8144](https://code.fanruan.com/hugh/open-JSD-8144)
- [demo-third-sms](https://code.fanruan.com/fanruan/demo-third-sms)
