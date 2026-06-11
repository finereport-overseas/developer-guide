# IOFileAttrMark

| 属性 | 值 |
| --- | --- |
| 接口类型 | extra-core |
| 完整类名 | `com.fr.stable.fun.IOFileAttrMark` |

## 背景与场景

帆软设计器包含大量交互扩展接口（工具栏、菜单、单元格右键菜单、属性/事件配置页等）。部分接口包含 `readXML` 和 `writeXML` 等序列化方法，而部分接口则没有。`IOFileAttrMark` 接口用于扩展报表配置的读写能力，通常与其他设计器交互接口配合使用，而非单独使用。

## 接口定义

```java
package com.fr.stable.fun;

import com.fr.json.JSONException;
import com.fr.json.JSONObject;
import com.fr.stable.fun.mark.Mutable;
import com.fr.stable.xml.XMLable;

/**
 * Created by richie on 16/4/19.
 * 给IOFile对象提供的额外属性
 */
public interface IOFileAttrMark extends Mutable, XMLable {

    String MARK_STRING = "IOFileAttrMark";
    int CURRENT_LEVEL = 1;

    /**
     * 该属性的XML标记标签，具有唯一性
     * @return XML标签
     */
    String xmlTag();

    /**
     * 属性转为json
     * @return json格式的属性
     */
    JSONObject createJSONConfig() throws JSONException;

    /**
     * 克隆当前对象
     * @return 克隆出来的对象
     */
    IOFileAttrMark clone();
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
    <IOFileAttrMark class="your class name"/>
</extra-core>
```

## 原理说明

接口通过以下方式获取所有声明的报表属性扩展：

```java
Set<IOFileAttrMark> attrs = PluginModule.getAgent(PluginModule.ExtraCore)
    .getArray(IOFileAttrMark.MARK_STRING);
```

模板反序列化时，`IOFileAttrMarkReader.read` 负责加载扩展属性。`IOFileAttrMarkManager.getAllAsMap` 方法通过 `getExtraMarks` 加载所有插件声明的属性。

## 常用链接

- Demo：[demo-menu-handler（attr-mark 分支）](https://code.fanruan.com/hugh/demo-menu-handler/src/branch/10.0/src/main/java/com/tptj/demo/hg/menu/handler/DemoAttrMark.java)
