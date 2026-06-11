# DialectCreator

| 属性 | 值 |
| --- | --- |
| 接口类型 | extra-core |
| 完整类名 | `com.fr.stable.fun.DialectCreator` |

## 背景与场景

FineReport 支持通过 JDBC 连接主流数据库。尽管 JDBC 标准化程度较高，但不同数据库厂商在连接协议、使用方式和 SQL 语法上存在差异，这些差异在帆软开发中称为"数据库方言"。开发者可通过 `DialectCreator` 接口，为目标数据库提供适配支持，使其能在 FineReport 产品中正常工作。

## 接口定义

```java
package com.fr.stable.fun;

import com.fr.stable.fun.mark.Mutable;

public interface DialectCreator extends Mutable {
    String XML_TAG = "DialectCreator";
    int CURRENT_LEVEL = 1;

    /**
     * 根据数据源驱动信息，返回对应的 Dialect 适配类
     */
    Class<?> generate(UrlDriver driver);

    /**
     * 根据数据库连接，返回对应的 Dialect 适配类
     */
    Class<?> generate(Connection connection);
}
```

`Dialect` 接口包含约 80+ 个方法，涵盖数据库特定操作：

- 表/Schema 初始化和获取
- 列类型转换
- SQL LIMIT/OFFSET 生成
- 存储过程处理
- 外键管理
- 序列/自增生成
- 事务控制
- 唯一约束异常处理

`AbstractDialect` 提供简化适配封装，所有 `Dialect` 接口方法均委托给 `DialectFactory` 中的默认实现。`DefaultDialect` 继承 `AbstractDialect`，通过 `DialectKeyConstants` 中的键值模式实现所有方法。

## 支持版本

| 产品线 | 版本 | 支持情况 |
| --- | --- | --- |
| FR | 8.0 | 支持 |
| FR | 9.0 | 支持 |
| FR | 10.0 | 支持 |
| FR | 11.0 | 支持 |
| BI | 3.6 | 支持 |
| BI | 4.0 | 支持 |
| BI | 5.1 | 支持 |
| BI | 5.1.2 | 支持 |
| BI | 5.1.3 | 支持 |

## 插件注册

在 `plugin.xml` 中添加以下节点：

```xml
<extra-core>
    <DialectCreator class="your class name"/>
</extra-core>
```

## 原理说明

所有方言均由 `DialectFactory` 生成。初始化时，工厂实例化 `DialectProviderImpl`（实现 `DialectProvider`）。其中 `getDialectFromExtraDriver` 和 `getDialectFromExtraMetadata` 两个方法，负责从插件中获取方言实例并返回对应的方言类。

**注意**：`DialectCreator#generate` 接口方法在无匹配方言时必须返回 `null`。

## 常用链接

- Demo：[demo-dialect-creator](https://code.fanruan.com/hugh/demo-dialect-creator)
