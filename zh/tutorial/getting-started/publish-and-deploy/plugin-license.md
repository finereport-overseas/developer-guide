# 插件授权

当开发者开发的插件想要用户付费购买的时候，需要在插件中加入付费 API，以便在试用期到期后提示用户购买插件。

---

## 关键类

在接入点接口的实现类中，加上付费说明的注解（Annotation），注解的内容如下：

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
     * 开发者根据插件id申请得到的验证码
     *
     * @return 验证码
     */
    String callSignKey() default StringUtils.EMPTY;

    /**
     * 验证码可以写在代码中,也可以写在文件中
     *
     * @return 存验证码的文件的路径
     */
    String callSignKeyPath() default StringUtils.EMPTY;
}
```

---

## 示例实现

我们以主题插件开发为示例：注解 `@Authorize` 用来描述这是一个付费的插件，而下面的代码用于处理认证通过和未通过时的不同逻辑：

```java
if (PluginContexts.currentContext().isAvailable()) {
    // 做认证通过的事情
} else {
    // 做认证未通过的事情
}
```

完整的主题插件示例实现：

```java
@Authorize(callSignKey = Constants.PLUING_ID)
public class ThemeGreen extends AbstractThemeVariousProvider {

    @Override
    public String name() {
        return "AcrossGreen";
    }

    @Override
    public String text() {
        return "横向目录";
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

上述示例中，`callSignKey` 的值就是插件的 ID，需要和 `plugin.xml` 中的 `id` 字段一致。如果插件试用期已过，那么就会返回空的路径，导致这个插件不会起作用。当然，也可以使用其他更多的方式进行购买提醒。

---

## 注意事项

1. 注解和授权控制代码并非要在一个类上；
2. 一般来说，同时含有设计器接口和非设计器接口的插件，`@Authorize` 注解只应该加在除 `extra-designer` 标签以外的接入点实现类上。

---

## 效果自测

| 报表注册 | 插件 lic | 试用期内 | 设计器 *1 | 设计器中的服务器环境 *2 | 服务器环境 *3 |
| :---: | :---: | :---: | :---: | :---: | :---: |
| 未注册 | 无/有 | 无限试用期 | 正常 | 正常 | 正常 |
| 注册 | 无 | 是 | 正常 | 正常 | 正常 |
| 注册 | 无 | 否 | 过期 | 过期 | 过期 |
| 注册 | 有 | 无 | 已授权 | 已授权 | 已授权 |

*1：设计器是指包含有为设计器部分做的插件，比如设计器的菜单、对话框等；  
*2：指的是包含有为服务器部分做的插件，在设计器中包含有的 jetty 服务器环境的运行情况；  
*3：指的是包含有为服务器部分做的插件，在 tomcat 等其他独立 Java Web 容器中的运行情况。

---

## 插件包授权

10.0.2（stable-12.27 以后）中添加了插件包的概念（即客户可以购买一个集合的插件）。

在插件的 `@Authorize` 注解中添加了 `groupSignKey` 属性，用来标记自己的插件所属的插件组 ID：

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
     * 开发者根据插件id申请得到的验证码
     *
     * @return 验证码
     */
    String callSignKey() default StringUtils.EMPTY;

    /**
     * 验证码可以写在代码中,也可以写在文件中
     *
     * @return 存验证码的文件的路径
     */
    String callSignKeyPath() default StringUtils.EMPTY;

    /**
     * 插件所属group
     */
    String groupSignKey() default StringUtils.EMPTY;
}
```

> 为了保障您的分成权益，在添加插件包注解前请务必向官方确认，您的插件确已所属某个插件包。
