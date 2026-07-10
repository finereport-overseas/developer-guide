# PreviewProvider

| Property | Value |
| --- | --- |
| Module | extra-designer |
| Full Class Name | `com.fr.design.fun.PreviewProvider` |
| Official Docs | [View Documentation](https://wiki.fanruan.com/display/PD/PreviewProvider) |

---

## 1. Special Terms

None

## 2. Background and Use Cases

`PreviewProvider` is the designer-side interface for preview modes, used in conjunction with `ActorProvider`. When a plugin introduces a new preview mode, a corresponding new preview option must be added in the designer. The `PreviewProvider` interface is used to provide that new preview option in the designer.

## 3. Interface Introduction

```java
package com.fr.design.fun;

import com.fr.design.mainframe.JTemplate;
import com.fr.stable.Filter;
import com.fr.stable.fun.mark.Mutable;

import java.util.Map;

/**
 * @author richie
 * @date 2015-03-19
 * @since 8.0
 * Custom preview mode interface.
 */
public interface PreviewProvider extends Mutable, Filter<JTemplate> {

    String MARK_STRING = "PreviewProvider";

    int CURRENT_LEVEL = 1;


    /**
     * Name of the dropdown popup menu item.
     * @return popup menu name
     */
    String nameForPopupItem();

    /**
     * Icon path for the dropdown popup menu item.
     * @return icon path
     */
    String iconPathForPopupItem();

    /**
     * Large icon path.
     * @return large icon path
     */
    String iconPathForLarge();

    /**
     * Event triggered when the dropdown menu item is clicked.
     * @param jt current template object
     */
    void onClick(JTemplate<?, ?> jt);

    /**
     * Integer used to identify the preview type.
     * @return preview type code
     */
    int previewTypeCode();

    /**
     * Default parameter map carried by this preview mode.
     * @return parameter map
     */
    Map<String, Object> parametersForPreview();

    /**
     * How the template is handled under this preview mode.
     * Paged/fill-report modes compute and output the template; mobile preview directly outputs the URL.
     * @return handling mode
     */
    String getActionType();


}
```

## 4. Supported Versions

| Product Line | Version | Supported | Notes |
| --- | --- | --- | --- |
| FR | 8.0 | Yes |  |
| FR | 9.0 | Yes |  |
| FR | 10.0 | Yes |  |
| FR | 11.0 | Yes |

## 5. Plugin Registration

```xml
<extra-designer>
        <PreviewProvider class="your class name"/>
</extra-designer>
```

## 6. How It Works

```java
package com.fr.design.mainframe;

...

public class JWorkBook extends JTemplate<WorkBook, WorkBookUndoState> {

   ...

    /**
     * Supported preview modes.
     *
     * @return preview interfaces
     */
    @Override
    public PreviewProvider[] supportPreview() {
        PreviewProvider[] templatePreviews = super.supportPreview();
        return ArrayUtils.addAll(new PreviewProvider[]{
                new PagePreview(), new WritePreview(), new ViewPreview(), new WriteEnhancePreview(), new MobilePreview()
        }, templatePreviews);
    }

    /**
     * Preview menu items.
     *
     * @return preview menu items
     */
    @Override
    public UIMenuItem[] createMenuItem4Preview() {
        List<UIMenuItem> menuItems = new ArrayList<UIMenuItem>();
        PreviewProvider[] previewProviders = supportPreview();
        for (final PreviewProvider provider : previewProviders) {
            UIMenuItem item = new UIMenuItem(provider.nameForPopupItem(), BaseUtils.readIcon(provider.iconPathForPopupItem()));
            item.addActionListener(new ActionListener() {
                @Override
                public void actionPerformed(ActionEvent e) {
                    provider.onClick(JWorkBook.this);
                }
            });
            menuItems.add(item);
        }
        return menuItems.toArray(new UIMenuItem[menuItems.size()]);
    }

    

    @Override
    /**
     *
     */
    public Icon getPreviewLargeIcon() {
        PreviewProvider provider = getPreviewType();
        String iconPath = provider.iconPathForLarge();
        return BaseUtils.readIcon(iconPath);
    }

    ...
}
```

## 7. Limitations

`previewTypeCode` has a design flaw: it does not account for potential conflicts between multiple plugins registering the same type code. Text-based identifiers have clearer semantics and far lower collision probability. It is recommended that developers always return `0` here and instead use the `parametersForPreview` method to specify the `op` parameter, which associates with the corresponding `ActorProvider`.

`getActionType` is used to specify the URL parameter that carries the report path. The default value is `viewlet`. If a corresponding `ActorProvider` requires a different parameter name, this method can be overridden to specify it.

The `accept` interface is the entry point called when the designer preview is clicked, to determine whether this implementation applies. The default behavior does not require override.

## 8. Useful Links

Demo: [demo-preview-provider](https://code.fanruan.com/hugh/demo-preview-provider)

## 9. Open Source Examples

Disclaimer: All open-source examples in the documentation are developed and provided by individual developers for reference and learning purposes only. Neither the developers nor the official team are obligated to provide instruction or guidance on any outcomes related to open-source examples. Any commercial use is entirely at the user's own risk.
