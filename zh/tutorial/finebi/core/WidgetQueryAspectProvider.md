# WidgetQueryAspectProvider

| 属性 | 值 |
| --- | --- |
| 所属模块 | extra-core (FineBI) |
| 官方文档 | [查看文档](https://wiki.fanruan.com/pages/viewpage.action?pageId=158402926) |

---

## 接口作用

提供组件查询检查切面，包括导出和普通查询，提供组件查询部分组件信息及结果信息

## 接口内容

### 主要接口


```java
public interface WidgetQueryAspectProvider extends Mutable {
    String XML_TAG = "SimpleAspectProvider";

    int CURRENT_LEVEL = 1;

    /**
     * @return 该切面是否适用于组件导出查询
     */
    boolean isExportAspect();

    /**
     * @return 该切面是否适用于普通数据查询
     */
    boolean isQueryAspect();

    /**
     * 组件查询前，提供组件基本信息，不影响组件查询结果
     *
     * @param widgetQueryInfo 仪表板组件基本信息
     * @throws FineEngineException 组件基本信息异常
     */
    void before(WidgetQueryInfo widgetQueryInfo) throws FineEngineException;

    /**
     * 组件查询结束后，对查询结果处理
     *
     * @param widgetQueryInfo   仪表板组件基本信息
     * @param widgetQueryResult 组件查询结果
     * @throws FineEngineException 组件查询结果异常
     */
    void after(WidgetQueryInfo widgetQueryInfo, WidgetQueryResult widgetQueryResult) throws FineEngineException;
}


```

### 接口接入


```xml
<extra-core>
        <WidgetQueryAspectProvider class="继承抽象类的子类路径"/>
</extra-core>
```

## 提供给插件的抽象类


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
