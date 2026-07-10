# UniversalConnectionProvider

| Property | Value |
| --- | --- |
| Interface Type | extra-decision |
| Full Class Name | `com.fr.decision.fun.UniversalConnectionProvider` |

## Background and Use Cases

Data sources abstract configuration details away from individual datasets. Through this interface, custom connection types (such as Redis or a custom JDBC pool) can be managed uniformly on the platform side, eliminating the need to embed connection configuration in every dataset.

## Interface Definition

```java
package com.fr.decision.fun;

import com.fr.json.JSONObject;
import com.fr.stable.fun.mark.Mutable;
import com.fr.stable.fun.mark.WebCoalition;

public interface UniversalConnectionProvider<T extends Connection>
        extends Mutable, WebCoalition {

    String MARK_STRING = "UniversalConnectionProvider";
    int CURRENT_LEVEL = 1;

    String nameForConnection();

    String iconPathForConnection();

    Class<T> classForConnection();

    JSONObject serialize(T t);

    T deserialize(T oldConnection, JSONObject object);
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
    <UniversalConnectionProvider class="your class name"/>
</extra-decision>
```

## Notes

- `deserialize()` must return a new instance; it must not return the old connection reference
- The return value of `nameForConnection()` must exactly match the `databaseType` registered on the frontend
- The current frontend UI does not support directly modifying connection names

## Useful Links

- Demo: [demo-connection-provider](https://code.fanruan.com/hugh/demo-connection-provider)

## Open Source Examples

> Disclaimer: All open source examples in this documentation are developed and provided by individual developers for reference and learning purposes only. Neither the developers nor the official team are obligated to provide teaching or guidance on these examples. Commercial use is strictly prohibited; any consequences arising from commercial use are solely the responsibility of the user.

- [demo-tabledata-redis](https://code.fanruan.com/fanruan/demo-tabledata-redis)
