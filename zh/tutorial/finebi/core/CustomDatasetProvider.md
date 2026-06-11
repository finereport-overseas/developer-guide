# CustomDatasetProvider

| 属性 | 值 |
| --- | --- |
| 所属模块 | extra-core (FineBI) |
| 官方文档 | [查看文档](https://wiki.fanruan.com/pages/viewpage.action?pageId=158402883) |

---

## 接口作用

支持主线的excel、sql、db等类型外的数据集接入bi公共数据

## 接口内容


```java
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

## 接口接入


```java
    <extra-core>
        <CustomDatasetProvider class="继承AbstractCustomDatasetProvider的子类路径"/>
    </extra-core>
```

## 提供给插件的抽象类

统筹接口功能抽象类


```java
public abstract class AbstractCustomDatasetGenerator implements CustomDatasetGenerator, Serializable {

    @Override
    public boolean match(String name) {
        return CompareUtils.isEqual(name, getName());
    }
}
```

取数功能抽象类

- 不支持sql取数
- 支持sql取数

前端页面


```java
@Open
public interface CustomDatasetPageGenerator {

    /**
     * 获取图标
     */
    String getIcon(CustomDatasetContext datasetContext);

    /**
     * 获取html
     */
    String getEditPageHTML(CustomDatasetContext datasetContext);

    /**
     * 获取Component
     */
    AssembleComponent getComponent(CustomDatasetContext datasetContext);

}


```
