# SystemOptionProvider

| Property | Value |
| --- | --- |
| Interface Type | extra-decision |
| Full Class Name | `com.fr.decision.fun.SystemOptionProvider` |

## Background and Use Cases

The decision platform management system provides a wide range of administrative operations. `SystemOptionProvider` allows developers to extend custom management menu items. This is a functional interface (rather than a business-specific one), suitable for all platform extension scenarios unrelated to particular business logic.

## Interface Definition

```java
package com.fr.decision.fun;

import com.fr.stable.fun.mark.WebCoalition;

/**
 * Decision platform management system menu extension item
 */
public interface SystemOptionProvider extends WebCoalition {

    String XML_TAG = "SystemOptionProvider";
    int CURRENT_LEVEL = 1;

    String id();
    String parentId();
    String fullPath();
    String displayName();
    int sortIndex();
}
```

Frontend menu registration example:

```javascript
BI.config("dec.constant.management.navigation", function (items) {
    items.push({
        value: "hash value in the address bar",
        id: "backend module ID for the menu",
        pId: "parent node ID, optional for root-level items",
        text: "display text for the menu item",
        cardType: "component type for the main view of this menu",
        cls: "management-directory-font"
    });
    return items;
});
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
    <SystemOptionProvider class="your class name"/>
</extra-decision>
```

## How It Works

Backend management permissions and frontend page declarations are implemented separately. The backend manages permissions, while the frontend declares the management pages.

## Notes

- The backend `id`/`parentId` and frontend `id`/`pId` must **exactly match**; mismatches will cause permission issues
- `fullPath` is used for backend permission validation, format: `root-_-parent`
- Only data connections and smart operations support child node extension; extending other functions risks breaking existing functionality
- This interface typically needs to be used in conjunction with frontend resource interfaces, database access providers, service interfaces, and internationalization interfaces

## Useful Links

- Demo: [demo-system-option-provider](https://code.fanruan.com/hugh/demo-system-option-provider)

## Open Source Examples

> Disclaimer: All open source examples in this documentation are developed and provided by individual developers for reference and learning purposes only. Neither the developers nor the official team are obligated to provide teaching or guidance on these examples. Commercial use is strictly prohibited; any consequences arising from commercial use are solely the responsibility of the user.

- [demo-system-management](https://code.fanruan.com/fanruan/demo-system-management)
