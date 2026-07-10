# Plugin Licensing

When a developer wants users to pay for a plugin, they need to integrate the licensing API into the plugin so that users are prompted to purchase once the trial period expires.

---

## Key Class

Add the `@Authorize` annotation to the extension point implementation class. The annotation is defined as follows:

```java
package com.fr.stable.fun;

import com.fr.stable.StringUtils;

import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)
public @interface Authorize {

    /**
     * The verification code obtained by the developer based on the plugin ID.
     *
     * @return verification code
     */
    String callSignKey() default StringUtils.EMPTY;

    /**
     * The verification code can be written directly in code or stored in a file.
     *
     * @return path to the file containing the verification code
     */
    String callSignKeyPath() default StringUtils.EMPTY;
}
```

---

## Example Implementation

Using a theme plugin as an example: the `@Authorize` annotation marks the plugin as paid, and the following code handles the different logic for authorized and unauthorized states:

```java
if (PluginContexts.currentContext().isAvailable()) {
    // logic when authorized
} else {
    // logic when not authorized
}
```

A complete theme plugin implementation:

```java
@Authorize(callSignKey = Constants.PLUING_ID)
public class ThemeGreen extends AbstractThemeVariousProvider {

    @Override
    public String name() {
        return "AcrossGreen";
    }

    @Override
    public String text() {
        return "Horizontal Navigation";
    }

    @Override
    public String coverPath() {
        return "/com/fr/solution/theme/green/files/cover.png";
    }

    @Override
    public String scriptPath() {
        if (PluginContexts.currentContext().isAvailable()) {
            return "/com/fr/solution/theme/green/files/theme.js";
        } else {
            return "";
        }
    }

    @Override
    public String stylePath() {
        if (PluginContexts.currentContext().isAvailable()) {
            return "/com/fr/solution/theme/green/files/style.css";
        } else {
            return "";
        }
    }
}
```

In the example above, the value of `callSignKey` is the plugin ID, which must match the `id` field in `plugin.xml`. If the trial period has expired, an empty path is returned so the plugin has no effect. Of course, other approaches can also be used to prompt users to purchase.

---

## Notes

1. The annotation and the authorization control code do not have to be in the same class.
2. For plugins that include both designer-side and non-designer-side interfaces, the `@Authorize` annotation should generally only be applied to extension point implementation classes outside the `extra-designer` tag.

---

## Self-Testing

| Report Registration | Plugin License | Within Trial Period | Designer *1 | Server Env in Designer *2 | Server Environment *3 |
| :---: | :---: | :---: | :---: | :---: | :---: |
| Not registered | None/Present | Unlimited trial | Normal | Normal | Normal |
| Registered | None | Yes | Normal | Normal | Normal |
| Registered | None | No | Expired | Expired | Expired |
| Registered | Present | N/A | Licensed | Licensed | Licensed |

*1: "Designer" refers to the plugin portion that serves the designer, such as designer menus and dialogs.  
*2: Refers to the plugin portion that serves the server side, running in the Jetty server environment embedded in the designer.  
*3: Refers to the plugin portion that serves the server side, running in an independent Java Web container such as Tomcat.

---

## Plugin Bundle Licensing

Starting from version 10.0.2 (stable-12.27 and later), the concept of a plugin bundle was introduced, allowing customers to purchase a collection of plugins as a group.

A `groupSignKey` attribute was added to the `@Authorize` annotation to mark which plugin group a plugin belongs to:

```java
package com.fr.stable.fun;

import com.fr.stable.StringUtils;

import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)
public @interface Authorize {

    /**
     * The verification code obtained by the developer based on the plugin ID.
     *
     * @return verification code
     */
    String callSignKey() default StringUtils.EMPTY;

    /**
     * The verification code can be written directly in code or stored in a file.
     *
     * @return path to the file containing the verification code
     */
    String callSignKeyPath() default StringUtils.EMPTY;

    /**
     * The plugin bundle this plugin belongs to.
     */
    String groupSignKey() default StringUtils.EMPTY;
}
```

> To protect your revenue share, please confirm with FanRuan officially that your plugin has been assigned to a plugin bundle before adding the bundle annotation.
