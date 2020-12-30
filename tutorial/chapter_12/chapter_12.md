# Beginner Tutorial - simple Web API

## Introduction
In this tutorial, we will introduce two providers: *HttpHandlerProvider* and *URLAliasProvider*. These two are often used together to provide a simple Web API accepting HTTP request.

## HttpHandlerProvider
This is the one to achieve providing a Web API. But it has a problem which will be discussed later. Let's see what it looks like first:
```java
package com.fr.decision.fun;
import com.fr.stable.fun.mark.Mutable;
   
public interface HttpHandlerProvider extends Mutable {
   
    String XML_TAG = "HttpHandlerProvider";
   
    int CURRENT_LEVEL = 1;
   
    HttpHandler[] registerHandlers();
}
```
The structure is simple. It just offers an array of *HttpHandler*:
```java
package com.fr.decision.fun;
   
import com.fr.third.springframework.web.bind.annotation.RequestMethod;
   
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
   
public interface HttpHandler {
   
    /**
     * HTTP Method
     * @return Matches all methods when returning null
     */
    RequestMethod getMethod();
   
    /**
     * Web API path
     * E.g.:
     *     /
     *     /foo
     *     /aaa/bbb
     */
    String getPath();
   
    /**
     * Is this path open to public?
     */
    boolean isPublic();
   
    /**
     * Handle the request
     */
    void handle(HttpServletRequest req, HttpServletResponse res) throws Exception;
}
```
The core method is *handle()*. We define how we handle the requests to our API in this method.

### Example
The example below is to return a JSON string to the requestor.
```java
package com.fr.plugin.http.handler;
  
import com.fr.decision.fun.impl.BaseHttpHandler;
import com.fr.json.JSONObject;
import com.fr.third.springframework.web.bind.annotation.RequestMethod;
import com.fr.web.utils.WebUtils;
  
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
public class TestHandler extends BaseHttpHandler {
    @Override
    public RequestMethod getMethod() {
        return null;
    }
  
    @Override
    public String getPath() {
        return "/test/jsonp";
    }
  
    @Override
    public boolean isPublic() {
        return true;
    }
  
    @Override
    public void handle(HttpServletRequest req, HttpServletResponse res) throws Exception {
        JSONObject result=JSONObject.create().put("success",true);
        WebUtils.printAsJSON(res,result);
    }
}
```
Next, use *HttpHandlerProvider* to register this handler:
```java
package com.fr.plugin.http;
  
import com.fr.decision.fun.HttpHandler;
import com.fr.decision.fun.impl.AbstractHttpHandlerProvider;
import com.fr.plugin.http.handler.DemoHandler;
import com.fr.plugin.http.handler.TestHandler;
 
public class DemoHandlerProvider extends AbstractHttpHandlerProvider {
    @Override
    public HttpHandler[] registerHandlers() {
        return new HttpHandler[]{
                new TestHandler()
        };
    }
}
```
Register it in the plugin.xml:
```xml
<extra-decision>
    <HttpHandlerProvider class="com.fr.plugin.http.DemoHandlerProvider"/>
</extra-decision>
```
To use the Web API defined:

Public Address: http://localhost:8075/webroot/decision/plugin/public/${plugin-id}/test/jsonp

Private Address: http://localhost:8075/webroot/decision/plugin/private/${plugin-id}/test/jsonp

As you may notice, the URL is too long and it depends on the plugin id. Yes, this is the problem we mentioned at the beginning. We will introduce *URLAliasProvider* to solve it.

