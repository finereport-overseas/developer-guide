# Custom Dataset

## Frontend

The backend Step 3 injects frontend resources: HTML and a Component (which includes JS and CSS).

To implement the frontend panel, define a `render` method in JS and initialize it via `new BIPlugin().init(render)`.

```html
<div id="test"></div>
```

```js
/**
 * data: If the dataset has been saved previously, this will contain the developer's saved data; null for a new dataset.
 * config: Configuration information, such as the template environment. Future property extensions will also be placed here.
 * saveSessionCallback(data: Object): Promise — Save callback; accepts a JSON object, returns a Promise indicating success or failure.
 * closeSessionCallback: Calling this ends the current page session and returns control to BI.
 */
function render(data, config, saveFn, closeFn) {
    console.log('data:', data);
    console.log('config:', config);
    document.getElementById('test').innerHTML = `
        jsonContext: <input id="jsonContext" value='${data.jsonContext}' /><br />
        transferName: <input id="transferName" value='${data.transferName}'>
        <button id="save" type="button">save</button>
        <button id="close" type="button">close</button>`;
    document.getElementById('save').onclick = function () {
        const jsonContext = document.getElementById('jsonContext').value;
        const transferName = document.getElementById('transferName').value;
        saveFn({ jsonContext, transferName }).then(res => console.log(res));
    };
    document.getElementById('close').onclick = function () {
        closeFn();
    };
}
new BIPlugin().init(render);
```

## Backend

### Step 1: Register the Plugin Manager

Extend `AbstractCustomDatasetProvider` to implement a custom subclass for integration with the plugin manager.

```java
// CustomDatasetProvider interface
@Open
public interface CustomDatasetProvider extends Mutable {

    String XML_TAG = "CustomDatasetProvider";

    int CURRENT_LEVEL = 1;

    /**
     * Custom dataset generator
     */
    CustomDatasetGenerator getGenerator();
}
```

```java
// AbstractCustomDatasetProvider abstract class
@API(level = CustomDatasetProvider.CURRENT_LEVEL)
public abstract class AbstractCustomDatasetProvider implements CustomDatasetProvider {

    @Override
    public int currentAPILevel() {
        return CustomDatasetProvider.CURRENT_LEVEL;
    }

    @Override
    public String mark4Provider() {
        return getClass().getName();
    }
}
```

### Step 2: Implement the Dataset Generator

Extend `AbstractCustomDatasetGenerator` to implement a custom subclass for providing the dataset's own properties (name, i18n, frontend resource configuration, and backend data retrieval logic).

> `name` must be strictly unique — each plugin within the same project must have a distinct value.

```java
// CustomDatasetGenerator interface
@Open
public interface CustomDatasetGenerator {

    @NotNull
    boolean match(String name);

    /** Custom dataset name — unique identifier */
    @NotNull
    String getName();

    /** Display name for the new custom dataset type; override with your own i18n key */
    @NotNull
    default String getDisplayName() {
        return getName();
    }

    /** Frontend page generator */
    @NotNull
    CustomDatasetPageGenerator getPageGenerator();

    /** Data source generator */
    @NotNull
    CustomDatasetDatasourceGenerator getDatasourceGenerator();

    /**
     * When used as a built-in data source in a self-service dataset,
     * whether the data source needs to be re-extracted during self-service dataset extraction.
     * jsonContext is the custom jsonContext defined by the developer in the custom dataset.
     */
    default boolean needExtractSourceData(String jsonContext) {
        return false;
    }
}
```

```java
// AbstractCustomDatasetGenerator abstract class
public abstract class AbstractCustomDatasetGenerator implements CustomDatasetGenerator, Serializable {

    @Override
    public boolean match(String name) {
        return CompareUtils.isEqual(name, getName());
    }
}
```

### Step 3: Implement the Frontend Page Generator

Implement the `CustomDatasetPageGenerator` interface to provide frontend-related content: the plugin name, icon, and the frontend page displayed when creating a new dataset.

```java
@Open
public interface CustomDatasetPageGenerator {

    /** Get the icon */
    String getIcon(CustomDatasetContext datasetContext);

    /** Get the HTML */
    String getEditPageHTML(CustomDatasetContext datasetContext);

    /** Get the Component */
    AssembleComponent getComponent(CustomDatasetContext datasetContext);
}
```

### Step 4 (Core): Implement the Data Retrieval Logic

Choose the appropriate abstract class to extend based on whether the data source supports SQL:

- **SQL-supported**: Extend `AbstractSqlSupportedDatasourceGenerator`. BI operations (filtering, grouping, joining, etc.) generate SQL based on the plugin-provided Dialect and delegate execution to the plugin, which returns streaming data.
- **SQL-not-supported**: Extend `AbstractDefaultDatasourceGenerator` (suitable for most scenarios). The plugin is treated as a single data unit; the main pipeline reads streaming data from the plugin via Spark and uses SparkDialect to interpret BI operation queries.
- **Multi-plugin join**: Always treated as SQL-not-supported.

#### About jsonContext

