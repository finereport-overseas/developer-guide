# Beginner Tutorial - Locale Finder

## Introduction
In this tutorial, we will introduce a provider called *LocaleFinder*. It helps change text at the front end with the user's locale language. We can use this interface to define locale String in our own plugin or redefine default locale String in FineReport.
```java
package com.fr.stable.fun;
 
public interface LocaleFinder extends Level {
   
    String MARK_STRING = "LocaleFinder";
   
    int CURRENT_LEVEL = 1;
   
    /**
     * Find the path of locale language files
     *
     * @return the path of locale language files
     */
    String find();
}
```

## Example
### 1) Implement the provider
```java
package com.fr.plugin.locale;
  
import com.fr.plugin.transform.ExecuteFunctionRecord;
import com.fr.plugin.transform.FunctionRecorder;
import com.fr.stable.fun.impl.AbstractLocaleFinder;
@FunctionRecorder
public class DemoLocaleFinder extends AbstractLocaleFinder {
    @Override
    @ExecuteFunctionRecord
    public String find() {
        return "com/fr/plugin/locale/demo";
    }
}
```
The path above refers to the demo.properties under resources/com/fr/plugin/locale.

### 2) Define properties files
The demo.properties is used for default, demo_en_US.properties is for English locale, and demo_zh_CN.properties is for Chinese. You got the point.

Inside a properties file, there is key-value for your locale String. See the example below.

demo.properties:
```properties
Fine-Plugin_demo_key=
Fine-Plugin_demo_XXXX=
```
demo_en.properties:
```properties
Fine-Plugin_demo_key=locale finder demo
Fine-Plugin_demo_XXXX=XXXX
```
demo_zh.properties:
```properties
Fine-Plugin_demo_key=\u56fd\u9645\u5316\u793a\u4f8b
Fine-Plugin_demo_XXXX=XXXX
```

### 3) Use locale String in your plugin
The usage of this provider differs in the designer, the server, and the Web.

Designer:
```java
com.fr.design.i18n.Toolkit.i18nText(key)
```
Server:
```java
InterProviderFactory.getProvider().getLocText(key)
```
Web:
```js
FR.i18nText(key) // When preivewed in FineReport
BI.i18nText(key) // On the decision-making platform
```

### 4) Register the plugin
```xml
<extra-core>
   <LocaleFinder class="com.fr.plugin.locale.demo.DemoLocaleFinder"/>
</extra-core>
```