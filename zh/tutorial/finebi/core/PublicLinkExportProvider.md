# PublicLinkExportProvider

| 属性 | 值 |
| --- | --- |
| 所属模块 | extra-core (FineBI) |
| 官方文档 | [查看文档](https://wiki.fanruan.com/pages/viewpage.action?pageId=158402914) |

---

## 接口作用

提供给客户开启公共链接导出的接口

## 接口内容

### 主要接口


```java
@Careful
public interface PublicLinkExportProvider extends Immutable {

    String XML_TAG = "PublicLinkExportProvider";

    int CURRENT_LEVEL = 1;

    /**
     * 指定模板公共链接是否支持导出
     *
     * @param reportId 模板Id
     *
     * @return 支持导出
     */
    boolean isExportable(String reportId);

}


```

## 接口接入


```xml
<extra-core>
        <PublicLinkExportProvider class="com.finebi.plugin.link.export.DefaultPublicLinkExportProvider"/>
</extra-core>
```

## 提供给插件的抽象类


```java
@API(level = ServerTablesProvider.CURRENT_LEVEL)
public abstract class AbstractPublicLinkExportProvider implements PublicLinkExportProvider {

    public int currentAPILevel() {
        return CURRENT_LEVEL;
    }

    @Override
    public int layerIndex() {
        return 0;
    }

}
```
