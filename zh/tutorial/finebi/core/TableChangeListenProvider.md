# TableChangeListenProvider

| 属性 | 值 |
| --- | --- |
| 所属模块 | extra-core (FineBI) |
| 官方文档 | [查看文档](https://wiki.fanruan.com/pages/viewpage.action?pageId=158402920) |

---

## 接口作用

增加表，编辑表，删除表的监听

## 接口内容

### 主要接口


```java
public interface TableChangeListenProvider extends Mutable {

    String XML_TAG = "TableChangeListenProvider";

    int CURRENT_LEVEL = 1;

    /**
     * 添加表后触发
     * @param tableNames 表的原始名
     */
    void addEvent(Set<String> tableNames);

    /**
     * 更新表配置后触发
     * @param tableNames 表原始名
     */
    void updateEvent(Set<String> tableNames);

    /**
     * 删除表配置后触发
     *
     * @param tableNames 表原始名
     */
    void removeEvent(Set<String> tableNames);

}


```

## 接口接入


```xml
<extra-core>
        <TableChangeListenProvider class="继承抽象类的子类路径"/>
</extra-core>
```

## 提供给插件的抽象类


```java
@API(level = TableChangeListenProvider.CURRENT_LEVEL)
public abstract class AbstractTableChangeListenProvider implements TableChangeListenProvider {

    public int currentAPILevel() {
        return CURRENT_LEVEL;
    }

    @Override
    public String mark4Provider() {
        return getClass().getName();
    }

}
```
