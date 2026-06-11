# ServerTablesProvider

| 属性 | 值 |
| --- | --- |
| 所属模块 | extra-core (FineBI) |
| 官方文档 | [查看文档](https://wiki.fanruan.com/pages/viewpage.action?pageId=158402918) |

---

## 接口作用

允许客户通过用户信息控制服务器数据集的展示范围

## 接口内容

### 主要接口


```java
@Careful
public interface ServerTablesProvider extends Immutable {

    String XML_TAG = "ServerTableListProvider";

    int CURRENT_LEVEL = 1;

    /**
     * 依据用户名获取当前用户可以看到的服务器数据集的名称集合
     *
     * @param userId 用户ID
     * @return 服务器数据集的名字（原始名）
     */
    List<String> getServerTableNames(String userId);
}


```

## 接口接入


```xml
<extra-core>
    <ServerTableListProvider class="com.fr.plugin.demo.server.CustomServerTablesProvider"/>
</extra-core>
```

## 提供给插件的抽象类


```java
@API(level = ServerTablesProvider.CURRENT_LEVEL)
public abstract class AbstractServerTablesProvider implements ServerTablesProvider {

    public int currentAPILevel() {
        return CURRENT_LEVEL;
    }

    @Override
    public int layerIndex() {
        return 0;
    }
}
```
