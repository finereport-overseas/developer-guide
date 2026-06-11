# RegPaneProvider

| 属性 | 值 |
| --- | --- |
| 接口类型 | provider |
| 所属模块 | extra-designer |
| 完整类名 | `com.fr.design.fun.RegPaneProvider` |
| 官方文档 | [查看文档](https://wiki.fanruan.com/display/PD/com.fr.design.fun.RegPaneProvider) |

## 简介

替换设计器中内置的正则表达式输入面板（`RegFieldPane`），用于自定义正则校验规则的编辑界面。

## 接口定义

```java
public interface RegPaneProvider extends Immutable {

    int CURRENT_LEVEL = 1;

    String XML_TAG = "RegPaneProvider";

    /**
     * 创建并返回自定义的正则表达式输入面板
     */
    RegFieldPane createRegPane();
}
```

## 使用方式

在插件的 `plugin.xml` 中，通过 `extra-designer` 标签注册实现类：

```xml
<extra-designer>
    <RegPaneProvider class="com.fr.plugin.xxx.YourClassName"/>
</extra-designer>
```

## 示例代码

```java
public class MyRegPaneProvider implements RegPaneProvider {

    @Override
    public RegFieldPane createRegPane() {
        // 返回自定义的正则表达式输入面板
        return new MyRegFieldPane();
    }
}

public class MyRegFieldPane extends RegFieldPane {

    @Override
    // 重写面板初始化或校验逻辑
    protected void initComponents() {
        super.initComponents();
        // 添加自定义 UI 组件或修改默认行为
    }
}
```

## 注意事项

- 该接口继承 `Immutable`，运行期不可热替换。
- `createRegPane()` 每次调用应返回新实例，避免共享状态导致的 UI 冲突。
- 同时注册多个实现时，仅第一个生效。
