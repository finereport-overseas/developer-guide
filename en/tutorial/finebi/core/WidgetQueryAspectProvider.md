# WidgetQueryAspectProvider

| Property | Value |
| --- | --- |
| Module | extra-core (FineBI) |
| Official Docs | [View Documentation](https://wiki.fanruan.com/pages/viewpage.action?pageId=158402926) |

---

## Purpose

Provides a query inspection aspect for components, covering both export queries and regular queries. Exposes partial component information and result information during component queries.

## Interface Content

### Main Interface

```java
public interface WidgetQueryAspectProvider extends Mutable {
    String XML_TAG = "SimpleAspectProvider";

    int CURRENT_LEVEL = 1;

    /**
     * @return whether this aspect applies to component export queries
     */
    boolean isExportAspect();

    /**
     * @return whether this aspect applies to regular data queries
     */
    boolean isQueryAspect();

    /**
     * Called before a component query. Provides basic component information without affecting query results.
     *
     * @param widgetQueryInfo basic information about the dashboard component
     * @throws FineEngineException if there is an error with the component information
     */
    void before(WidgetQueryInfo widgetQueryInfo) throws FineEngineException;

    /**
     * Called after a component query completes. Allows processing of query results.
     *
     * @param widgetQueryInfo   basic information about the dashboard component
     * @param widgetQueryResult component query result
     * @throws FineEngineException if there is an error with the query result
     */
    void after(WidgetQueryInfo widgetQueryInfo, WidgetQueryResult widgetQueryResult) throws FineEngineException;
}
```

### Registration

```xml
<extra-core>
        <WidgetQueryAspectProvider class="path to your subclass extending the abstract class"/>
</extra-core>
```

## Abstract Class Provided to Plugins

```java
@API(level = WidgetQueryAspectProvider.CURRENT_LEVEL)
public abstract class AbstractWidgetQueryAspectProvider implements WidgetQueryAspectProvider {

    @Override
    public int currentAPILevel() {
        return CURRENT_LEVEL;
    }

    @Override
    public String mark4Provider() {
        return getClass().getName();
    }
}
```
