# ResourcePathTransformer

| Property | Value |
| --- | --- |
| Interface Type | extra-core |
| Full Class Name | `com.fr.stable.fun.ResourcePathTransformer` |

## Background and Use Cases

This interface is primarily used for replacing resource files within the product. Common use cases include: icon replacement in OEM scenarios, migrating report and resource file paths, and replacing built-in HTML templates.

## Interface Definition

```java
package com.fr.stable.fun;

import com.fr.stable.fun.mark.Mutable;

/**
 * @author richie
 * @date 2015-06-09
 * @since 8.0
 * Resource file reading and transformation, primarily for OEM scenarios
 */
public interface ResourcePathTransformer extends Mutable {

    String MARK_STRING = "ResourcePathTransformer";
    int CURRENT_LEVEL = 1;

    boolean accept(String path);

    /**
     * Transforms the resource read path
     * @param path the path to transform
     * @return the new resource file path
     */
    String transform(String path);
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
    <ResourcePathTransformer class="your class name"/>
</extra-core>
```

## How It Works

The implementation is obtained via a `StableFactory` method. All resource files are loaded via `IOUtils.readResource()` and `IconUtils`. Before reading, each transformer is checked in sequence to determine whether the path needs to be transformed.

**Limitation:** Resources loaded before the plugin engine starts will not go through the transformer.

## Useful Links

- Demo: [demo-resource-path-transformer](https://code.fanruan.com/hugh/demo-resource-path-transformer)
