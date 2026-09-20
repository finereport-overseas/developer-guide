# plugin.xml 基础配置

> 这一章不会给你带来任何成就感，甚至有些枯燥。但如果你是刚接触帆软插件开发的开发者，请耐心读完。

在开发第一个插件之前，我们先来看一下插件的配置文件 plugin.xml。它其实是插件的"简历"，作用是告诉设计器或服务器"我是谁、我能做什么"，然后等待设计器或服务器给它分配工作。

先看下面这个例子。现在不必关心它是怎么工作的，我们只需要知道它给我们提供了哪些可编辑的内容。

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

我们可以把上面的例子分成两部分。第一部分是"我是谁"。

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

* id：插件的唯一标识，相当于我们的身份证号
* name：插件的名称，相当于我们的名字
* active：yes/no，告诉服务器插件当前是否被激活（激活的插件不一定可用，但可用的插件一定是被激活的）
* version：插件自身的版本号
* env-version：插件适用的平台版本，有两种写法：一种是 `8.0`，表示支持 8.0 及以上版本；另一种是 `9.0~10.0`，表示支持 9.0 到 10.0
* vendor：插件的开发者，支持 email 属性
* jartime：插件支持的平台 JAR 包的最早日期，也就是说要使用这个插件，必须使用在该日期之后发布的平台 JAR 包
* description：插件的简短描述
* change-notes：插件的版本变更说明
* main-package：插件的主包路径（暂且不用深究，只要知道有这个东西即可，后续教程会详细说明）
* function-recorder：插件的功能点记录器（同样可以先忽略，只要知道有这个东西即可）

第二部分讲的是"我能做什么"。

```xml
<extra-decision>
    <GlobalRequestFilterProvider class="com.fr.plugin.myfunction.Demo"/>
</extra-decision>
```

这是对接口的功能声明：
```xml
<GlobalRequestFilterProvider class = "com.fr.plugin.myfunction.Demo" />
```
其基本结构是
```xml
<接口标记字符串 class = "类名" />
```
每个接口的标记字符串会在后续教程中介绍。部分特殊接口还需要提供其他属性和子接口元素，现在同样不必在意，先从通用的开始。

另外，由于平台是由多个模块构成的，我们的接口实现必须声明在对应模块的标签下。XXX 模块的 XML 标签就是 extra-XXX。

> 注意：一个插件中可以声明多个不同模块，同一个模块标签下也可以声明多个不同接口的实现。

## plugin.xml 节点一览

上面的例子只列出了最常用的节点。下面按类别列出 `<plugin>` 下可能出现的第一层子节点。

> 常见程度依据对本仓库 50 个 plugin.xml 文件的统计：**必需** =（几乎）每个文件都出现，**常用** = 使用较广泛，**可选** = 仅在特定场景下使用。

### 元数据节点

| 子节点 | 常见程度 | 说明 |
|---|---|---|
| `id` | 必需 | 插件唯一标识 |
| `name` | 必需 | 插件名称 |
| `version` | 必需 | 插件版本号 |
| `env-version` | 必需 | 插件适用的平台版本，支持两种写法：`8.0` 表示 8.0 及以上，`9.0~10.0` 表示 9.0 到 10.0 |
| `vendor` | 必需 | 插件开发者/供应商，支持 email 属性 |
| `active` | 必需 | yes/no，插件是否默认激活 |
| `jartime` | 必需 | 插件支持的平台 JAR 包最早发布日期 |
| `description` | 常用 | 插件描述 |
| `change-notes` | 常用 | 版本变更说明 |
| `main-package` | 常用 | 插件主包路径 |
| `bi-env-version` | 可选 | 插件适用的 FineBI 版本 |
| `bijartime` | 可选 | 插件支持的 FineBI JAR 包最早发布日期 |
| `hidden` | 可选 | 是否在插件列表中隐藏 |
| `group` | 可选 | 插件所属分组 |
| `auto-update` | 可选 | 是否自动更新 |
| `prefer-package` / `prefer-packages` | 可选 | 优先加载的包 |
| `app-name` | 可选 | 插件所属应用名称 |
| `verif` | 可选 | 验证信息 |
| `designer-pack-version` | 可选 | 设计器包版本 |
| `workspace-independent` | 可选 | 是否与工作空间无关 |

### 功能配置节点

| 子节点 | 常见程度 | 说明 |
|---|---|---|
| `function-recorder` | 常用 | 功能点埋点记录器 |
| `lifecycle-monitor` | 常用 | 插件生命周期监听器 |
| `plugin-xml-i18n` | 可选 | 插件 XML 国际化配置 |
| `conditional-trigger` | 可选 | 条件触发器 |
| `sync` | 可选 | 同步配置 |

### 扩展点声明节点

每个模块对应一个 `extra-XXX` 标签，接口实现必须声明在所属模块的标签下。

| 子节点 | 说明 |
|---|---|
| `extra-core` | 核心模块扩展点 |
| `extra-decision` | 决策平台扩展点 |
| `extra-report` | 报表模块扩展点 |
| `extra-designer` | 设计器模块扩展点 |
| `extra-form` | 表单模块扩展点 |
| `extra-chart` | 图表模块扩展点 |

到这里本章就结束了。实际上还有一些特殊配置，但平时用得不多，我们会在后面遇到时再做介绍。

请仔细阅读，确保自己弄清楚了每个配置的含义。当然，如果你已经了解 plugin.xml 的结构，可以直接跳到下一篇教程。
