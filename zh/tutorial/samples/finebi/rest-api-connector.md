# 自定义数据源

## 前端

后端步骤三中注入了前端资源：html 和 Component（包含 js 和 css 资源）。

实现前端面板只需在 js 中约定 `render` 方法，并通过 `new BIPlugin().init(render)` 完成初始化。

```html
<div id="test"></div>
```

```js
/**
 * data：如果是保存过的，这里直接填入开发者保存的信息，新建的话这里为 null
 * config：配置信息，比如提供所在模板等环境类信息，未来属性拓展也主要会放在这里
 * saveSessionCallback(data: Object): Promise 保存回调函数，入参为保存的 json 对象，返回值为 Promise 对象，返回保存成功或失败状态
 * closeSessionCallback：调用该接口会结束当前页面会话，把主动权交给 BI
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

## 后端

### 步骤一：注入插件管理

继承 `AbstractCustomDatasetProvider` 实现自定义子类，用于接入插件管理。

```java
// CustomDatasetProvider 接口
@Open
public interface CustomDatasetProvider extends Mutable {

    String XML_TAG = "CustomDatasetProvider";

    int CURRENT_LEVEL = 1;

    /**
     * 自定义数据集生成器
     */
    CustomDatasetGenerator getGenerator();
}
```

```java
// AbstractCustomDatasetProvider 抽象类
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

### 步骤二：实现数据集生成器

继承 `AbstractCustomDatasetGenerator` 实现自定义子类，用于获取自身属性（名字、国际化、前端资源配置、后端取数逻辑）。

> `name` 需要严格防重，同一工程下每个插件应不同。

```java
// CustomDatasetGenerator 接口
@Open
public interface CustomDatasetGenerator {

    @NotNull
    boolean match(String name);

    /** 自定义数据集 name，唯一标识符 */
    @NotNull
    String getName();

    /** 新建自定义数据集类型展示名，重写需自行加国际化 key */
    @NotNull
    default String getDisplayName() {
        return getName();
    }

    /** 前端页面生成器 */
    @NotNull
    CustomDatasetPageGenerator getPageGenerator();

    /** 配置数据生成器 */
    @NotNull
    CustomDatasetDatasourceGenerator getDatasourceGenerator();

    /**
     * 数据源作为自助数据集内置数据源时，自助数据集抽数是否需要重新抽取该数据源
     * jsonContext 即为开发者在自定义数据集内自定义的那份 jsonContext
     */
    default boolean needExtractSourceData(String jsonContext) {
        return false;
    }
}
```

```java
// AbstractCustomDatasetGenerator 抽象类
public abstract class AbstractCustomDatasetGenerator implements CustomDatasetGenerator, Serializable {

    @Override
    public boolean match(String name) {
        return CompareUtils.isEqual(name, getName());
    }
}
```

### 步骤三：实现前端页面生成器

实现 `CustomDatasetPageGenerator` 接口，用于获取前端相关内容：插件名称、图标，以及新建数据集时展示的前端页面。

```java
@Open
public interface CustomDatasetPageGenerator {

    /** 获取图标 */
    String getIcon(CustomDatasetContext datasetContext);

    /** 获取 html */
    String getEditPageHTML(CustomDatasetContext datasetContext);

    /** 获取 Component */
    AssembleComponent getComponent(CustomDatasetContext datasetContext);
}
```

### 步骤四（核心）：实现取数逻辑

根据数据源是否支持 SQL，选择继承不同的抽象类：

- **支持 SQL**：继承 `AbstractSqlSupportedDatasourceGenerator`。BI 操作（过滤、分组、关联等）会基于插件提供的 Dialect 生成 SQL，下放给插件执行，返回流式数据。
- **不支持 SQL**：继承 `AbstractDefaultDatasourceGenerator`（适合大多数场景）。插件被视为一个数据整体，主线通过 Spark 读取插件提供的流式数据，使用 SparkDialect 解析 BI 操作查询。
- **多插件联合**：统一按不支持 SQL 处理。

#### 关于 jsonContext

