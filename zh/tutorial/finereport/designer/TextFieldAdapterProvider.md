# TextFieldAdapterProvider

| 属性 | 值 |
| --- | --- |
| 接口类型 | provider |
| 所属模块 | extra-designer |
| 完整类名 | `com.fr.design.fun.TextFieldAdapterProvider` |
| 官方文档 | [查看文档](https://wiki.fanruan.com/display/PD/com.fr.design.fun.TextFieldAdapterProvider) |

## 简介

替换设计器中内置的带错误提示的文本输入框适配器（`ErrorMsgTextFieldAdapter`），用于自定义输入框的错误信息展示行为。

## 接口定义

```java
public interface TextFieldAdapterProvider extends Immutable {

    String XML_TAG = "TextFieldAdapterProvider";

    int CURRENT_LEVEL = 1;

    /**
     * 创建并返回自定义的文本框适配器
     */
    ErrorMsgTextFieldAdapter createTextFieldAdapter();
}
```

## 使用方式

在插件的 `plugin.xml` 中，通过 `extra-designer` 标签注册实现类：

```xml
<extra-designer>
    <TextFieldAdapterProvider class="com.fr.plugin.xxx.YourClassName"/>
</extra-designer>
```

## 示例代码

```java
public class MyTextFieldAdapterProvider implements TextFieldAdapterProvider {

    @Override
    public ErrorMsgTextFieldAdapter createTextFieldAdapter() {
        return new MyErrorMsgTextFieldAdapter();
    }
}

public class MyErrorMsgTextFieldAdapter extends ErrorMsgTextFieldAdapter {

    @Override
    // 重写错误提示的显示逻辑，例如改为弹窗提示
    public void showErrorMsg(String errorMsg) {
        JOptionPane.showMessageDialog(null, errorMsg, "输入错误", JOptionPane.ERROR_MESSAGE);
    }
}
```

## 注意事项

- 该接口继承 `Immutable`，运行期不可热替换。
- `createTextFieldAdapter()` 每次调用应返回新实例。
- 同时注册多个实现时，仅第一个生效。
