# ReportChangeListenProvider

| 属性 | 值 |
| --- | --- |
| 所属模块 | extra-core (FineBI) |
| 官方文档 | [查看文档](https://wiki.fanruan.com/pages/viewpage.action?pageId=158402916) |

---

## 接口作用

增加模板，编辑模板，删除模板的监听

## 接口内容

### 主要接口


```java
public interface ReportChangeListenProvider extends Mutable {


    String XML_TAG = "ReportChangeListenProvider";

    int CURRENT_LEVEL = 1;

    /**
     * 添加模板后触发
     *
     * @param reportIds 模板Id
     */
    void addEvent(Set<String> reportIds);

    /**
     * 更新模板配置后触发
     *
     * @param reportIds 表原始名
     */
    void updateEvent(Set<String> reportIds);

    /**
     * 删除模板配置后触发
     *
     * @param reportIds 表原始名
     */
    void removeEvent(Set<String> reportIds);

}


```

## 接口接入


```xml
<extra-core>
        <ReportChangeListenProvider class="继承抽象类的子类路径"/>
</extra-core>
```

## 提供给插件的抽象类


```java
@API(level = ReportChangeListenProvider.CURRENT_LEVEL)
public abstract class AbstractReportChangeListenProvider implements ReportChangeListenProvider {

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
