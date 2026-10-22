# 数据源连接器取数接口

| 属性 | 值 |
| --- | --- |
| 接口类型 | provider（数据源连接器） |
| 所属模块 | `extra-fdl` |
| 适用产品 | FineDataLink（FDL） |

## 简介

FDL 从 4.1 起开放**数据源连接器取数插件接口**，用于扩展「数据同步」的输入来源，以及「数据转换」中的输入算子，从而支持从各种来源端读取数据。

整体通过三类接口协作完成取数：

1. **算子接口（InputNode）** —— 定义输入算子的类型与结构，承载用户在面板中配置的属性。
2. **数据连接接口（Connection）** —— 定义数据连接的基本配置与创建方式（非必须，如 API 可省去）。
3. **连接器数据源接口（Datasource）** —— 定义 FDL 中的数据类型，并通过算子的配置产生结果集。

## 插件开放原则

- 接口方法中的参数与返回值尽量不直接依赖主 jar 中的类。
- 插件不直接实现主 jar 中的数据结构接口，而是通过代理方式防止接口改动带来影响。
- 所有对外提供的类型都打上 `@Open` 注解。
- 尽量少开放接口，后续按需求评估再逐步开放。

## 连接器三大组成

### 1. 算子接口（InputNode）

算子类型的配置由开发者自定义，面板配置保存在算子节点中。

- **类型**：实现 `InputNodeTypeProvider` 接口（含 `XML_TAG` 常量），用 `PluginInputNodeType` 代理插件接口。在 `plugin.xml` 中注入后，`DataFlowNodeTypes` 即可发现该类型。
- **结构**：`PluginInputNode` 继承输入算子抽象类 `AbstractInputNode`，从而复用输入算子的公共属性与方法。它内部持有一个 `InputNodeProvider` 引用——这个接口开放给用户实现，用户自定义属性全部存放在 `InputNodeProvider` 中，因此该实现类需支持 Jackson 反序列化；其子类通过自定义反序列化器，根据算子类型确定反序列化的具体类。

框架发现算子类型的示例代码：

```java
Set<InputNodeTypeProvider<InputNodeProvider>> array =
        ExtraFdlClassManager.getInstance().getArray(InputNodeTypeProvider.XML_TAG);
Map<String, PluginInputNodeType> pluginProviders = array.stream()
        .collect(Collectors.toMap(InputNodeTypeProvider::id, PluginInputNodeType::new));
pluginProviders.forEach(allDataFlowTypes::putIfAbsent);
```

### 2. 数据连接接口（Connection）

- 实现参考 FR 数据连接插件接口，区别在于：数据连接的配置需要实现 `PluginNRdbmsConnectionConf`，它最终继承自 FR 的 `AbstractDatabaseConnection`。
- 需要额外实现获取数据源类型名称的接口，该名称需与 FDL 的数据源类型对应。
- 注入方式：前端面板注入 + 后台接口注入（可参考 FR 插件的数据连接实现）。

> 数据连接是**非必须**的。像 API 这类没有通用配置的，可以省去数据连接，直接在算子中配置所有取数所需元素。

整体思路：

1. 定义数据连接配置（非必须）
2. 算子中选择具体的数据连接（非必须）
3. 算子中配置其他属性
4. 数据源接口通过算子生成的配置来产生数据结果集

### 3. 连接器数据源接口（Datasource）

- 实现思路与 JDBC 数据源接口类似，但需要实现的接口较少（除数据源名称、license 校验之外）。
- 关键方法是根据配置创建获取数据的 `ResultSet`：`AbstractNRdbmsResultSetProvider createResultSetProvider(Map<String, Object> conf)`，入参 `conf` 由算子的配置类生成。
- `AbstractNRdbmsResultSetProvider` 参考 JDBC 数据源的 `ResultSet` 设计，关键接口有 4 个：

| 方法 | 说明 |
| --- | --- |
| `next()` | 是否存在下一条数据；存在则将游标下移一条 |
| `getRow()` | 获取当前一行数据 |
| `currentCurs()` | 获取当前行数；初始为 -1，调用 `next()` 后存在数据则变为 0 |
| `getMetaColumns()` | 获取字段类型集合 |

框架发现数据源的示例代码：

```java
Set<NRdbmsDatasourceProvider> nRdbmsDatasourceProviders =
        ExtraFdlClassManager.getInstance().getArray(NRdbmsDatasourceProvider.XML_TAG);
nRdbmsDatasourceProviders.forEach(datasourceProvider ->
        result.put(datasourceProvider.dsType(), new PluginNRdbmsDatasource(datasourceProvider)));
```

## plugin.xml 注册

与 FDL 其他接入点一致，通过 `extra-fdl` 标签注册实现类：

```xml
<extra-fdl>
    <InputNodeTypeProvider class="com.fr.plugin.fdl.xxx.YourInputNodeType"/>
    <NRdbmsDatasourceProvider class="com.fr.plugin.fdl.xxx.YourDatasourceProvider"/>
</extra-fdl>
```

- 数据连接配置（如有）同样在 `extra-fdl` 中声明。
- 无通用配置（如 API）可省去数据连接，仅在算子中配置。

## POM 依赖

```xml
<dependencies>
    <dependency>
        <groupId>com.fr.intelligence</groupId>
        <artifactId>fdl-plugin-interface</artifactId>
        <version>4.1-PERESIST-SNAPSHOT</version>
    </dependency>
</dependencies>
```

## 插件开发方式

- 前端面板配置需与算子/连接配置类的字段对应，并支持反序列化。
- 完整的前端开发指南：[连接器数据源读取插件接口（前端）](./connector-datasource-frontend.md)
- 完整的后端开发指南：[连接器数据源读取插件接口（后端）](./connector-datasource-backend.md)

## 注意事项

- 用户自定义的配置类（如 `InputNodeProvider` 子类、数据连接配置类）必须支持 Jackson 反序列化，且字段与前端表单配置项对应。
- 数据连接为可选项；是否提供取决于数据源是否有通用连接配置。
- 接口随 FDL 版本演进，具体方法签名、包路径以 `fdl-plugin-interface` 实际 API 为准。
