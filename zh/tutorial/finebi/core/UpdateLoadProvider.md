# UpdateLoadProvider

| 属性 | 值 |
| --- | --- |
| 所属模块 | extra-core (FineBI) |
| 官方文档 | [查看文档](https://wiki.fanruan.com/pages/viewpage.action?pageId=158402924) |

---

## 接口作用

更新生命周期插件接入点，方法返回值为boolean

true: 不执行对应操作，如onLoad返回true，则不执行默认load操作。

false：继续执行对应默认操作

## 接口内容

### 主要接口


```java
/**
 * 更新生命周期插件接入点
 * 方法返回值为boolean
 * true: 不执行对应操作，如onLoad返回true，则不执行load操作。
 * false：继续执行对应操作
 *
 * @author James.Zhao
 * @version 5.1.3
 * Created by James.Zhao on 2020/4/8
 */
public interface UpdateLoadProvider extends Mutable {

    String XML_TAG = "UpdateLoadProvider";

    int CURRENT_LEVEL = 1;

    /**
     * 更新生命周期开始前
     */
    <T extends AbstractLoadProperty> boolean onStart(TaskInfoBO bo, AbstractLoad<T> load);

    /**
     * 数据加载与更新之前
     */
    <T extends AbstractLoadProperty> boolean onLoad(TaskInfoBO bo, AbstractLoad<T> load);

    /**
     * 数据提交之前
     */
    <T extends AbstractLoadProperty> boolean onCommit(TaskInfoBO bo, AbstractLoad<T> load);

    /**
     * 数据表重命名前
     */
    <T extends AbstractLoadProperty> boolean onRename(TaskInfoBO bo, AbstractLoad<T> load);

    /**
     * 更新生命周期正常结束后调用
     */
    <T extends AbstractLoadProperty> boolean onEnd(TaskInfoBO bo, AbstractLoad<T> load);

    /**
     * 数据表回滚前
     */
    <T extends AbstractLoadProperty> boolean onRollback(TaskInfoBO bo, AbstractLoad<T> load, Throwable throwable);
}  
```

### 接口接入


```xml
<extra-core>
        <UpdateLoadProvider class="继承抽象类的子类路径"/>
</extra-core>
```

## 提供给插件的抽象类


```java
/**
 * 更新生命周期扩展点抽象类
 *
 * @author James.Zhao
 * @version 5.1.3
 * Created by James.Zhao on 2020/4/8
 */
@API(level = UpdateLoadProvider.CURRENT_LEVEL)
public abstract class AbstractUpdateLoadProvider implements UpdateLoadProvider {
    @Override
    public int currentAPILevel() {
        return CURRENT_LEVEL;
    }

    @Override
    public String mark4Provider() {
        return getClass().getName();
    }

    @Override
    public <T extends AbstractLoadProperty> boolean onLoad(TaskInfoBO bo, AbstractLoad<T> load) {
        return false;
    }

    @Override
    public <T extends AbstractLoadProperty> boolean onCommit(TaskInfoBO bo, AbstractLoad<T> load) {
        return false;
    }

    @Override
    public <T extends AbstractLoadProperty> boolean onRename(TaskInfoBO bo, AbstractLoad<T> load) {
        return false;
    }

    @Override
    public <T extends AbstractLoadProperty> boolean onEnd(TaskInfoBO bo, AbstractLoad<T> load) {
        return false;
    }

    @Override
    public <T extends AbstractLoadProperty> boolean onRollback(TaskInfoBO bo, AbstractLoad<T> load, Throwable throwable) {
        return false;
    }

    @Override
    public <T extends AbstractLoadProperty> boolean onStart(TaskInfoBO bo, AbstractLoad<T> load) {
        return false;
    }
} 
```
