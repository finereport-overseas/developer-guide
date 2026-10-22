# 连接器数据源读取插件接口（后端）

## 1. POM 依赖

开发接口时，依赖 fdl 的插件 sdk —— `fdl-plugin-interface`：

```java
<dependencies>
        <dependency>
            <groupId>com.fr.intelligence</groupId>
            <artifactId>fdl-plugin-interface</artifactId>
            <version>4.1-PERESIST-SNAPSHOT</version>
        </dependency>
</dependencies>
```

## 2. AbstractInputNodeTypeProvider

首先，需要继承 `AbstractInputNodeTypeProvider`，这是新算子的接入点：

```java
    /**
     * 功能点是否支持
     *
     * @return 是否支持此组件
     */
    @Override
    public abstract boolean isSupport();

    /**
     * 功能点校验
     */
    @Override
    public abstract void checkSupport();

    /**
     * @return 算子类型 id，需要和前端的 nodeType 对应。
     */
    @Override
    public abstract String id();

    /**
     * @return 组件的类型，返回 AbstractInputNodeProvider 的实现类
     */
    @Override
    public abstract Class<T> getDataFlowNodeProviderClass();
```

## 3. AbstractInputNodeProvider

其次，通过继承 `AbstractInputNodeProvider` 来实现算子具体的配置，并实现参数替换、校验、生成配置等一系列方法：

```java
    /**
     * 复制对象, 所有的引用对象都要复制
     *
     * @return 复制体
     */
    @Override
    public abstract AbstractInputNodeProvider clone();

    /**
     * 检测配置是否完整，返回不完整的报错集合（有条件的话可以做国际化）
     *
     * @return 校验失败的原因集合
     */
    @Override
    public abstract List<String> check();

    /**
     * 参数替换，对支持参数的属性进行解析后替换。
     * eg: provider.setName(paramReplaceFunction.apply(provider.getName))
     */
    @Override
    public abstract void renderParam(Function<String, String> paramReplaceFunction);

    /**
     * 返回执行需要的参数集合，AbstractNRdbmsDatasourceProvider#createResultSetProvider 的入参
     *
     * @return 执行参数集合
     */
    @Override
    public abstract Map<String, Object> buildConfMap();
```

## 4. AbstractNRdbmsDatasourceProvider

连接器的数据源接口：

```java
    /**
     * 数据源类型名称，和其他数据源不重复
     */
    public abstract String dsType();

    /**
     * 前端显示的数据源名称
     */
    public abstract String dsTypeName();

    /**
     * license 是否正常，影响在前端显示效果。
     */
    public abstract boolean isSupport();

    /**
     * license 是否正常，影响在后端的使用
     */
    public abstract void checkSupport();

     /**
     * 获取取数的结果集
     *
     * @return 数据结果集
     */
    @Override
    public abstract AbstractNRdbmsResultSetProvider createResultSetProvider(Map<String, Object> conf);
```

## 5. AbstractNRdbmsResultSetProvider

连接器取数的结果集：

```java
    /**
     * 判断是否存在下一条数据，如果存在，需要将游标向下移动一条。
     *
     * @return t/f
     */
    @Override
    public abstract boolean next();

    /**
     * 获取当前的一行数据
     *
     * @return 当前行数据集合
     */
    @Override
    public abstract List<Object> getRow();

    /**
     * 获取当前所处的行数，初始为 -1，每次调用 next() 后，如果存在数据，则 +1
     *
     * @return 当前行数
     */
    @Override
    public abstract int currentCurs();

    /**
     * 获取字段类型的集合
     *
     * @return 字段类型结合
     */
    @Override
    public abstract List<PluginMetaColumn> getMetaColumns();


public class PluginMetaColumn implements MetaColumnProvider {

    /**
     * 字段名
     */
    private String name;

    /**
     * 对应 JDBC 的 Type
     *
     * @see java.sql.Types
     */
    private int columnType;

    /**
     * 对应 JDBC 的 TypeName
     * eg: varchar, int
     */
    private String columnTypeName;

    /**
     * 对应 JDBC 的 precision
     * 长度，会影响到目标库建表，如果没有长度，可以默认一个比如 255
     */
    private int precision;

    /**
     * 对应 JDBC 的 scale
     * 精度，会影响到目标库建表，如果没有精度，可以固定一个值
     */
    private int scale;

    /**
     * 影响到字段映射的显示值，比如 varchar(255)，decimal(5,5)
     */
    private String dialectColumnTypeName;
}
```

## 6. PluginNRdbmsConnectionConf

数据连接的配置类，这个类继承自 `com.fr.data.impl.AbstractDatabaseConnection`：

```java
@Open
public abstract class PluginNRdbmsConnectionConf extends DPNRdbmsConnectionConf {

    /**
     * 对应 datasource 的 dsType
     */
    public abstract String getDPType();
}
```

## 7. plugin.xml

fdl 的功能扩展下面需要填 `AbstractInputNodeTypeProvider` 和 `AbstractNRdbmsDatasourceProvider` 的子类：

```xml
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<plugin>
    <id>com.fr.plugin.fdl.generic.jdbc</id>
    <name><![CDATA[api取数]]></name>
    <active>yes</active>
    <version>1.0</version>
    <group>fdl</group>
    <env-version>4.2.1</env-version>
    <jartime>2024-11-20</jartime>
    <vendor>fdl</vendor>
    <description><![CDATA[]]></description>
    <change-notes><![CDATA[
      [2023-11-13]支持fdl使用通用jdbc执行sql脚本和作为数据来源<br/>
    ]]></change-notes>

    <!-- 主jar的接口，可以定义一些通用的功能扩展，如国际化 -->
    <extra-core>
        <LocaleFinder class="com.fr.plugin.dp.datasource.resource.LocaleFinder"/>
    </extra-core>

    <!-- fdl插件特有的功能扩展 -->
    <extra-fdl>
        <NRdbmsDatasourceProvider class="com.fr.plugin.dp.datasource.ds.APIDatasource" />
        <InputTypeProvider class="com.fr.plugin.dp.node.input.APIReadNode">
    </extra-fdl>
    <!-- 插件功能记录点类 -->
    <function-recorder class="com.fr.plugin.dp.datasource.resource.LocaleFinder"/>
</plugin>
```

> 原文中的效果截图请参考 KMS 文档。
