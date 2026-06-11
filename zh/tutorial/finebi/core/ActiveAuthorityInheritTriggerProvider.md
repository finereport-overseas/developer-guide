# AuthorityInheritTriggerProvider

| 属性 | 值 |
| --- | --- |
| 接口类型 | provider |
| 所属模块 | extra-core（FineBI） |
| 完整类名 | `com.finebi.common.api.provider.AuthorityInheritTriggerProvider` |
| 官方文档 | [查看文档](https://wiki.fanruan.com/pages/viewpage.action?pageId=158402881) |

## 简介

用于控制是否启用自助数据集原始表权限继承开关的接口。默认不支持该开关（主题内不展示），通过插件实现后可开启此功能。

## 接口定义

```java
public interface AuthorityInheritTriggerProvider extends Mutable {

    String XML_TAG = "AuthorityInheritTriggerProvider";

    /**
     * 是否启用 analysis 原始表的权限继承开关
     */
    boolean originTableInheritTriggerEnable();
}
```

推荐继承抽象基类 `AbstractAuthorityInheritTriggerProvider`，已实现 `currentAPILevel()` 和 `mark4Provider()`：

```java
@API(level = Constants.Extension.NOT_STACKABLE_API_LEVEL)
public abstract class AbstractAuthorityInheritTriggerProvider implements AuthorityInheritTriggerProvider {

    @Override
    public int currentAPILevel() {
        return Constants.Extension.NOT_STACKABLE_API_LEVEL;
    }

    @Override
    public String mark4Provider() {
        return getClass().getName();
    }
}
```

## 使用方式

在插件的 `plugin.xml` 中，通过 `extra-core` 标签注册实现类：

```xml
<extra-core>
    <AuthorityInheritTriggerProvider class="com.fr.plugin.xxx.YourClassName"/>
</extra-core>
```

平台通过以下方式读取插件提供的开关值：

```java
Set<AuthorityInheritTriggerProvider> providers =
    ExtraClassManager.getInstance().getArray(AuthorityInheritTriggerProvider.XML_TAG);
if (CollectionUtils.isNotEmpty(providers)) {
    providers.stream().findAny().ifPresent(i ->
        OriginTableInheritTriggerManager.INSTANCE
            .registerOriginTableInheritTriggerProvider(i.originTableInheritTriggerEnable()));
}
```

## 示例代码

```java
@EnableMetrics
public class ActiveAuthorityInheritTriggerProvider extends AbstractAuthorityInheritTriggerProvider {

    @Override
    @Focus(
        id = "com.finebi.plugin.bi.authority.inherit.ActiveAuthorityInheritTriggerProvider",
        text = "BI原始表权限继承开关",
        source = Original.PLUGIN
    )
    public boolean originTableInheritTriggerEnable() {
        // 返回 true 表示启用原始表权限继承开关
        return true;
    }
}
```

## 注意事项

- 该接口为不可叠加类型（`NOT_STACKABLE_API_LEVEL`），同时注册多个实现时只有一个生效（取 `findAny()`）。
- 默认情况下该开关不展示，只有注册插件实现后才会在主题设置中显示。