`jsonContext` 是一个高度可自定义的 JSON 字符串，框架只负责取和存，具体解析逻辑由插件自行实现。

示例：用户输入了带参数的 SQL，插件收到的 jsonContext 如下：

```json
{
    "sql": "select * from customer_db_info where driver in ('{$driver}')"
}
```

#### CustomDatasetContext

传递给插件的 BI 参数（用户信息、角色、sql 相关参数及占位等）：

```java
@Open
public interface CustomDatasetContext {

    /** 登录用户的用户名参数 */
    String getUserName();

    /** 获取登录系统的姓名（用户名） */
    String getDisplayName();

    /** 登录用户的角色参数 */
    String getFineRole();

    /** 登录用户所属的组织机构集合的参数 */
    String getFinePosition();

    Integer getEngineType();

    /** 数据集表 id（插件埋点用） */
    String getTableId();

    /**
     * url、sql 等不固定参数，有就放进去
     * 目前 Object 值为 List<String> 或 String 类型
     */
    Map<String, Object> getParameterMap();

    /**
     * 对于支持 sql 数据源，sql 的 from table、join table 等部分通过
     * table_placeholder 替换；不支持 sql 时该参数为 null
     */
    String getTablePlaceholder();
}
```

支持 SQL 时，BI 下发的 SQL 示例：

```sql
select
  `T_46896E9E82384F679DF06`.`driver` as `__fcol_0`,
  count(1) as `__fcol_1`
from `46896e9e82384f679df06a84c6c02a8e` as `T_46896E9E82384F679DF06`
group by 1
```

#### CustomDatasetDatasourceGenerator

后端取数逻辑的顶层接口：

```java
@Open
public interface CustomDatasetDatasourceGenerator {

    /** 数据源是否支持 sql */
    boolean isSupportedSql();

    /**
     * 流式获取结果集（不支持 sql 场景）
     * @param jsonContext    用户自定义信息
     * @param datasetContext 上下文信息
     * @param columnInfos    建表时的字段配置
     */
    RowIterator getRowIterator(String jsonContext, CustomDatasetContext datasetContext, ColumnInformation[] columnInfos);

    /**
     * 流式获取结果集（支持 sql 场景，可能有多个 sql 关联下放）
     * @param jsonContexts    用户自定义信息列表
     * @param datasetContexts 上下文信息列表
     * @param sql             BI 查询 sql
     * @param columnInfos     建表时的字段配置列表
     */
    RowIterator getRowIterator(List<String> jsonContexts, List<CustomDatasetContext> datasetContexts,
                               String sql, List<ColumnInformation[]> columnInfos);

    /**
     * 获取数据源方言
     */
    Dialect getDialect(String jsonContext, CustomDatasetContext datasetContext);

    /**
     * 获取数据源参数
     * @return 插件参数 [key:type]，type 包含 Number、String、Date
     */
    Map<String, Class<?>> getParameters(String jsonContext, CustomDatasetContext datasetContexts);

    /**
     * 列信息获取
     * @return columnType 使用 {@link java.sql.Types}
     */
    ColumnInformation[] createColumnInfos(String jsonContext, CustomDatasetContext datasetContexts);
}
```

> 注意：
> - `ColumnInformation.columnType` 使用 `java.sql.Types` 各类型的 int 值
> - `getParameters` 中 Class 使用 `java.lang.Number`、`java.lang.String`、`java.util.Date`
> - 每次取数都会携带 `columnInfos`（建表时的字段信息），方便插件在取数过程中做数据矫正

基于该接口提供两个抽象类，二选一继承：

**不支持 SQL（适合大多数自定义数据源）**：

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

    // 子类只需实现不涉及 SQL 的方法
}
```

**支持 SQL（sql 下放给插件执行）**：

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

    // 子类只需实现 SQL 相关方法
}
```

## 开源 Demo

[plugin-bi-custom-dataset-demo](https://code.fineres.com/projects/PG2/repos/plugin-bi-custom-dataset-demo/browse)（MySQL 取数示例）

## 版本说明

- `>= 6017`：安装插件后无需重启即可使用
