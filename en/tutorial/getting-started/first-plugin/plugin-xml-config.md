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
* description: the short description of the plugin
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
We will introduce what is the mark string of each interface in the following tutorials. Some special interfaces have to provide some other attributes and sub-interface elements. Again, no need to worry about it right now. Let's start with the general first.

Then, since FR is built on various modules, our interface implementation has to be declared under the label of the corresponding module. The XML tag of the XXX module is extra-XXX.

> Note: Different modules can be declared in one plugin, and implementations of different interfaces can also be declared under the same module label.

## Reference: Common Nodes of plugin.xml

The example above only shows the nodes you will use most often. The following tables list the first-level child nodes of `<plugin>` that may appear in a plugin.xml.

> Frequency is derived from a scan of 50 plugin.xml files in this repository: **Required** = present in (almost) every file, **Common** = widely used, **Optional** = only used in specific scenarios.

### Metadata Nodes

| Node | Frequency | Description |
|---|---|---|
| `id` | Required | Unique identifier of the plugin |
| `name` | Required | Display name of the plugin |
| `version` | Required | Version of the plugin itself |
| `env-version` | Required | Platform versions the plugin applies to. Two formats are supported: `8.0` means 8.0 and above, `9.0~10.0` means from 9.0 to 10.0 |
| `vendor` | Required | Developer/vendor of the plugin, the `email` attribute is supported |
| `active` | Required | `yes`/`no`, whether the plugin is activated by default |
| `jartime` | Required | Earliest release date of the platform JAR files supported by the plugin |
| `description` | Common | Short description of the plugin |
| `change-notes` | Common | Changelog of the plugin |
| `main-package` | Common | Main package of the plugin |
| `bi-env-version` | Optional | FineBI versions the plugin applies to |
| `bijartime` | Optional | Earliest release date of the FineBI JAR files supported by the plugin |
| `hidden` | Optional | Whether the plugin is hidden from the plugin list |
| `group` | Optional | Group the plugin belongs to |
| `auto-update` | Optional | Whether the plugin is updated automatically |
| `prefer-package` / `prefer-packages` | Optional | Packages that are loaded with priority |
| `app-name` | Optional | Name of the application the plugin belongs to |
| `verif` | Optional | Verification information |
| `designer-pack-version` | Optional | Version of the designer package |
| `workspace-independent` | Optional | Whether the plugin is independent of the workspace |

### Function Configuration Nodes

| Node | Frequency | Description |
|---|---|---|
| `function-recorder` | Common | Recorder of the plugin's function points |
| `lifecycle-monitor` | Common | Listener of the plugin lifecycle |
| `plugin-xml-i18n` | Optional | Internationalization configuration of plugin.xml |
| `conditional-trigger` | Optional | Conditional trigger |
| `sync` | Optional | Synchronization configuration |

### Extension Point Nodes

Each module owns one `extra-XXX` label, and the interface implementations must be declared under the label of the module they belong to.

| Node | Description |
|---|---|
| `extra-core` | Core module extension points |
| `extra-decision` | Decision platform extension points |
| `extra-report` | Report module extension points |
| `extra-designer` | Designer module extension points |
| `extra-form` | Form module extension points |
| `extra-chart` | Chart module extension points |

At this point, this chapter is over. In fact, there are some special configurations, but generally, they are not used much. Therefore we will introduce them when we meet them in the future.


Please read carefully to ensure that you are clear about every configuration. Of course, if you have already known about the structure of plugin.xml, you can jump to the next tutorial.
