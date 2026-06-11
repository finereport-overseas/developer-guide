# AuthorityKit——权限相关工具类

## 源码

[AuthorityKit.java](https://code.fanruan.com/fanruan/finekit/src/branch/master/src/main/java/com/fanruan/api/decision/AuthorityKit.java)

## 说明

AuthorityKit 功能主要用于**获取模块的 ID**。其中的权限设置部分 API 本身不常用，而且目前也无法保障这个接口的相对稳定，所以一般开发时只使用它获取各模块的 ID，尽可能不要调用其他接口控制权限，以免未来升级时不稳定。

### `AuthorityKit.Management.$KEY` 对照表

| Key 值 | 对应决策平台的权限模块 |
| --- | --- |
| `USER` | 用户管理 |
| `AUTHORITY` | 权限管理 |
| `DIRECTORY` | 目录管理 |
| `APPEARANCE` | 外观配置 |
| `CONNECTION` | 数据连接 |
| `SYSTEM` | 系统管理 |
| `SCHEDULE` | 定时调度 |
| `MOBILE` | 移动平台 |
| `REGISTER` | 注册管理 |
| `PLUGIN` | 插件管理 |
| `SECURITY` | 安全管理 |
| `TEMPLATE` | 模板认证 |
| `MAP` | 地图配置 |
| `MAINTENANCE` | 智能运维 |

> 注：相关插件中，其他插件扩展的管理系统的模块依然可以使用这个 Key 值匹配。

## 相关链接

- [finekit 仓库](https://code.fanruan.com/fanruan/finekit)
