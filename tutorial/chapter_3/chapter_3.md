# Introduction of plugin.xml

> This is a chapter that will not give you any sense of accomplishment, and it is also kind of boring. But if you are a developer who has just been exposed to FR plugins, please be patient.

Before developing our first plugin, let's take a look at plugin.xml (the configuration file of a plugin). In fact, it is the resume of a plugin. Its role is to tell the designer or the server "who am I, what can I do", and wait for the designer or the server to assign work.

Let us look at the following example. Now don't worry about how it works, we just want to know what editable contents it provides us.

```xml
<?xml version="1.0" encoding="UTF-8" standalone="no"?><plugin>
    <id>com.fr.plugin.myfunction.id</id>
    <name><![CDATA[ Plugin Title ]]></name>
    <active>yes</active>
    <version>1.0</version>
    <env-version>10.0~10.0</env-version>
    <vendor>Plugin Vendor</vendor>
    <jartime>2018-11-01</jartime>
    <description><![CDATA[ Plugin Description ]]></description>
    <change-notes><![CDATA[ Plugin Change Notes ]]></change-notes>
    <main-package>com.fr.plugin.myfunction</main-package>
    <function-recorder class="com.fr.plugin.myfunction.Demo"/>
    <extra-decision>
        <GlobalRequestFilterProvider class="com.fr.plugin.myfunction.Demo"/>
    </extra-decision>
</plugin>
```

We can divide the above example into two parts. The first part is "who am I".

```xml
<id>com.fr.plugin.function.demo</id>
<name><![CDATA[ Plugin Title ]]></name>
<active>yes</active>
<version>1.0</version>
<env-version>10.0~10.0</env-version>
<vendor>Plugin Vendor</vendor>
<jartime>2018-11-01</jartime>
<description><![CDATA[ Plugin Description ]]></description>
<change-notes><![CDATA[ Plugin Change Notes ]]></change-notes>
<main-package>com.fr.plugin.function.demo</main-package>
<function-recorder class="com.fr.plugin.function.demo.HelloWorld"/>
```

* id: the unique identifier of the plugin, which is equal to our ID Number
* name: the name of the plugin, which is equal to our name
* active: yes/no tells the server whether the plugin is currently activated (activated plugins are not necessarily available, but available plugins must be activated)
* version: the version of the plugin itself
* env-version: the version of FR that the plugin can be applied to. There are two ways of writing. The first one is 8.0, which means it supports version 8.0 and above. The second one is 9.0 ~ 10.0, meaning it supports from 9.0 to 10.0
* vendor: the developer of the plugin, the email attribute supported
* jartime: the earliest date of the FR JAR files supported by the plugin. That is, if you want to use this plugin, you must use it on the FR JAR files released after this date
* description: the short description pf the plugin
* change-notes: the changelog of the plugin
* main-package: the path of the main package of the plugin (P.S. You can ignore this one right now, as long as you know there is such a thing. Detailed instructions will be in the following tutorial)
* function-recorder: the recorder of the plugin's function points (P.S. You can also ignore this. Just know that there is such a thing.)

The second part tells "what can I do".

```xml
<extra-decision>
    <GlobalRequestFilterProvider class="com.fr.plugin.myfunction.Demo"/>
</extra-decision>
```

This is the function declaration of the interface: 
```xml
<GlobalRequestFilterProvider class = "com.fr.plugin.myfunction.Demo" />
```
The basic structure is  
```xml
<Interface-Mark-String class = "class name" />
```
We will introduce what is the mark string of each interface in the following tutorials. Some special interfaces have to provide some other attributes and sub-interface elements. Again, no need to worry about it right now. Let’s start with the general first.

Then, since FR is built on various modules, our interface implementation has to be declared under the label of the corresponding module. The XML tag of the XXX module is extra-XXX.

> Note: Different modules can be declared in one plugin, and implementations of different interfaces can also be declared under the same module label. 

At this point, this chapter is over. In fact, there are some special configurations, but generally, they are not used much. Therefore we will introduce them when we meet them in the future.


Please read carefully to ensure that you are clear about every configuration. Of course, if you have already known about the structure of plugin.xml, you can jump to the next tutorial.

<link rel="stylesheet" href="//cdn.bootcss.com/gitalk/1.7.0/gitalk.min.css"></link>
<script src="//cdn.bootcss.com/gitalk/1.7.0/gitalk.min.js"></script>
<div id="gitalk-container"></div>
<script>
    var gitalk = new Gitalk({
        clientID: '08230253bee67abb4384',
        clientSecret: '509e24756efaf3cc4423400c03fa755c1bcf2785',
        repo: 'developer-guide',
        owner: 'finereport-joe',
        admin: ['finereport-joe'],
        id: location.pathname
    })
</script>