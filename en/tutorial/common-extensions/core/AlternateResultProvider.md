# AlternateResultProvider

| Property | Value |
| --- | --- |
| Interface Type | extra-core |
| Full Class Name | `com.fr.stable.fun.AlternateResultProvider` |

## Background and Use Cases

FineReport has a built-in standard dataset cache strategy. When the built-in strategy cannot meet requirements, developers can implement `AlternateResultProvider` to define a custom caching solution tailored to their business needs.

Additionally, after a fill-in operation modifies a data table, the associated cache needs to be invalidated. This interface parses connection, schema, and table information and matches cache SQL keywords (`from`, `join`, `union`, `into`) to help synchronize cache invalidation with fill-in configuration changes.

This interface can also be used to register externally accessible cache strategies between plugins, reducing inter-plugin coupling.

## Interface Definition

```java
package com.fr.stable.fun;

import com.fr.stable.fun.mark.Mutable;

public interface AlternateResultProvider<K, V> extends Mutable {
    int CURRENT_LEVEL = 1;
    String MARK_STRING = "AlternateResultProvider";

    boolean accept(Class<?> keyClz, Class<?> valClz);
    V get(K key);
    void put(K key, V value);
    void remove(K key);
    <E> void removeBy(E extraKey);
}
```

```java
package com.fr.stable.fun.impl;

import com.fr.stable.fun.AlternateResultProvider;

@API(level = AlternateResultProvider.CURRENT_LEVEL)
public abstract class AbstractAlternateResultProvider<K, V>
        implements AlternateResultProvider<K, V> {

    public int currentAPILevel() {
        return CURRENT_LEVEL;
    }

    public String mark4Provider() {
        return MARK_STRING;
    }

    @Override
    public <E> void removeBy(E extraKey) {
        K[] matchedKeys = matchedKeys(extraKey);
        if (ArrayUtils.isNotEmpty(matchedKeys)) {
            for (K k : matchedKeys(extraKey)) {
                remove(k);
            }
        }
    }

    public abstract <E> K[] matchedKeys(E extraKey);
}
```

## Supported Versions

| Product | Version | Support |
| --- | --- | --- |
| FR | 10.0 | Supported |
| FR | 11.0 | Supported |

## Plugin Registration

Add the following node in `plugin.xml`:

```xml
<extra-core>
    <AlternateResultProvider class="your class name"/>
</extra-core>
```

## How It Works

As shown in `DmlController` and `SynchronizedLiveDataModelUtils`, the shared dataset cache retrieval logic queries plugin implementations first, falling back to the product's built-in cache. When a fill-in operation triggers cache invalidation, the system calls the corresponding plugin method to remove cache entries.

Retrieval method:
```java
Set<AlternateResultProvider> set = ExtraClassManager.getInstance().getArray(AlternateResultProvider.XML_TAG);
```

## Useful Links

- Demo: [demo-alternate-result-provider](https://code.fanruan.com/hugh/demo-alternate-result-provider)
