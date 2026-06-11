# ReportSessionHelpProvider

| 属性 | 值 |
| --- | --- |
| 所属模块 | extra-report |
| 完整类名 | `com.fr.report.fun.ReportSessionHelpProvider` |
| 官方文档 | [查看文档](https://wiki.fanruan.com/display/PD/ReportSessionHelpProvider) |

---

## 一、特殊名词介绍

无

## 二、背景、场景介绍

该接口主要用于报表计算前处理报表session，从而达到改变某些报表计算的环境参数、报表调整、模板调整等场景。同时因为报表session是在一次报表预览周期内存活的一个对象，那么也可以利用此接口实现对一些无接口但是具备报表session方法调用的逻辑的切入、感知以及干预。

## 三、接口介绍


```java
package com.fr.report.fun;

import com.fr.stable.fun.mark.Mutable;
import com.fr.stable.web.Repository;

/**
 * 计算报表之前对session 的一些特殊处理接口
 * Created by zack on 2016/2/24.
 */
public interface ReportSessionHelpProvider extends Mutable{
    String XML_TAG = "ReportSessionHelpProvider";
    int CURRENT_LEVEL = 1;

    /**
     * 计算报表之前对session 的一些特殊处理（比如填报暂存，把暂存的数据塞进去）
     *
     * @param repo repo
     */
    void dealWithSession(Repository repo);

}

```


```java
package com.fr.stable.web;

import com.fr.common.annotations.Open;
import com.fr.stable.StringUtils;
import com.fr.stable.script.CalculatorProvider;

import javax.servlet.http.HttpServletRequest;
import java.util.Map;

@Open
public interface Repository {

	 BrowserProvider getBrowser();

    /**
     * 获取资源地址
     * @param source 资源
     * @param type 类型
     * @return 地址
     */
	 String checkoutObject(Object source, String type);

	/**
	 * 获取图片资源地址,原方法checkoutObject里面带有移动端兼容逻辑,新增一个针对图片的资源接口
	 * @param source 图片对象
	 * @return 图片资源地址
	 */
	default String checkoutImage(Object source) {
		// 为了保证插件兼容性，添加默认实现
		// zack:默认实现返回""
		return StringUtils.EMPTY;
	}

	 String getServletURL();

	 String getSessionID();

	 HttpServletRequest getHttpServletRequest();

     String getHTTPRequestParameter(String parameterName);

    /**
     * 获取国际化字符串
     * @param tpl 原始字符串
     * @return  国际化字符串
     */
	 String i18n(String tpl);

	 CalculatorProvider getCalculator();

	 int getResolution();

	 Map<String, Object> getReportParameterMap();

     Device getDevice();

	 double getFontScale();

	 void setFontScale(double scale);
}
```


```java
/*
 * Copyright(c) 2001-2011, FineReport Inc, All Rights Reserved.
 */
package com.fr.web;

import com.fr.base.ImageProvider;
import com.fr.base.ServerConfig;
import com.fr.base.TemplateUtils;
import com.fr.base.mobile.MobileConstants;
import com.fr.data.NetworkHelper;
import com.fr.general.ComparatorUtils;
import com.fr.general.GeneralUtils;
import com.fr.general.http.HttpToolbox;
import com.fr.general.web.ParameterConstants;
import com.fr.json.JSONObject;
import com.fr.plugin.injectable.PluginModule;
import com.fr.script.Calculator;
import com.fr.stable.BaseConstants;
import com.fr.stable.CheckOut;
import com.fr.stable.CodeUtils;
import com.fr.stable.CommonCodeUtils;
import com.fr.stable.Constants;
import com.fr.stable.StringUtils;
import com.fr.stable.fun.ServletURLTransformer;
import com.fr.stable.plugin.ExtraClassManagerProvider;
import com.fr.stable.script.CalculatorProvider;
import com.fr.stable.web.BrowserProvider;
import com.fr.stable.web.Device;
import com.fr.stable.web.Repository;
import com.fr.stable.web.SessionProvider;
import com.fr.third.org.apache.http.NameValuePair;
import com.fr.web.core.TemplateSessionIDInfo;
import com.fr.web.impl.FineImageResource;
import com.fr.web.impl.ImageResource;
import com.fr.web.utils.WebUtils;

import javax.servlet.http.HttpServletRequest;
import java.awt.Image;
import java.util.ArrayList;
import java.util.Iterator;
import java.util.List;
import java.util.Locale;
import java.util.Map;
import java.util.Set;

/**
 * Created by IntelliJ IDEA.
 * User   : Richer
 * Version: 6.5.6
 * Date   : 11-8-10
 * Time   : 下午6:15
 */
public class RepositoryDeal implements Repository {

    /*
     * alex:通过Servlet方式访问Session的Repository
     */

    private HttpServletRequest req;
    protected TemplateSessionIDInfo sessionIDInfor;
    private String servletURL; // 访问servlet的url
    private Browser browser; // 浏览器类型
    private Calculator i18nCalculator;
    private int resolution = Constants.DEFAULT_WEBWRITE_AND_SCREEN_RESOLUTION;

    private double fontScale = 1;

    public RepositoryDeal(HttpServletRequest req, TemplateSessionIDInfo sessionIDInfor) {
        this(req, sessionIDInfor, Constants.DEFAULT_WEBWRITE_AND_SCREEN_RESOLUTION);
    }

    public RepositoryDeal(HttpServletRequest req, TemplateSessionIDInfo sessionIDInfor, int resolution) {
        this.req = req;
        this.sessionIDInfor = sessionIDInfor;

        // 取出在VersionTransition#saveCalculatorContext中保存的servlet地址，
        this.servletURL = (String) Calculator.getSavedVariables().get(CalculatorProvider.SERVLET_URL);
        if (this.servletURL == null) {
            this.servletURL = NetworkHelper.createServletURL(req, ServerConfig.getInstance().getReportServletName());
        }
        this.browser = req == null ? new Browser() : Browser.resolve(req);
        this.resolution = resolution;

        initCalculator();
    }

    private void initCalculator() {
        if (this.i18nCalculator == null) {
            this.i18nCalculator = Calculator.createCalculator();

            Locale locale = null;
            String localeString = sessionIDInfor == null ? StringUtils.EMPTY : (String) sessionIDInfor.getParameterValue(Constants.__FR_LOCALE__);
            if (StringUtils.isNotBlank(localeString)) {
                locale = GeneralUtils.createLocale(localeString);
            } else {
                locale = WebUtils.getLocale(req);
            }

            this.i18nCalculator.set(Constants.__LOCALE__, locale);

            this.i18nCalculator.setAttribute(SessionProvider.KEY, sessionIDInfor);
        }
    }

    @Override
    public BrowserProvider getBrowser() {
        return browser;
    }

    @Override
    public Device getDevice() {
        return WebUtils.getDevice(req);
    }

    @Override
    public double getFontScale() {
        return this.fontScale;
    }

    @Override
    public void setFontScale(double scale) {
        this.fontScale = scale;
    }

    /**
     * 获取资源地址
     *
     * @param source 资源
     * @param type   类型
     * @return 地址
     */
    @Override
    public String checkoutObject(Object source, String type) {
        String prefix = servletURL;
        ServletURLTransformer transformer = findServletURLTransformer();
        if (source == null && ComparatorUtils.equals(BaseConstants.CHECKOUTIMAGE, type)) {// 返回一个空图片的地址
            if (transformer != null) {
                prefix = transformer.prefixForImage(servletURL, type);
            }
            return prefix + "?op=resource&resource=/com/fr/web/images/s.gif";
        } else if (source instanceof JSONObject) { // 这边的type直接了. 没有判断类型的了.
            if (transformer != null) {
                prefix = transformer.prefixForWrite(servletURL, source, type);
            }
            JSONObject location = (JSONObject) source;
            // carl:标识一下这个widget是所属什么report的，否则parameterpane里的widget就去显示报表里面去拿值了
            return prefix + "?op=widget" + (type == null ? "" : ("&ftype=" + type)) + "&location=" + CodeUtils.encodeURIComponent(location.toString()) + "&sessionID=" + sessionIDInfor.getSessionID();
        } else {
            CheckOut check = CheckOut.getByValue(type);
            if (transformer != null) {
                prefix = transformer.prefixForResource(servletURL, check);
            }
            switch (check) {
                case CHECKOUTFORMLET:
                    return encodeViewlet(prefix, String.valueOf(source));
                case CHECKOUTREPORTLET:
                    return encodeViewlet(prefix, String.valueOf(source));
                case CHECKOUTWIDGET:
                    return prefix + "?op=widget&widgetname=" + source + "&sessionID=" + sessionIDInfor.getSessionID();
                case CHECKOUTIMAGE:
                    return checkOutImageByApiVersion(source, prefix);
            }
            return StringUtils.EMPTY;
        }
    }

    /**
     * tomcat8.5.31之后的版本不支持[], 把超链url中的cjk改成encodeURIComponent
     * 进行两次encode，避免容器例如 tomcat 的配置项 URIEncoding 值的影响
     * <p>
     * tomcat7 默认是ISO-8859-1，8之后默认都是 UTF-8
     *
     * @param prefix
     * @param source
     * @return
     */
    protected String encodeViewlet(String prefix, String source) {
        if (prefix == null || source == null) {
            return StringUtils.EMPTY;
        }
        List<NameValuePair> pairs = URLUtils.parse(ParameterConstants.VIEWLET + "=" + source);
        List<String> keys = new ArrayList<String>();
        List<String> values = new ArrayList<String>();
        for (NameValuePair pair : pairs) {
            String key = pair.getName();
            if (key == null) {
                key = StringUtils.EMPTY;
            } else {
                key = CommonCodeUtils.encodeURIComponent(CommonCodeUtils.encodeURIComponent(key));
            }
            String value = pair.getValue();
            if (value == null) {
                value = StringUtils.EMPTY;
            } else {
                value = CommonCodeUtils.encodeURIComponent(CommonCodeUtils.encodeURIComponent(value));
            }
            keys.add(key);
            values.add(value);
        }

        return HttpToolbox.appendQuery(prefix, keys.toArray(new String[keys.size()]), values.toArray(new String[values.size()]));
    }

    /**
     *  获取图片的资源地址
     * @param source 图片对象
     * @return 服务器图片资源地址
     */
    public String checkoutImage(Object source) {
        String prefix = prefixForResource(CheckOut.CHECKOUTIMAGE);
        ImageResourceProvider imageResource = createImageResource(source);
        if (imageResource == null) {
            return StringUtils.EMPTY;
        }
        JSONObject paraJson = imageResource.createAttachmentJSON();
        paraJson.put("op", "fr_attach");
        paraJson.put("cmd", "ah_image");

        return buildImageResourceUrl(prefix, paraJson);
    }

    /**
     * 获取图片的资源地址,考虑apiversion移动端兼容
     */
    private String checkOutImageByApiVersion(Object source, String prefix) {
        ImageResourceProvider imageResource = createImageResource(source);
        if (imageResource == null) {
            return StringUtils.EMPTY;
        }
        if (checkBase64Required()) {
            return imageResource.createBase64();
        }
        JSONObject paraJson = imageResource.createAttachmentJSON();
        paraJson.put("op", "fr_attach");
        paraJson.put("cmd", "ah_image");

        return buildImageResourceUrl(prefix, paraJson);
    }

    private boolean checkBase64Required() {
        if (this.getDevice().isMobile()) {
            int api_version = WebUtils.getHTTPRequestIntParameter(getHttpServletRequest(), MobileConstants.API_VERSION_JSON);
            return MobileConstants.API_VERSION_JSON_IMAGE_URL_2 > api_version;
        }
        return false;
    }

    private ImageResourceProvider createImageResource(Object source) {
        ImageResourceProvider imageResource = null;
        if (source instanceof Image) {
            imageResource = new ImageResource((Image) source, sessionIDInfor);
        }
        if (source instanceof ImageProvider) {
            imageResource = new FineImageResource((ImageProvider) source);
        }
        return imageResource;
    }

    private String buildImageResourceUrl(String prefix, JSONObject paraJson) {
        StringBuffer buffer = new StringBuffer();
        buffer.append(prefix);
        boolean isFirst = true;
        Iterator iterator = paraJson.keys();
        while (iterator.hasNext()) {
            if (isFirst) {
                buffer.append("?");
                isFirst = false;
            } else {
                buffer.append("&");
            }
            String key = GeneralUtils.objectToString(iterator.next());
            buffer.append(key).append("=").append(paraJson.optString(key));

        }
        return buffer.toString();
    }

    @Override
    public String getServletURL() {
        return this.servletURL;
    }

    @Override
    public HttpServletRequest getHttpServletRequest() {
        return this.req;
    }

    @Override
    public String getHTTPRequestParameter(String parameterName) {
        return WebUtils.getHTTPRequestParameter(req, parameterName);
    }

    /**
     * 获取国际化字符串
     *
     * @param tpl 原始字符串
     * @return 国际化字符串
     */
    @Override
    public String i18n(String tpl) {
        if (tpl == null) {
            return null;
        }

        // denny: 防止Export-Excel-Simple这种带-号的，如果是这种，得用i18n("Export-Excel-Simple")
//		return getI18NCalculator().renderTpl(tpl, Key_RenderAction);
        return TemplateUtils.renderTpl(i18nCalculator, tpl);
    }

    @Override
    public CalculatorProvider getCalculator() {
        return this.i18nCalculator;
    }

    @Override
    public int getResolution() {
        return this.resolution;
    }

    @Override
    public Map<String, Object> getReportParameterMap() {
        return sessionIDInfor.getParameterMap();
    }

    @Override
    public String getSessionID() {
        return this.sessionIDInfor.getSessionID();
    }

    private ServletURLTransformer findServletURLTransformer() {
        ExtraClassManagerProvider pluginProvider = PluginModule.getAgent(PluginModule.ExtraCore);
        ServletURLTransformer transformer = null;
        if (pluginProvider != null) {
            Set<ServletURLTransformer> set = pluginProvider.getArray(ServletURLTransformer.XML_TAG);
            for (ServletURLTransformer transformerItem : set) {
                if (transformerItem.accept(getHttpServletRequest())) {
                    transformer = transformerItem;
                    break;
                }
            }
        }
        return transformer;
    }

    private String prefixForResource(CheckOut checkOut) {
        ServletURLTransformer transformer = findServletURLTransformer();
        if (transformer != null) {
            return transformer.prefixForResource(servletURL, checkOut);
        }
        return servletURL;
    }
}


```


```java
package com.fr.web.core;

import com.fr.web.RepositoryDeal;

import javax.servlet.http.HttpServletRequest;
import java.util.Map;

public class ReportRepositoryDeal extends RepositoryDeal {

	public ReportRepositoryDeal(HttpServletRequest req, ReportSessionIDInfor sessionIDInfor) {
		super(req, sessionIDInfor);
	}

    public ReportRepositoryDeal(HttpServletRequest req, TemplateSessionIDInfo sessionIDInfor, int resolution) {
        super(req, sessionIDInfor, resolution);
    }

    /**
     * 获取report的参数
     */
    public Map<String, Object> getReportParameterMap() {
        // 不知道有什么用，要单独写一个获取报表参数
        return super.getReportParameterMap();
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

## 五、插件注册


```xml
<extra-report>
        <ReportSessionHelpProvider class="your class name"/>
</extra-report>
```

## 六、原理说明

接口通过：Set<ReportSessionHelpProvider> sessionHelpProviders = ExtraReportClassManager.getInstance().getArray(ReportSessionHelpProvider.XML_TAG);获取插件中所有申明的处理接口实例。

在产品中主要通过AbstractActor#dealWithSessionInfo方法，在模板加载并创建session后,实际内容输出前调用接口进行生效。

## 七、特殊限制说明

       接口的入参是Repository（上下文）对象，在报表引擎中被广泛的使用于计算引擎中。用来传递上下文信息，一般最普遍传入的是ReportRepositoryDeal（报表上下文）对象。该对象除了提供报表session的操作之外，还有很多其他对象可以操作。但要注意，因为上下文并非是一个上下文对象贯穿整个报表生命周期，而是需要的时候会重新创建的。而ReportSessionHelpProvider接口的上下文对象仅仅是在报表预览请求发起当时有效，对于后续的计算过程中的上下文时重新创建的，所以如果直接改上下文对象，那么对后续的计算时无法进行干预的。

       ReportSessionHelpProvider接口只对普通报表和聚合报表有效，对决策报表无效。预览方式不支持新填报和H5预览。

       ReportSessionHelpProvider接口本身作用与报表session被创建后的临近逻辑处，在实践中，可以认为接口触发等同于session创建，从而作为一些特殊条件下的监控和访问事件入口。

       该类接口通常会耦合很多产品内部的一些对象方法，这些方法本身不是接口，在升级时可能会变更！开发者需要提前做好预防策略，做好更新前的测试和适配工作。

## 八、常用链接

demo地址：[demo-report-session-help-provider](https://code.fanruan.com/hugh/demo-report-session-help-provider)

## 九、开源案例

免责声明：所有文档中的开源示例，均为开发者自行开发并提供。仅用于参考和学习使用，开发者和官方均无义务对开源案例所涉及的所有成果进行教学和指导。若作为商用一切后果责任由使用者自行承担。

暂无
