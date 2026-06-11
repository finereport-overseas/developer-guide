# StylePlaceHolder

| 属性 | 值 |
| --- | --- |
| 所属模块 | extra-report (stable) |
| 完整类名 | `com.fr.stable.fun.StylePlaceHolder` |
| 官方文档 | [查看文档](https://wiki.fanruan.com/display/PD/StylePlaceHolder) |

---

## 一、特殊名词介绍

无

## 二、背景、场景介绍

与内部CSS扩展这样引入插件内部的CSS资源相对应，StylePlaceHolder接口主要用于引入一些第三方的css资源，常见于第三方图表集成、第三方UI库引入等场景。仅对cpt报表和决策报表有效

## 三、接口介绍


```java
package com.fr.stable.fun;

import com.fr.stable.StringUtils;
import com.fr.stable.fun.mark.Mutable;

/**
 * Created by richie on 16/4/26.
 * 样式表占位,用于在page.html或者form.html中引入样式表
 */
public interface StylePlaceHolder extends Mutable {

    String MARK_STRING = "StylePlaceHolder";

    int CURRENT_LEVEL = 1;

    /**
     * 占位的内容
     * @return 内容
     */
    String placeHolderContent();

    /**
     * 脚本内容
     * @return 脚本描述对象
     */
    LinkTag[] holderLinks();

    class LinkTag {

        private String type;
        private String href;
        private String rel;

        public static LinkTag build() {
            return new LinkTag();
        }

        private LinkTag() {

        }

        public LinkTag type(String type) {
            this.type = type;
            return this;
        }

        public LinkTag href(String href) {
            this.href = href;
            return this;
        }

        public LinkTag rel(String rel) {
            this.rel = rel;
            return this;
        }

        public String toTag() {
            StringBuilder sb = new StringBuilder();
            sb.append("<link");
            if (StringUtils.isNotEmpty(rel)) {
                sb.append(" rel=");
                sb.append("\"");
                sb.append(rel);
                sb.append("\"");
            }
            if (StringUtils.isNotEmpty(type)) {
                sb.append(" type=");
                sb.append("\"");
                sb.append(type);
                sb.append("\"");
            }
            if (StringUtils.isNotEmpty(href)) {
                sb.append(" href=");
                sb.append("\"");
                sb.append(href);
                sb.append("\"");
            }

            sb.append("/>");
            return sb.toString();
        }
    }
}


```


```java
package com.fr.stable.fun.impl;

import com.fr.stable.fun.StylePlaceHolder;
import com.fr.stable.fun.mark.API;

/**
 * Created by richie on 16/4/26.
 */
@API(level = StylePlaceHolder.CURRENT_LEVEL)
public abstract class AbstractStylePlaceHolder extends AbstractProvider implements StylePlaceHolder {

    public int currentAPILevel() {
        return CURRENT_LEVEL;
    }


    public String placeHolderContent() {
        StringBuilder sb = new StringBuilder();
        LinkTag[] linkTags = holderLinks();
        if (linkTags != null) {
            for (LinkTag link : linkTags) {
                sb.append(link.toTag());
            }
        }
        return sb.toString();
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
        <StylePlaceHolder class="your class name"/>
</extra-report>
```

注册决策报表预览引入外部JS


```xml
<extra-form>
        <StylePlaceHolder class="your class name"/>
</extra-form>
```

## 六、原理说明

cpt报表：当ReportletDealWith#dealWithHtml 生成html页面内容的时候，会直接从插件引擎中读取相关注册的接口进行生效ReportletDealWith#createStylePlaceHolderString。

决策报表：AbstractFormActor#initMap4Form 初始化html内容是，会直接从插件引擎中读取相关注册的接口进行生效AbstractFormActor#createStylePlaceHolderString。

## 七、特殊限制说明

接口的加载落后于com.fr.stable.fun.CssFileHandler，但优先于com.fr.decision.fun.WebResourceProvider

## 八、常用链接

三组常见引入JS和CSS的插件接口对比

demo地址：[demo-style-place](https://code.fanruan.com/hugh/demo-style-place)

com.fr.stable.fun.JavaScriptPlaceHolder

## 九、开源案例

免责声明：所有文档中的开源示例，均为开发者自行开发并提供。仅用于参考和学习使用，开发者和官方均无义务对开源案例所涉及的所有成果进行教学和指导。若作为商用一切后果责任由使用者自行承担。