`jsonContext` is a highly customizable JSON string. The framework only handles storing and retrieving it; the plugin is responsible for parsing.

Example: the user enters parameterized SQL, and the plugin receives the following `jsonContext`:

```json
{
    "sql": "select * from customer_db_info where driver in ('{$driver}')"
}
```

#### CustomDatasetContext

BI parameters passed to the plugin (user information, roles, SQL-related parameters, and placeholders):

```java
@Open
public interface CustomDatasetContext {

    /** Logged-in user's username parameter */
    String getUserName();

    /** Display name (username) of the logged-in user */
    String getDisplayName();

    /** Role parameter of the logged-in user */
    String getFineRole();

    /** Organizational unit collection parameter of the logged-in user */
    String getFinePosition();

    Integer getEngineType();

    /** Dataset table ID (used for plugin instrumentation) */
    String getTableId();

    /**
     * Variable parameters such as URL and SQL; include as available.
     * Currently the Object value is of type List<String> or String.
     */
    Map<String, Object> getParameterMap();

    /**
     * For SQL-supported data sources, the FROM table, JOIN table, and other parts of the SQL
     * are replaced via table_placeholder; null for non-SQL data sources.
     */
    String getTablePlaceholder();
}
```

Example SQL issued by BI for SQL-supported data sources:

```sql
select
  `T_46896E9E82384F679DF06`.`driver` as `__fcol_0`,
  count(1) as `__fcol_1`
from `46896e9e82384f679df06a84c6c02a8e` as `T_46896E9E82384F679DF06`
group by 1
```

#### CustomDatasetDatasourceGenerator

Top-level interface for the backend data retrieval logic:

```java
@Open
public interface CustomDatasetDatasourceGenerator {

    /** Whether the data source supports SQL */
    boolean isSupportedSql();

    /**
     * Stream-based result set retrieval (non-SQL scenario)
     * @param jsonContext    User-defined information
     * @param datasetContext Context information
     * @param columnInfos    Field configuration from table creation
     */
    RowIterator getRowIterator(String jsonContext, CustomDatasetContext datasetContext, ColumnInformation[] columnInfos);

    /**
     * Stream-based result set retrieval (SQL scenario; may involve multiple joined SQL queries)
     * @param jsonContexts    List of user-defined information
     * @param datasetContexts List of context information
     * @param sql             BI query SQL
     * @param columnInfos     List of field configurations from table creation
     */
    RowIterator getRowIterator(List<String> jsonContexts, List<CustomDatasetContext> datasetContexts,
                               String sql, List<ColumnInformation[]> columnInfos);

    /**
     * Get the data source dialect
     */
    Dialect getDialect(String jsonContext, CustomDatasetContext datasetContext);

    /**
     * Get data source parameters
     * @return Plugin parameters [key:type]; type includes Number, String, Date
     */
    Map<String, Class<?>> getParameters(String jsonContext, CustomDatasetContext datasetContexts);

    /**
     * Column information retrieval
     * @return columnType uses {@link java.sql.Types}
     */
    ColumnInformation[] createColumnInfos(String jsonContext, CustomDatasetContext datasetContexts);
}
```

> Notes:
> - `ColumnInformation.columnType` uses the int values from `java.sql.Types`
> - In `getParameters`, use `java.lang.Number`, `java.lang.String`, `java.util.Date` for the Class
> - Each data retrieval call includes `columnInfos` (field information from table creation) so the plugin can perform data corrections during retrieval

Two abstract classes are provided based on this interface — choose one to extend:

**SQL-not-supported (suitable for most custom data sources):**

```java
public abstract class AbstractDefaultDatasourceGenerator implements CustomDatasetDatasourceGenerator {

    @Override
    public final boolean isSupportedSql() {
        return false;
    }

    @Override
    public final RowIterator getRowIterator(List<String> jsonContexts, List<CustomDatasetContext> datasetContexts,
                                            String sql, List<ColumnInformation[]> columnInfos) {
        throw new UnsupportedOperationException("getRowIterator with sql not supported.");
    }

    @Override
    public final Dialect getDialect(String jsonContext, CustomDatasetContext datasetContext) {
        throw new UnsupportedOperationException("getDialect with sql not supported.");
    }

    // Subclasses only need to implement non-SQL methods
}
```

**SQL-supported (SQL is delegated to the plugin for execution):**

```java
public abstract class AbstractSqlSupportedDatasourceGenerator implements CustomDatasetDatasourceGenerator {

    @Override
    public final boolean isSupportedSql() {
        return true;
    }

    @Override
    public final RowIterator getRowIterator(String jsonContext, CustomDatasetContext datasetContext,
                                            ColumnInformation[] columnInfos) {
        throw new UnsupportedOperationException("getRowIterator without sql not supported.");
    }

    // Subclasses only need to implement SQL-related methods
}
```

## Open-Source Demo

[plugin-bi-custom-dataset-demo](https://code.fineres.com/projects/PG2/repos/plugin-bi-custom-dataset-demo/browse) (MySQL data retrieval example)

## Version Notes

- `>= 6017`: No restart required after plugin installation
