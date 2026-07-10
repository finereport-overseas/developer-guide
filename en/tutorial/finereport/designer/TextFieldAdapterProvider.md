# TextFieldAdapterProvider

| Property | Value |
| --- | --- |
| Interface Type | provider |
| Module | extra-designer |
| Full Class Name | `com.fr.design.fun.TextFieldAdapterProvider` |
| Official Docs | [View Documentation](https://wiki.fanruan.com/display/PD/com.fr.design.fun.TextFieldAdapterProvider) |

## Overview

Replaces the built-in text input field adapter with error message display (`ErrorMsgTextFieldAdapter`) in the designer, allowing customization of error message presentation behavior in input fields.

## Interface Definition

```java
public interface TextFieldAdapterProvider extends Immutable {

    String XML_TAG = "TextFieldAdapterProvider";

    int CURRENT_LEVEL = 1;

    /**
     * Creates and returns a custom text field adapter.
     */
    ErrorMsgTextFieldAdapter createTextFieldAdapter();
}
```

## Usage

Register the implementation class in the plugin's `plugin.xml` via the `extra-designer` tag:

```xml
<extra-designer>
    <TextFieldAdapterProvider class="com.fr.plugin.xxx.YourClassName"/>
</extra-designer>
```

## Example Code

```java
public class MyTextFieldAdapterProvider implements TextFieldAdapterProvider {

    @Override
    public ErrorMsgTextFieldAdapter createTextFieldAdapter() {
        return new MyErrorMsgTextFieldAdapter();
    }
}

public class MyErrorMsgTextFieldAdapter extends ErrorMsgTextFieldAdapter {

    @Override
    // Override the error message display logic, e.g., use a dialog instead
    public void showErrorMsg(String errorMsg) {
        JOptionPane.showMessageDialog(null, errorMsg, "Input Error", JOptionPane.ERROR_MESSAGE);
    }
}
```

## Notes

- This interface extends `Immutable` and cannot be hot-swapped at runtime.
- `createTextFieldAdapter()` should return a new instance on each call.
- If multiple implementations are registered simultaneously, only the first one takes effect.
