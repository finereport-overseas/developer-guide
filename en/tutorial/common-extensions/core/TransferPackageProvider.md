# TransferPackageProvider

| Property | Value |
| --- | --- |
| Interface Type | extra-core |
| Full Class Name | `com.fr.stable.fun.TransferPackageProvider` |

## Background and Use Cases

During the upgrade process from older to newer versions of FanRuan Report, extensive code refactoring was performed, including many package moves. This causes templates from older versions to fail with "class not found" errors when running on a newer version server.

The `TransferPackageProvider` interface provides class name conversion: when reading the `class` attribute from cpt/frm files, it maps old class names to new ones.

For example, if a template contains `<ExampleAttr class="com.fr.xxx.MyClass"/>` and `MyClass` has been moved to `com.fr.xxx.moved.MyClass`, this interface can be used to perform the substitution automatically.

Beyond package migration compatibility, this interface can also intercept embedded report objects via class substitution in special cases to influence execution logic.

## Interface Definition

```java
package com.fr.stable.fun;

import com.fr.stable.fun.mark.Mutable;

/**
 * Class name converter for package migration compatibility
 *
 * Created by loy on 2017/1/18.
 */
public interface TransferPackageProvider extends Mutable {

    String XML_TAG = "TransferPackageProvider";

    int CURRENT_LEVEL = 1;

    /**
     * Whether the given class name should be converted by this transformer
     * @param oldClassName the old class name read from the file
     */
    boolean accept(String oldClassName);

    /**
     * Returns the new class name to use as a replacement
     * @param oldClassName the old class name read from the file
     */
    String transfer(String oldClassName);
}
```

## Supported Versions

| Product | Version | Support |
| --- | --- | --- |
| FR | 8.0 | Supported |
| FR | 9.0 | Supported |
| FR | 10.0 | Supported |
| FR | 11.0 | Supported |

## Plugin Registration

Add the following node in `plugin.xml`:

```xml
<extra-core>
    <TransferPackageProvider class="your class name"/>
</extra-core>
```

## How It Works

The interface takes effect through two main code paths:

1. **`TableDataXmlUtils`**: Applies transformation during dataset object deserialization
2. **`GeneralXMLTools`**: Applies transformation when parsing XMLable objects via `readXMLable()` and `readStringAsXMLable()`

**Special limitation:** Not all `class` elements in cpt/frm files are covered by this interface — only elements read via `GeneralXMLTools.readXMLable` / `readStringAsXMLable` and template dataset objects are affected.

## Useful Links

- Demo: [demo-transfer-package-provider](https://code.fanruan.com/hugh/demo-transfer-package-provider)
