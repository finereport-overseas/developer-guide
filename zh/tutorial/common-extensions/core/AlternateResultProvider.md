# AlternateResultProvider

| 属性 | 值 |
| --- | --- |
| 接口类型 | extra-core |
| 完整类名 | `com.fr.stable.fun.AlternateResultProvider` |

## 背景与场景

FineReport 平台内置了标准的数据集缓存策略。当内置策略无法满足需求时，开发者可通过实现 `AlternateResultProvider` 来定义符合业务需求的自定义缓存方案。

此外，在填报操作修改数据表后，相关联的缓存需要失效处理。该接口通过解析连接、Schema 和表信息，并匹配缓存 SQL 关键字（from、join、union、into），帮助实现缓存与填报配置的联动。

该接口还可用于：插件之间通过注册可外部访问的缓存策略，降低插件间耦合度。

## 接口定义

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

## 支持版本

| 产品线 | 版本 | 支持情况 |
| --- | --- | --- |
| FR | 10.0 | 支持 |
| FR | 11.0 | 支持 |

## 插件注册

在 `plugin.xml` 中添加以下节点：

```xml
<extra-core>
    <AlternateResultProvider class="your class name"/>
</extra-core>
```

## 原理说明

如 `DmlController` 和 `SynchronizedLiveDataModelUtils` 所示，共享数据集缓存的获取逻辑中，会优先查询插件实现，回退到产品内置缓存。填报操作触发缓存失效时，系统会调用对应插件方法移除缓存条目。

获取方式：
```java
Set<AlternateResultProvider> set = ExtraClassManager.getInstance().getArray(AlternateResultProvider.XML_TAG);
```

## 常用链接

- Demo：[demo-alternate-result-provider](https://code.fanruan.com/hugh/demo-alternate-result-provider)
