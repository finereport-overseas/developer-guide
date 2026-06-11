# JavaScriptPlaceHolder

| 属性 | 值 |
| --- | --- |
| 所属模块 | extra-report (stable) |
| 完整类名 | `com.fr.stable.fun.JavaScriptPlaceHolder` |
| 官方文档 | [查看文档](https://wiki.fanruan.com/display/PD/JavaScriptPlaceHolder) |

---

## 一、特殊名词介绍

无

## 二、背景、场景介绍

与内部JS扩展这样引入插件内部的js资源相对应，JavaScriptPlaceHolder接口主要用于引入一些第三方的js资源，常见于第三方图表集成、第三方UI库引入等场景。

## 三、接口介绍


```java
package com.fr.stable.fun;

import com.fr.stable.StringUtils;
import com.fr.stable.fun.mark.Mutable;

/**
 * Created by richie on 16/4/26.
 * 脚本占位,用于在page.html或者form.html中引入脚本
 */
public interface JavaScriptPlaceHolder extends Mutable {

    String MARK_STRING = "JavaScriptPlaceHolder";

    int CURRENT_LEVEL = 2;

    /**
     * 占位的内容
     * @return 内容
     */
    String placeHolderContent();

    /**
     * 脚本内容
     * @return 脚本描述对象
     */
    ScriptTag[] holderScripts();

    class ScriptTag {

        private String type;
        private String src;
        private String text;

        public static ScriptTag build() {
            return new ScriptTag();
        }

        private ScriptTag() {

        }

        public ScriptTag type(String type) {
            this.type = type;
            return this;
        }

        public ScriptTag src(String src) {
            this.src = src;
            return this;
        }

        public ScriptTag text(String text) {
            this.text = text;
            return this;
        }

        public String toTag() {
            StringBuilder sb = new StringBuilder();
            sb.append("<script");
            if (StringUtils.isNotEmpty(type)) {
                sb.append(" ");
                sb.append("type=");
                sb.append("\"");
                sb.append(type);
                sb.append("\"");
            }
            if (StringUtils.isNotEmpty(src)) {
                sb.append(" ");
                sb.append("src=");
                sb.append("\"");
                sb.append(src);
                sb.append("\"");
            }
            sb.append(">");
            if (StringUtils.isNotEmpty(text)) {
                sb.append(text);
            }
            sb.append("</script>");
            return sb.toString();
        }
    }

}


```


```java
package com.fr.stable.fun.impl;

import com.fr.stable.fun.JavaScriptPlaceHolder;
import com.fr.stable.fun.mark.API;

/**
 * Created by richie on 16/4/26.
 */
@API(level = JavaScriptPlaceHolder.CURRENT_LEVEL)
public abstract class AbstractJavaScriptPlaceHolder extends AbstractProvider implements JavaScriptPlaceHolder {

    public int currentAPILevel() {
        return CURRENT_LEVEL;
    }

    public String mark4Provider() {
        return this.getClass().getName();
    }

    public String placeHolderContent() {
        StringBuilder sb = new StringBuilder();
        ScriptTag[] scripts = holderScripts();
        if (scripts != null) {
            for (ScriptTag script : scripts) {
                sb.append(script.toTag());
            }
        }
        return sb.toString();
    }

    public ScriptTag[] holderScripts() {
        return new ScriptTag[0];
    }
}


```

## 四、支持版本

| 产品线 | 版本 | 支持情况 | 备注 |
| --- | --- | --- | --- |
| FR | 8.0 | 支持 |  |
| FR | 9.0 | 支持 |  |
| FR | 10.0 | 支持 |  |
| FR | 11.0 | 支持 |
| BI | 3.6 | 支持 |  |
| BI | 4.0 | 支持 |  |
| BI | 5.1 | 支持 |  |
| BI | 5.1.2 | 支持 |  |
| BI | 5.1.3 | 支持 |  |

## 五、插件注册

注册cpt报表预览引入外部JS


```xml
<extra-report>
        <JavaScriptPlaceHolder class="your class name"/>
</extra-report>
```

注册决策报表预览引入外部JS


```xml
<extra-form>
        <JavaScriptPlaceHolder class="your class name"/>
</extra-form>
```

## 六、原理说明

cpt报表：当ReportletDealWith#dealWithHtml 生成html页面内容的时候，会直接从插件引擎中读取相关注册的接口进行生效ReportletDealWith#createScriptPlaceHolderString。

决策报表：AbstractFormActor#initMap4Form 初始化html内容是，会直接从插件引擎中读取相关注册的接口进行生效AbstractFormActor#createScriptPlaceHolderString。

## 七、特殊限制说明

接口的加载落后于com.fr.stable.fun.JavaScriptFileHandler，但优先于com.fr.decision.fun.WebResourceProvider

## 八、常用链接

三组常见引入JS和CSS的插件接口对比

demo地址：[demo-java-script-place](https://code.fanruan.com/hugh/demo-java-script-place)

com.fr.stable.fun.StylePlaceHolder

## 九、开源案例

免责声明：所有文档中的开源示例，均为开发者自行开发并提供。仅用于参考和学习使用，开发者和官方均无义务对开源案例所涉及的所有成果进行教学和指导。若作为商用一切后果责任由使用者自行承担。
