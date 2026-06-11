# CustomI18NProvider

| 属性 | 值 |
| --- | --- |
| 所属模块 | extra-report (stable) |
| 完整类名 | `com.fr.stable.fun.CustomI18NProvider` |
| 官方文档 | [查看文档](https://wiki.fanruan.com/display/PD/CustomI18NProvider) |

---

## 一、特殊名词介绍

无

## 二、背景、场景介绍

CustomI18NProvider接口仅用于支持扩展内置I18N模板函数函数，对国际化的键值进行动态的变更或扩展。

## 三、接口介绍


```java
package com.fr.stable.fun;

import com.fr.stable.fun.mark.Selectable;

import java.util.Locale;

/**
 * Created by kerry on 4/8/21
 */
public interface CustomI18NProvider extends Selectable {
    String MARK_STRING = "CustomI18NProvider";
    int CURRENT_LEVEL = 1;

    /**
     * 根据key和语言环境获取国际化值
     * @param key 国际化key
     * @param locale 语言环境
     * @return 国际化值
     */
   String getLocText(String key, Locale locale);

}


```

## 四、支持版本

| 产品线 | 版本 | 支持情况 | 备注 |
| --- | --- | --- | --- |
| FR | 10.0 | 支持 |  |
| FR | 11.0 | 支持 |

## 五、插件注册


```xml
<extra-report>
        <CustomI18NProvider class="your class name"/>
</extra-report>
```

## 六、原理说明


```java
package com.fr.function;

import com.fr.stable.fun.CustomI18NProvider;
import com.fr.general.GeneralContext;
import com.fr.general.GeneralUtils;
import com.fr.locale.InterProviderFactory;
import com.fr.log.FineLoggerFactory;
import com.fr.plugin.injectable.PluginModule;
import com.fr.script.AbstractFunction;
import com.fr.stable.Constants;
import com.fr.stable.Primitive;
import com.fr.stable.StringUtils;
import com.fr.stable.UtilEvalError;
import com.fr.stable.bridge.ObjectHolder;
import com.fr.stable.plugin.ExtraReportClassManagerProvider;

import java.util.Locale;
import java.util.Properties;
import java.util.Set;

public class I18N extends AbstractFunction {

    /**
     * 获取参数国际化值
     *
     * @param args 参数
     * @return 国际化后的值
     */
    public Object run(Object[] args) {
        if (args.length < 1) {
            return Primitive.ERROR_NAME;
        }

        Object var = getCalculatorProvider().resolveVariable(Constants.__LOCALE__);
        if (var == null) {
            try {
                var = getCalculatorProvider().eval("=$" + Constants.__LOCALE__);
            } catch (UtilEvalError e) {
                FineLoggerFactory.getLogger().error(e.getMessage(), e);
            }
        }

        Locale locale = GeneralContext.getLocale();
        if (var instanceof Locale) {
            locale = (Locale) var;
        }

        try {
            String key = GeneralUtils.objectToString(args[0]);
            String locValue = getLocTextFromCustom(key, locale);
            if (!findErrValue(key, locValue)) {
                return locValue;
            }
            // 先从 server 中读。
            locValue = InterProviderFactory.getProvider().getLocText(key, locale);
            // 找不到，则返回 key 的名字
            if (findErrValue(key, locValue)) {
                String newKey = I18nCompatibleUtils.getNewKeyFromOldKey(key);
                // 从 server 中再来一次
                locValue = InterProviderFactory.getProvider().getLocText(newKey, locale);
                if (findErrValue(newKey, locValue)) {
                    // 然后从 web 中读。
                    locValue = InterProviderFactory.getClientProvider().getEntireKV(locale).get(newKey);
                    if (StringUtils.isEmpty(locValue)) {
                        return newKey;
                    }
                }
            }
            return locValue;
        } catch (Exception e) {
            FineLoggerFactory.getLogger().error(e.getMessage(), e);
            return args[0];
        }
    }

    private String getLocTextFromCustom(String key, Locale locale){
        ExtraReportClassManagerProvider extraReportClassManager = PluginModule.getAgent(PluginModule.ExtraReport);
        if (extraReportClassManager != null) {
            Set<CustomI18NProvider> providers = extraReportClassManager.getArray(CustomI18NProvider.MARK_STRING);
            for (CustomI18NProvider provider : providers) {
                if (provider.selector().accept(new ObjectHolder())) {
                    return provider.getLocText(key, locale);
                }
            }
        }
        return key;
    }

    /**
     * 对于 provider 来说， 如果找不到， 是返回原值的。
     *
     * @param key      国际化的 key 值
     * @param locValue 本地 value 值
     * @return 是 / 否
     */
    private boolean findErrValue(String key, String locValue) {

        return StringUtils.equals(key, locValue);
    }


    public Type getType() {
        return REPORT;
    }

    /**
     * @description: 用来解决 engine-i18n 的命名规范后，之前保存到模板中的值读取不到国际化的问题。
     * @author: Harrison Liu
     * @date: 2018/8/27
     */
    private enum I18nCompatibleUtils {

        Engine_Print_Compatible("Print", "Fine-Engine_Print"),
        Engine_Export("Export", "Fine-Engine_Export"),
        Engine_Export_Excel_Simple_Alias("Export-Excel-Simple","Fine-Engine_Export_Excel_Simple"),

        Utils_Print_Client("Utils-Print[Client]","Fine-Engine_Utils_Print[Client]"),

        Engine_Add("Add", "Fine-Engine_Add"),
        Engine_Record("Record", "Fine-Engine_Record"),
        Engine_Row("Row", "Fine-Engine_Report_Row"),

        Engine_Report("Report", "Fine-Engine_Report");

        private final String rawKey;
        private final String newKey;

        private static final String filePath = "i18n_deprecated.properties";
        private static final Properties PROPERTIES = new Properties();

        static {
            initProperties();
        }

        I18nCompatibleUtils(String rawKey, String newKey) {
            this.rawKey = rawKey;
            this.newKey = newKey;
        }


        public String getRawKey() {
            return rawKey;
        }

        public String getNewKey() {
            return newKey;
        }

        /**
         * @description: 从旧值读新值
         * @param: [rawKey 原始值]
         * @return: java.lang.String
         * @author: Harrison Liu
         * @date: 2018/8/27
         */
        public static String getNewKeyFromOldKey(String rawKey) {
            if (StringUtils.isEmpty(rawKey)) {
                return rawKey;
            }
            String value = PROPERTIES.getProperty(rawKey);
            if (value != null) {
                return value;
            }
            for (I18nCompatibleUtils i18nMapping : I18nCompatibleUtils.values()) {
                if (StringUtils.equals(i18nMapping.getRawKey(), rawKey)) {
                    return i18nMapping.getNewKey();
                }
            }

            return rawKey;
        }

        private static void initProperties() {

            try {
                PROPERTIES.load(I18N.class.getResourceAsStream(filePath));
            } catch (Exception e) {
                FineLoggerFactory.getLogger().error(e.getMessage(), e);
            }
        }
    }
}


```

## 七、特殊限制说明

目前从设计上看，这个接口开得过于窄了。如果不是不得已，尽量不要使用该接口。

如果要扩大接口得使用范围，就需要做较多的封装来适配前端得国际化函数

## 八、常用链接

demo地址：[demo-custom-i18n-provider](https://code.fanruan.com/hugh/demo-custom-i18n-provider)

## 九、开源案例

免责声明：所有文档中的开源示例，均为开发者自行开发并提供。仅用于参考和学习使用，开发者和官方均无义务对开源案例所涉及的所有成果进行教学和指导。禁止用于任何商业用途，若作为商用一切后果责任由使用者自行承担。
