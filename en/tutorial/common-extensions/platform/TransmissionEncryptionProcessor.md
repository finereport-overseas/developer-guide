# TransmissionEncryptionProcessor

| Property | Value |
| --- | --- |
| Interface Type | extra-decision |
| Full Class Name | `com.fr.decision.fun.TransmissionEncryptionProcessor` |

## Background and Use Cases

In earlier versions of the decision platform, sensitive data such as user passwords were transmitted without encryption or with only base64 encoding. Version 10.0 introduced the `TransmissionDecryptProvider` interface to support encrypted transmission, but since encryption is a reversible operation, the decryption order could not be guaranteed when multiple plugins coexisted. In April 2021, the official team replaced it with the exclusive `TransmissionEncryptionProcessor` interface to improve reliability. The old interface has been deprecated.

## Interface Definition

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

Deprecated legacy interface (retained for backward compatibility only):

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
    <TransmissionEncryptionProcessor class="your class name"/>
</extra-decision>
```

To maintain backward compatibility with older versions (not recommended):

```xml
<extra-decision>
    <TransmissionEncryptionProcessor class="your class name"/>
    <TransmissionDecryptProvider class="your class name"/>
</extra-decision>
```

## How It Works

Implementations are retrieved via:
- `ExtraDecisionClassManager.getInstance().getSingle(TransmissionEncryptionProcessor.MARK_STRING)`
- `getArray(TransmissionDecryptProvider.MARK_STRING)`

The system uses `TransmissionTool` to call these implementations to encrypt/decrypt sensitive information in transit.

`TransmissionEncryptionProcessor` is `Immutable` (exclusive), meaning only one plugin implementation can be active in the environment at a time.

## Useful Links

- Demo: [demo-transmission-decrypt-provider](https://code.fanruan.com/hugh/demo-transmission-decrypt-provider)

## Open Source Examples

> Disclaimer: All open source examples in this documentation are developed and provided by individual developers for reference and learning purposes only. Neither the developers nor the official team are obligated to provide teaching or guidance on these examples. Commercial use is strictly prohibited; any consequences arising from commercial use are solely the responsibility of the user.

- [decision-transmission-decrypt](https://code.fanruan.com/fanruan/decision-transmission-decrypt)
