# DateFormatProvider

| 属性 | 值 |
| --- | --- |
| 所属模块 | extra-core (FineBI) |
| 官方文档 | [查看文档](https://wiki.fanruan.com/pages/viewpage.action?pageId=158402887) |

---

## 接口作用

以插件的形式提供二开自定义bi中的日期类型数据的格式化显示方式，以支持除已定义场景（中文，“/”，“-”）外的日期格式

## 接口内容

### 主要接口


```java
@Open
public interface DateFormatProvider extends Immutable {

    String XML_TAG = "DateFormatProvider";

    int CURRENT_LEVEL = 1;

    /**
     * 自定义仪表板的日期格式化
     *
     * @param group 日期分组
     * @param value 原始数据
     * @return 格式化后的日期
     */
    Object formatDashboardDate(int group, Object value);

    /**
     * 自定义数据集的日期格式化
     *
     * @param group 日期分组
     * @param value 原始数据
     * @return 格式化后的日期
     */
    Object formatTableDate(int group, Object value);
}
```

接口中的group类型说明：

### 接口接入

|   |
| --- |

## 提供给插件的抽象类


```java
@API(level = DateFormatProvider.CURRENT_LEVEL)
public abstract class AbstractDateFormatProvider implements DateFormatProvider {

    @Override
    public int currentAPILevel() {
        return CURRENT_LEVEL;
    }

    @Override
    public int layerIndex() {
        return 0;
    }

}
```
