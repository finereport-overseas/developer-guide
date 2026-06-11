# 插件生命周期

## 生命周期节点说明

| 节点 | 说明 |
|---|---|
| **load（加载）** | 读取并生成 `PluginContext` 对象；添加插件间依赖的 ClassPath；初始化插件接口的 Class 对象；读取证书 |
| **run（运行）** | 检测插件依赖的文件；生成 `PluginClassLoader`；检查并启动插件依赖的服务；生成插件接口对象，存入各模块的 `ExtraClassManager` |
| **stop（停止）** | 从 `ExtraClassManager` 中删除对象；清空 `PluginContext` 中的对象；销毁 `PluginClassLoader`；尝试停止服务依赖 |
| **forbid（禁用）** | 执行 stop；修改 `plugin.xml` 的 `active` 属性 |
| **enable（启用）** | 修改 `plugin.xml` 的 `active` 属性；重新读取证书；执行 run |
| **unload** | 对应 load；执行 stop；删除 `PluginContext` 中加载的 Class 对象；删除 `PluginContext` 对象 |
| **install（安装）** | 检测插件依赖的文件/服务/插件间依赖；安装依赖；复制文件到插件 Home |
| **uninstall（删除）** | 对应 install；检测依赖；删除文件 |
| **update（更新）** | 执行 stop；安装新版本插件；移动证书到新版本目录；删除旧插件 |

---

## 监听插件生命周期

### 在插件内监听自身的 start / stop 事件

插件支持热部署后，所有插件都需要在被 stop 后释放占用的资源。通过 `lifecycle-monitor` 标签实现：

```xml
<!-- plugin.xml -->
<lifecycle-monitor class="com.fr.plugin.performance.base.manager.monitor.PerformancePluginMonitor"/>
```

指定的类需继承 `AbstractPluginLifecycleMonitor`：

```java
public class PerformancePluginMonitor extends AbstractPluginLifecycleMonitor {

    @Override
    public void afterRun(PluginContext context) {
        // 插件启动后执行，如初始化缓存、注册监听等
    }

    @Override
    public void beforeStop(PluginContext context) {
        // 插件停止前执行，释放资源
    }
}
```

`AbstractPluginLifecycleMonitor` 还提供了三个额外的生命周期回调：

| 方法 | 触发时机 |
|---|---|
| `afterInstall(PluginContext)` | 安装完成后 |
| `beforeUninstall(PluginContext)` | 卸载前 |
| `afterUpdate(PluginContext)` | 更新完成后 |

> 注意：这三个事件发生在插件运行周期之外，`ExtraClassManager` 中没有当前插件的任何对象，不应依赖插件运行相关的事务。

---

## 细粒度监听（报表侧使用）

报表中需要缓存插件对象的地方，可以用插件事件模型监听特定插件的生命周期变动。

### 1. 定义监听器

```java
PluginEventListener listener = new PluginEventListener(
        PluginListenerPriority.StableFactory,
        PluginListenerScope.ServletContextScope) {

    @Override
    public void on(PluginEvent event) {
        refreshJavaScriptFiles();
    }
};
```

- `priority`：优先级，多个监听时决定执行顺序，级别越高越先执行
- `scope`：作用域，可用于自动移除监听（如 `ServletContextScope` 会在 Servlet 关闭时自动清除）

### 2. 定义过滤器

```java
PluginFilter filter = new PluginFilter() {

    @Override
    public boolean accept(PluginContext context) {
        // 只监听包含 JavaScriptFileHandler 的插件
        return context.contain("JavaScriptFileHandler");
    }
};
```

### 3. 注册监听

```java
// 每当满足条件的插件关闭时，刷新 JS 文件
PluginListenerRegistration.getInstance().listen(
        PluginEventType.AfterStop,
        listener,
        filter);
```

支持的事件类型由 `PluginEventType` 枚举定义，覆盖本文开头表格中的主要生命周期节点。
