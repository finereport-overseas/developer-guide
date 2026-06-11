# Beginner Tutorial - Locale Finder

## Introduction
This provider is used as a servlet filter implemented by a plugin. We don't bother to configure the web.xml. There are two interfaces: *EmbedRequestFilterProvider* and *GlobalRequestFilterProvider*.

## EmbedRequestFilterProvider
When using this, requests towards /decision/* will be intercepted.
```java
package com.fr.decision.fun;
   
import com.fr.stable.fun.mark.Mutable;
   
import javax.servlet.FilterChain;
import javax.servlet.FilterConfig;
import javax.servlet.ServletException;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
   
/**
 * Embedded Servlet Filter.
 */
public interface EmbedRequestFilterProvider extends Mutable {
   
    String MARK_STRING = "EmbedRequestFilterProvider";
   
    int CURRENT_LEVEL = 2;
   
    /**
     * Initialize the filter. Only takes effect after reboot.
     * @param filterConfig the filter config
     */
    void init(FilterConfig filterConfig);
   
    /**
     * filter method
     * @param req HTTP Request
     * @param res HTTP Response
     */
    void filter(HttpServletRequest req, HttpServletResponse res) throws IOException, ServletException;
   
   
    /**
     * Destroy the filter.
     */
    void destroy();
}
```
In order to register this provider, we should write plugin.xml like this:
```xml
<extra-decision>
    <EmbedRequestFilterProvider class="com.fr.plugin.request.filter.DemoEmbedRequestFilterProvider"/>
</extra-decision>
```

## GlobalRequestFilterProvider
This one is like the upgraded *EmbedRequestFilterProvider*. Here is how it looks like:
```java
package com.fr.decision.fun;
   
import com.fr.stable.fun.mark.Mutable;
   
import javax.servlet.FilterChain;
import javax.servlet.FilterConfig;
import javax.servlet.ServletException;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.util.Map;
   
/**
 * A global request filter. Only takes effect after reboot.
 */
public interface GlobalRequestFilterProvider extends Mutable {
   
    String MARK_STRING = "GlobalRequestFilterProvider";
   
    int CURRENT_LEVEL = 1;
   
    /**
     * the filter name
     *
     * @return filter name
     */
    String filterName();
   
   
    /**
     * url patterns
     *
     * @return the array of url patterns
     */
    String[] urlPatterns();
   
    /**
     * the class name of the external filter
     * The corresponding jar needs to be placed under the classpath of the web server. The path can be /WEB-INF/lib or /WEB-INF/classes.
     * @return class name
     */
    String externalFilterClassName();
   
    /**
     * Initialize the filter. Only takes effect after reboot.
     * @param filterConfig the filter config
     */
    void init(FilterConfig filterConfig) throws ServletException;
   
    /**
     * Initial parameters for the filter
     * @return parameter map
     */
    Map<String, String> initializationParameters();
   
    /**
     * filter method
     * @param req HTTP Request
     * @param res HTTP Response
     * @param filterChain
     */
    void doFilter(HttpServletRequest req, HttpServletResponse res, FilterChain filterChain) throws IOException, ServletException;
   
    /**
     * Destroy the filter.
     */
    void destroy();
}
```
There are two ways to realize a filter. One is to include an external filter class by using *externalFilterClassName()*, and the other is to implement the *doFilter()* method. If both are used, only the external one will take effect.

> The filter implemented by *GlobalRequestFilterProvider* is executed before the one implemented by *EmbedRequestFilterProvider*.
If multiple there are multiple filters implemented by *GlobalRequestFilterProvider*, the execution order is random.

## Example
Let's use *GlobalRequestFilterProvider* to build CAS Single Sign-On.

### 1) Implement the CAS Filter
```java
public class CasFilter extends AbstractGlobalRequestFilterProvider  {
    @Override
    public String filterName() {
        return "cas";
    }
  
    @Override
    public String[] urlPatterns() {
        return new String[]{"/*"};
    }
  
    @Override
    public String externalFilterClassName() {
        // The class name may differ in vairous versions of CAS client.
       return "edu.yale.its.tp.cas.client.filter.CASFilter";
    }
  
    @Override
    public void init(FilterConfig filterConfig) {
    }
  
    @Override
    public Map<String, String> initializationParameters() {
        // Initialize some parameters for the CAS filter. It may also change due to different version of CAS client.
        Map<String,String> para=new HashMap<String,String>();
        para.put("edu.yale.its.tp.cas.client.filter.loginUrl","http://localhost:8080/cas/login");
        para.put("edu.yale.its.tp.cas.client.filter.validateUrl","http://localhost:8080/cas/proxyValidate");
        para.put("edu.yale.its.tp.cas.client.filter.serverName","localhost:8080");
        return para;
    }
}
```

### 2) Implement the FR filter
```java
public class FRLoginFilter extends AbstractGlobalRequestFilterProvider {
    @Override
    public String filterName() {
        return "login";
    }
  
    @Override
    public String[] urlPatterns() {
        return new String[]{"/*"};
    }
  
    @Override
    public void doFilter(HttpServletRequest req, HttpServletResponse res, FilterChain filterChain) {
       String casUser=getCasUser(req,res);
        if(isNeedLogin(casUser,req,res)){
            if(!LoginFR(casUser,req,res)){//login the FineReport platform
                // do something if login failed
            }
        }
        try {
            filterChain.doFilter(req,res);
        } catch (IOException e) {
            e.printStackTrace();
        } catch (ServletException e) {
            e.printStackTrace();
        }
    }
  
    private  Boolean isNeedLogin(String casUser, HttpServletRequest req, HttpServletResponse res){
        if(!isLoginedFR(req)){
            return  true;
        }
        String frUser=LoginService.getInstance().getCurrentUserNameFromRequestCookie(req);
  
        return !ComparatorUtils.equals(casUser,frUser);
    }
    /**
     * get CAS user
     * @param req
     * @param res
     * @return
     */
    private String getCasUser(HttpServletRequest req,HttpServletResponse res){
        String username;
        // Get the username from CAS
        Object object = req.getSession().getAttribute("_const_cas_assertion_");
        if (object != null) {
            Assertion assertion = (Assertion) object;
            username = assertion.getPrincipal().getName();
        } else {
            username = (String) req.getSession().getAttribute("edu.yale.its.tp.cas.client.filter.user");
        }
        return  username;
    }
  
    /**
     * Decide whether the user has logged in FineReport Platform.
     * @param req
     * @return
     */
    private Boolean isLoginedFR(HttpServletRequest req){
        String token= TokenResource.COOKIE.getToken(req);
        return StringUtils.isNotBlank(token);
    }
  
    /**
     * Log in FineReport platform
     * @param username
     * @param req
     * @param res
     * @return
     */
    private  Boolean LoginFR(String username,HttpServletRequest req,HttpServletResponse res){
        try{
            User user = UserService.getInstance().getUserByUserName(username);
            if (user == null) {
               return  false;
            }
            String token = LoginService.getInstance().login(req, res, username);
            req.setAttribute(DecisionServiceConstants.FINE_AUTH_TOKEN_NAME, token);
           return  true;
        }catch (Exception e){
        }
        return  false;
    }
}
```
Write the plugin.xml to declare both filters:
```xml
<extra-decision>
    <GlobalRequestFilterProvider class="com.fr.plugin.request.filter.CasFilter"/>
    <GlobalRequestFilterProvider class="com.fr.plugin.request.filter.FRLoginFilter"/>
</extra-decision>
```