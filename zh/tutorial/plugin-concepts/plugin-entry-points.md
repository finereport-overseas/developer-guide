# 插件接入点

插件包以 zip 文件形式分发，结构如下：

```
xx.name.zip
├── plugin.xml           ← 插件摘要信息
├── xx.name.plugin.jar   ← 插件源码编译产物
├── zz.jar               ← 第三方依赖（非 fine-xxx 开头）
└── yy.jar
```

---

## plugin.xml 节点参考

`plugin.xml` 根节点为 `<plugin>`，支持以下子节点：

| 节点名 | 含义 | 默认值 | 备注 |
|---|---|---|---|
| `id` | 插件唯一标识符 | 无 | 全局唯一，不可重复 |
| `main-package` | 插件 class 的包名 | `com.fr.plugin` | 代码不在默认包下时必须声明 |
| `name` | 插件名称 | 无 | 显示在插件管理器和帆软市场 |
| `active` | 是否激活 | `no` | 不写则默认未激活，无法使用插件功能 |
| `hidden` | 是否在插件管理器中隐藏 | `no` | — |
| `version` | 插件版本号 | 无 | 每次更新需手动修改 |
| `env-version` | 适配的报表版本 | 无 | 一般为 `10.0` |
| `app-version` | 适配的移动端版本 | 无 | 仅移动端插件需要 |
| `jartime` | 适配的 jar 版本（build 日期） | 无 | jar build 日期需大于此值插件才能运行 |
| `vendor` | 插件作者 | 无 | — |
| `description` | 简要描述 | 无 | — |
| `change-notes` | 更新日志 | 无 | 建议倒序写，方便查看最近更新 |
| `extra-core` | 核心模块接口接入点 | 无 | — |
| `extra-decision` | 平台模块接口接入点 | 无 | — |
| `extra-schedule` | 定时调度模块接入点 | 无 | — |
| `extra-mobile` | 移动端模块接入点 | 无 | — |
| `extra-report` | 报表模块接入点 | 无 | — |
| `extra-form` | 决策报表模块接入点 | 无 | — |
| `extra-chart` | 图表模块接入点 | 无 | — |
| `extra-design` | 设计器模块接入点 | 无 | — |
| `extra-chart-design` | 设计器图表模块接入点 | 无 | — |
| `extra-analyse` | BI 模块接入点 | 无 | — |
| `function-recorder` | 插件功能点类接入点 | 无 | 详见[插件功能点记录](./plugin-features.md) |
| `lifecycle-monitor` | 插件生命周期类接入点 | 无 | 详见[插件生命周期](./plugin-lifecycle.md) |
| `prefer-packages` | 插件优先依赖目录 | 无 | 解决插件依赖 jar 与主工程 jar 冲突，子标签为 `prefer-package` |
| `dependence` | 插件依赖信息 | 无 | — |
| `attributes` | 插件自定义属性 | 无 | — |
| `move-after-install` | 安装后执行的文件移动操作 | 无 | — |

---

## dependence：插件间依赖

插件之间可以声明依赖关系。例如插件 A、B 都依赖插件 X：

```xml
<!-- 在 A 或 B 的 plugin.xml 中 -->
<dependence>
    <item type="plugin" key="com.test.x"/>
</dependence>
```

安装 A 或 B 时，系统会提示需要先安装插件 X。从帆软市场安装时会自动先安装依赖插件。

---

## attributes：自定义配置属性

`attributes` 节点可存储插件的可变配置：

```xml
<attributes>
    <encode name="你好，世界"/>
</attributes>
```

在插件代码中通过上下文读取：

```java
PluginXmlElement xml = PluginContexts.currentContext()
        .getXml()
        .getElement(PluginElementName.Attributes);

String name = StringUtils.EMPTY;
if (xml != null) {
    List<PluginXmlElement> children = xml.getChild("encode");
    if (children != null && !children.isEmpty()) {
        name = children.get(0).getAttribute("name");
    }
}
System.out.println(name);  // 输出：你好，世界
```
