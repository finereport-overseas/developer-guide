# NumberFormatProvider

| 属性 | 值 |
| --- | --- |
| 所属模块 | extra-core (FineBI) |
| 官方文档 | [查看文档](https://wiki.fanruan.com/pages/viewpage.action?pageId=158402912) |

---

## 接口作用

数字格式在后台处理的时候都是类似##,###.00，除了千分符是否显示之外不能支持其他格式，该接口提供自定义数值格式化的方法，可对仪表板、数据准备、导出excel进行自定义格式化

## 接口内容

### 主要接口


```java
@Open
public interface NumberFormatProvider extends Immutable {

    String XML_TAG = "NumberFormatProvider";

    int CURRENT_LEVEL = 1;

    /**
     * 获取仪表板数值格式化器
     *
     * @param type            格式化类型，-1：不作处理，0：取整，1：小数点后一位，2小数点后两位
     * @param isNumSeparators 是否使用分隔符
     * @return 格式化器
     */
    DecimalFormat getDashboardDecimalFormat(int type, boolean isNumSeparators);

    /**
     * 获取excel数值格式化pattern
     *
     * @param type            格式化类型，-1：不作处理，0：取整，1：小数点后一位，2小数点后两位
     * @param isNumSeparators 是否使用分隔符
     * @return 格式化pattern
     */
    String getExcelDecimalFormat(int type, boolean isNumSeparators);

    /**
     * 获取数据表数值格式化器
     *
     * @param type            格式化类型，-1：不作处理，0：取整，1：小数点后一位，2小数点后两位
     * @param isNumSeparators 是否使用分隔符
     * @return 格式化器
     */
    DecimalFormat getTableDecimalFormat(int type, boolean isNumSeparators);
}
```

### 接口接入

|   |
| --- |

## 提供给插件的抽象类


```java
@API(level = NumberFormatProvider.CURRENT_LEVEL)
public abstract class AbstractNumberFormatProvider implements NumberFormatProvider {

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
