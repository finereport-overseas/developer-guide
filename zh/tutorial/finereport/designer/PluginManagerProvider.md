# PluginManagerProvider

| 属性 | 值 |
| --- | --- |
| 接口类型 | provider |
| 所属模块 | extra-designer |
| 完整类名 | `com.fr.design.fun.PluginManagerProvider` |
| 官方文档 | [查看文档](https://wiki.fanruan.com/display/PD/com.fr.design.fun.PluginManagerProvider) |

## 简介

替换设计器中内置的插件管理入口实现，通常用于 OEM 场景下定制插件管理界面或行为。

## 接口定义

```java
/**
 * 替换插件管理入口
 * @version 10.0
 */
public interface PluginManagerProvider extends Selectable {

    String MARK_STRING = "PluginManagerProvider";

    int CURRENT_LEVEL = 1;

    /**
     * 返回替换后的插件管理 Action
     */
    UpdateAction pluginManagerAction();
}
```

## 使用方式

在插件的 `plugin.xml` 中，通过 `extra-designer` 标签注册实现类：

```xml
<extra-designer>
    <PluginManagerProvider class="com.fr.plugin.xxx.YourClassName"/>
</extra-designer>
```

## 示例代码

```java
public class MyPluginManager extends AbstractPluginManagerProvider {

    @Override
    public UpdateAction pluginManagerAction() {
        return new PluginManagerActionAdapter();
    }

    @Override
    public Selector selector() {
        return new Selector() {
            @Override
            public boolean accept(ObjectHolder holder) {
                return true;
            }
        };
    }

    private static class PluginManagerActionAdapter extends PluginManagerAction {

        @Override
        public void actionPerformed(ActionEvent e) {
            // 调用自定义的插件管理对话框
            OSBasedAction osBasedAction = OSSupportCenter.getAction(PMDialogAction.class);
            osBasedAction.execute();
        }
    }
}
```

## 注意事项

- 推荐继承 `AbstractPluginManagerProvider` 而非直接实现接口，可减少样板代码。
- `selector()` 方法控制该实现是否生效，返回 `true` 表示启用当前实现替换默认插件管理入口。
- 多个实现同时注册时，平台通过 `Selectable` 机制选择第一个 `accept` 返回 `true` 的实现。
