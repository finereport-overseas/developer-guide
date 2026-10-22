# plugin.xml 结构分析

> 扫描范围：当前工程下所有 `plugin.xml`（排除 backup 目录），共 **50** 个文件。
>
> 所有文件的根节点均为 `<plugin>`。

## 第一层子节点汇总

### 1. 元数据节点

| 子节点 | 出现次数 | 说明 |
|---|---:|---|
| `id` | 50 | 插件唯一标识 |
| `name` | 71 | 插件名称 |
| `description` | 71 | 插件描述 |
| `version` | 50 | 插件版本号 |
| `vendor` | 50 | 供应商信息 |
| `env-version` | 50 | 平台环境版本要求 |
| `bi-env-version` | 14 | BI 环境版本要求 |
| `active` | 50 | 是否默认激活 |
| `hidden` | 7 | 是否在插件列表中隐藏 |
| `jartime` | 49 | jar 包时间戳 |
| `bijartime` | 6 | BI jar 包时间戳 |
| `change-notes` | 66 | 版本变更说明 |
| `group` | 13 | 插件分组 |
| `auto-update` | 14 | 是否自动更新 |
| `main-package` | 30 | 主包路径 |
| `prefer-package` | 11 | 优先加载包 |
| `prefer-packages` | 9 | 优先加载包（复数） |
| `app-name` | 3 | 应用名称 |
| `verif` | 3 | 验证信息 |
| `designer-pack-version` | 1 | 设计器包版本 |
| `workspace-independent` | 1 | 是否工作空间无关 |

### 2. 功能配置节点

| 子节点 | 出现次数 | 说明 |
|---|---:|---|
| `function-recorder` | 73 | 功能点埋点记录器 |
| `lifecycle-monitor` | 28 | 插件生命周期监听器 |
| `plugin-xml-i18n` | 21 | 插件 XML 国际化配置 |
| `conditional-trigger` | 2 | 条件触发器 |
| `sync` | 1 | 同步配置 |

### 3. 扩展点声明（extra-*）

| 子节点 | 出现次数 | 说明 |
|---|---:|---|
| `extra-core` | 45 | 核心模块扩展点 |
| `extra-decision` | 42 | 决策平台扩展点 |
| `extra-report` | 12 | 报表模块扩展点 |
| `extra-designer` | 10 | 设计器模块扩展点 |
| `extra-form` | 7 | 表单模块扩展点 |
| `extra-chart` | 1 | 图表模块扩展点 |