# TransferPackageProvider

| 属性 | 值 |
| --- | --- |
| 接口类型 | extra-core |
| 完整类名 | `com.fr.stable.fun.TransferPackageProvider` |

## 背景与场景

帆软报表从低版本向高版本升级的过程中，进行了大量代码重构，其中包括许多移包操作。这导致低版本模板升级到高版本服务器后，会出现找不到类的问题。

`TransferPackageProvider` 接口提供类名转换功能：在读取 cpt/frm 文件中的 `class` 属性时，将旧类名映射为新类名。

例如，模板中存在 `<ExampleAttr class="com.fr.xxx.MyClass"/>`，而 `MyClass` 已移动至 `com.fr.xxx.moved.MyClass`，则可使用该接口实现自动替换。

除移包兼容外，该接口还可在特殊情况下通过类替换拦截嵌入的报表对象，干预执行逻辑。

## 接口定义

```java
package com.fr.stable.fun;

import com.fr.stable.fun.mark.Mutable;

/**
 * 兼容移包的类名转换器
 *
 * Created by loy on 2017/1/18.
 */
public interface TransferPackageProvider extends Mutable {

    String XML_TAG = "TransferPackageProvider";

    int CURRENT_LEVEL = 1;

    /**
     * 是否为该转换器要转换的类名
     * @param oldClassName 读到的旧类名
     */
    boolean accept(String oldClassName);

    /**
     * 返回替换后的新类名
     * @param oldClassName 读到的旧类名
     */
    String transfer(String oldClassName);
}
```

## 支持版本

| 产品线 | 版本 | 支持情况 |
| --- | --- | --- |
| FR | 8.0 | 支持 |
| FR | 9.0 | 支持 |
| FR | 10.0 | 支持 |
| FR | 11.0 | 支持 |

## 插件注册

在 `plugin.xml` 中添加以下节点：

```xml
<extra-core>
    <TransferPackageProvider class="your class name"/>
</extra-core>
```

## 原理说明

接口通过两条主要代码路径生效：

1. **`TableDataXmlUtils`**：在数据集对象反序列化时应用转换
2. **`GeneralXMLTools`**：在通过 `readXMLable()` 和 `readStringAsXMLable()` 解析 XMLable 对象时应用转换

**特别限制**：并非所有 cpt/frm 中的 `class` 元素都会被该接口覆盖，仅通过 `GeneralXMLTools.readXMLable` / `readStringAsXMLable` 读取的部分元素以及模板数据集对象才会被覆盖。

## 常用链接

- Demo：[demo-transfer-package-provider](https://code.fanruan.com/hugh/demo-transfer-package-provider)