## URLAliasProvider
It is to offer an alias to the path of our API.
```java
package com.fr.decision.fun;
   
import com.fr.decision.webservice.url.provider.URLAliasRegister;
import com.fr.stable.fun.mark.Mutable;
   
public interface URLAliasProvider extends URLAliasRegister, Mutable {
   
    String XML_TAG = "URLAliasProvider";
   
    int CURRENT_LEVEL = 1;
}
```
```java
package com.fr.decision.webservice.url.provider;
   
import com.fr.decision.webservice.url.alias.URLAlias;
   
public interface URLAliasRegister {
   
    URLAlias[] registerAlias();
}
```
*URLAliasProvider* itself does not have additional functions. The main method *registerAlias()* is from its parent *URLAliasRegister*. This method returns an array of *URLAlias*.
```java
package com.fr.decision.webservice.url.alias;
   
/**
 * url alias
 *
 * Example:
 *     shortPath: "/foo"
 *     targetURL: "/plugin/private/com.fr.plugin.my/hello"
 *     wideRange: false
 *     If we navigate to "/webroot/decision/url/foo", it is the same as visiting "/webroot/decision/plugin/private/com.fr.plugin.my/hello"
 *     If widRange is true，"/foo/baz" can be mapped to "/webroot/decision/plugin/private/com.fr.plugin.my/hello/baz"
 */
public interface URLAlias {
   
    /**
     * url alias
     */
    String getShortPath();
   
    /**
     * target url
     */
    String getTargetURL();
   
    /**
     * Is wide range mode used?
     */
    boolean isWideRange();
}
```

Usually, we use the factory class *URLAliasFactory* to create an instance.
```java
package com.fr.decision.webservice.url.alias;
   
import com.fr.decision.webservice.url.alias.impl.DecisionURLAlias;
import com.fr.decision.webservice.url.alias.impl.PluginURLAlias;
import com.fr.decision.webservice.url.alias.impl.RawURLAlias;
   
public class URLAliasFactory {
   
    public static URLAlias createRawAlias(String aliasPath, String targetURL) {
        return createRawAlias(aliasPath, targetURL, false);
    }
   
    public static URLAlias createRawAlias(String aliasPath, String targetURL, boolean wideRange) {
        RawURLAlias alias = new RawURLAlias();
        alias.setShortPath(aliasPath);
        alias.setTargetURL(targetURL);
        alias.setWideRange(wideRange);
        return alias;
    }
   
    public static URLAlias createDecisionAlias(String aliasPath, String decisionTargetURL) {
        return createDecisionAlias(aliasPath, decisionTargetURL, false);
    }
   
    public static URLAlias createDecisionAlias(String aliasPath, String decisionTargetURL, boolean wideRange) {
        DecisionURLAlias alias = new DecisionURLAlias();
        alias.setShortPath(aliasPath);
        alias.setDecisionTargetURL(decisionTargetURL);
        alias.setWideRange(wideRange);
        return alias;
    }
   
    public static URLAlias createPluginAlias(String aliasPath, String pluginPath) {
        return createPluginAlias(aliasPath, pluginPath, false);
    }
   
    public static URLAlias createPluginAlias(String aliasPath, String pluginPath, boolean isPublic) {
        return createPluginAlias(aliasPath, pluginPath, isPublic, false);
    }
   
    public static URLAlias createPluginAlias(String aliasPath, String pluginPath, boolean isPublic, boolean wideRange) {
        PluginURLAlias alias = new PluginURLAlias();
        alias.setShortPath(aliasPath);
        alias.setPluginPath(pluginPath);
        alias.setPublicURL(isPublic);
        alias.setWideRange(wideRange);
        return alias;
    }
}
```
In our process of developing a plugin, the most commonly used method is *URLAliasFactory.createPluginAlias(String aliasPath, String pluginPath, boolean isPublic)*. Make sure the third parameter is the same as the return value of *HttpHandler.isPublic()*.

### Example
This example is a supplement for the above one:
```java
package com.fr.plugin.http;
  
import com.fr.decision.fun.impl.AbstractURLAliasProvider;
import com.fr.decision.webservice.url.alias.URLAlias;
import com.fr.decision.webservice.url.alias.URLAliasFactory;
  
public class DemoURLAliasProvider extends AbstractURLAliasProvider {
    @Override
    public URLAlias[] registerAlias() {
        return new URLAlias[]{    
                URLAliasFactory.createPluginAlias("/demo","/test/jsonp",true)
        };
    }
}
```
Insert it into the plugin.xml:
```xml
<extra-decision>
    <URLAliasProvider class="com.fr.plugin.http.DemoURLAliasProvider"/>
</extra-decision>
```
Now we can visit the short path: http://localhost:8075/webroot/decision/url/demo.

The source code for this plugin is also available on Github: https://github.com/finereport-overseas/report-starter-10/tree/master/plugin-http-handler-demo