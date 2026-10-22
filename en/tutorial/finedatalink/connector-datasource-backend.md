# Connector Data Source Read Plugin Interface (Backend)

## 1. POM Dependency

When developing the interface, depend on the fdl plugin SDK — `fdl-plugin-interface`:

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

First, extend `AbstractInputNodeTypeProvider` — this is the entry point for the new operator:

```java
    /**
     * Whether the feature point is supported.
     *
     * @return whether this component is supported
     */
    @Override
    public abstract boolean isSupport();

    /**
     * Feature point validation.
     */
    @Override
    public abstract void checkSupport();

    /**
     * @return the operator type id, which must match the frontend nodeType.
     */
    @Override
    public abstract String id();

    /**
     * @return the component type, returning the implementation class of AbstractInputNodeProvider
     */
    @Override
    public abstract Class<T> getDataFlowNodeProviderClass();
```

## 3. AbstractInputNodeProvider

Next, extend `AbstractInputNodeProvider` to implement the operator's concrete configuration, as well as parameter substitution, validation, config generation, etc.:

```java
    /**
     * Clone the object; all referenced objects must be copied.
     *
     * @return the clone
     */
    @Override
    public abstract AbstractInputNodeProvider clone();

    /**
     * Check whether the configuration is complete; return the collection of validation failures (internationalization if possible).
     *
     * @return the collection of validation failure reasons
     */
    @Override
    public abstract List<String> check();

    /**
     * Parameter substitution: parse and replace properties that support parameters.
     * eg: provider.setName(paramReplaceFunction.apply(provider.getName))
     */
    @Override
    public abstract void renderParam(Function<String, String> paramReplaceFunction);

    /**
     * Return the parameter collection needed for execution, which is the input of
     * AbstractNRdbmsDatasourceProvider#createResultSetProvider.
     *
     * @return the execution parameter collection
     */
    @Override
    public abstract Map<String, Object> buildConfMap();
```

## 4. AbstractNRdbmsDatasourceProvider

The connector's data source interface:

```java
    /**
     * The data source type name, must not conflict with other data sources.
     */
    public abstract String dsType();

    /**
     * The data source name displayed on the frontend.
     */
    public abstract String dsTypeName();

    /**
     * Whether the license is valid; affects the display on the frontend.
     */
    public abstract boolean isSupport();

    /**
     * Whether the license is valid; affects usage on the backend.
     */
    public abstract void checkSupport();

     /**
     * Get the result set for data fetching.
     *
     * @return the data result set
     */
    @Override
    public abstract AbstractNRdbmsResultSetProvider createResultSetProvider(Map<String, Object> conf);
```

## 5. AbstractNRdbmsResultSetProvider

The result set for connector data fetching:

```java
    /**
     * Determine whether there is another row; if so, move the cursor down one row.
     *
     * @return true/false
     */
    @Override
    public abstract boolean next();

    /**
     * Get the current row of data.
     *
     * @return the current row data collection
     */
    @Override
    public abstract List<Object> getRow();

    /**
     * Get the current row index; starts at -1, and increments by 1 after each next() if data exists.
     *
     * @return the current row index
     */
    @Override
    public abstract int currentCurs();

    /**
     * Get the collection of column types.
     *
     * @return the column type collection
     */
    @Override
    public abstract List<PluginMetaColumn> getMetaColumns();


public class PluginMetaColumn implements MetaColumnProvider {

    /**
     * Column name.
     */
    private String name;

    /**
     * Corresponding JDBC Type.
     *
     * @see java.sql.Types
     */
    private int columnType;

    /**
     * Corresponding JDBC TypeName, e.g. varchar, int.
     */
    private String columnTypeName;

    /**
     * Corresponding JDBC precision (length); affects target-table creation.
     * If there is no length, a default such as 255 can be used.
     */
    private int precision;

    /**
     * Corresponding JDBC scale (precision); affects target-table creation.
     * If there is no scale, a fixed value can be used.
     */
    private int scale;

    /**
     * The display value that affects field mapping, e.g. varchar(255), decimal(5,5).
     */
    private String dialectColumnTypeName;
}
```

## 6. PluginNRdbmsConnectionConf

The configuration class for the data connection; this class extends `com.fr.data.impl.AbstractDatabaseConnection`:

```java
@Open
public abstract class PluginNRdbmsConnectionConf extends DPNRdbmsConnectionConf {

    /**
     * The dsType corresponding to the data source.
     */
    public abstract String getDPType();
}
```

## 7. plugin.xml

Under the fdl function extension, you must declare the subclasses of `AbstractInputNodeTypeProvider` and `AbstractNRdbmsDatasourceProvider`:

```xml
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<plugin>
    <id>com.fr.plugin.fdl.generic.jdbc</id>
    <name><![CDATA[api fetch]]></name>
    <active>yes</active>
    <version>1.0</version>
    <group>fdl</group>
    <env-version>4.2.1</env-version>
    <jartime>2024-11-20</jartime>
    <vendor>fdl</vendor>
    <description><![CDATA[]]></description>
    <change-notes><![CDATA[
      [2023-11-13] Support fdl using generic jdbc to execute sql scripts and as a data source<br/>
    ]]></change-notes>

    <!-- Interfaces of the main jar; define common function extensions such as internationalization -->
    <extra-core>
        <LocaleFinder class="com.fr.plugin.dp.datasource.resource.LocaleFinder"/>
    </extra-core>

    <!-- fdl-specific function extensions -->
    <extra-fdl>
        <NRdbmsDatasourceProvider class="com.fr.plugin.dp.datasource.ds.APIDatasource" />
        <InputTypeProvider class="com.fr.plugin.dp.node.input.APIReadNode">
    </extra-fdl>
    <!-- Plugin function recorder class -->
    <function-recorder class="com.fr.plugin.dp.datasource.resource.LocaleFinder"/>
</plugin>
```

> For the effect screenshots in the original doc, please refer to the KMS doc.
