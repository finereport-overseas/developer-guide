# NoticeChannelTypeProvider

| 属性 | 值 |
| --- | --- |
| 接口类型 | provider |
| 所属模块 | extra-fdl |
| 完整类名 | 暂无 |
| 官方文档 | 暂无 |

## 简介

为 FDL（Fine Data Link）提供自定义消息通知渠道类型的扩展接口，支持任务失败通知、脏数据通知及定时任务消息通知等场景。通过实现该接口，可接入邮件、企业微信、钉钉等自定义通知渠道。

## POM 依赖

```xml
<dependencies>
    <dependency>
        <groupId>com.fr.intelligence</groupId>
        <artifactId>fdl-plugin-interface</artifactId>
        <version>4.1-PERESIST-SNAPSHOT</version>
    </dependency>
</dependencies>
```

## 核心类说明

实现一个完整的通知渠道需要继承以下抽象类：

### 1. AbstractNoticeTypeProvider（接入点入口）

注册到 `extra-fdl` 的主入口，声明渠道类型并关联其他组件：

```java
// 通知渠道类型标识（需与前端类型对应）
public abstract String type();

// 是否支持该功能点
public abstract boolean isSupport();

// 功能点校验（不满足条件时抛出异常）
public abstract void checkSupport();

// 关联任务失败/脏数据通知的渠道实现类
public abstract Class<? extends AbstractNoticeChannelProvider> getNoticeChannelProviderClass();

// 关联定时任务消息通知节点的配置类
public abstract Class<? extends AbstractMessageEntityProvider> getMessageEntityProviderClass();
```

### 2. AbstractNoticeChannelProvider（通知渠道配置）

任务失败、脏数据通知渠道的配置类，需支持反序列化（与前端配置对应）：

```java
// 构建消息发送实现类
public abstract AbstractMessageNoticeProvider buildNotice();
```

### 3. AbstractMessageNoticeProvider（消息发送实现）

```java
// 执行消息发送，内容过长时先截取再拼接后缀
public abstract void send(String title, String content, String suffix);
```

### 4. AbstractMessageEntityProvider（定时任务通知节点配置）

定时任务消息通知节点的配置类，需支持反序列化，并实现参数替换和配置校验：

```java
// 深拷贝对象（所有引用对象都需复制）
public abstract MessageEntityProvider clone();

// 参数替换（对支持参数的属性解析后替换）
public abstract void renderParam(Function<String, String> paramReplaceFunction);

// 配置完整性校验，返回校验失败原因集合
public abstract List<String> check();

// 构建定时任务消息发送实现类
public abstract AbstractMessageSenderProvider buildSender();
```

### 5. AbstractMessageSenderProvider（定时任务消息发送）

```java
// 执行定时任务消息发送
public abstract void sendMessage() throws Exception;
```

## 使用方式

在插件的 `plugin.xml` 中，通过 `extra-fdl` 标签注册 `AbstractNoticeTypeProvider` 子类：

```xml
<extra-fdl>
    <NoticeChannelTypeProvider class="com.fr.plugin.dp.notice.EmailNoticeChannel"/>
</extra-fdl>
```

完整 `plugin.xml` 示例：

```xml
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<plugin>
    <id>com.fr.plugin.fdl.generic.jdbc</id>
    <name><![CDATA[邮件通知]]></name>
    <active>yes</active>
    <version>1.0</version>
    <group>fdl</group>
    <env-version>4.2.1</env-version>
    <jartime>2024-11-20</jartime>
    <vendor>fdl</vendor>

    <extra-core>
        <LocaleFinder class="com.fr.plugin.dp.datasource.resource.LocaleFinder"/>
    </extra-core>

    <extra-fdl>
        <NoticeChannelTypeProvider class="com.fr.plugin.dp.notice.EmailNoticeChannel"/>
    </extra-fdl>

    <function-recorder class="com.fr.plugin.dp.datasource.resource.LocaleFinder"/>
</plugin>
```

## 示例代码

```java
public class EmailNoticeChannel extends AbstractNoticeTypeProvider {

    @Override
    public String type() {
        return "EMAIL";  // 与前端渠道类型标识对应
    }

    @Override
    public boolean isSupport() {
        return true;
    }

    @Override
    public void checkSupport() {
        // 校验邮件服务配置是否完整，不满足时抛出异常
    }

    @Override
    public Class<? extends AbstractNoticeChannelProvider> getNoticeChannelProviderClass() {
        return EmailNoticeChannelProvider.class;
    }

    @Override
    public Class<? extends AbstractMessageEntityProvider> getMessageEntityProviderClass() {
        return EmailMessageEntityProvider.class;
    }
}

public class EmailNoticeChannelProvider extends AbstractNoticeChannelProvider {

    private String recipients;  // 收件人（支持反序列化）

    @Override
    public AbstractMessageNoticeProvider buildNotice() {
        return new EmailMessageNoticeProvider(this.recipients);
    }
}

public class EmailMessageNoticeProvider extends AbstractMessageNoticeProvider {

    private final String recipients;

    @Override
    public void send(String title, String content, String suffix) {
        // 内容超长时截取后拼接后缀
        String body = content.length() > 500
            ? content.substring(0, 500) + suffix
            : content + suffix;
        EmailUtils.send(recipients, title, body);
    }
}
```

## 注意事项

- `AbstractNoticeChannelProvider` 和 `AbstractMessageEntityProvider` 均需支持反序列化，其字段应与前端表单配置项对应。
- `AbstractMessageEntityProvider.check()` 应返回所有校验失败原因（支持国际化），返回空集合表示配置合法。
- `renderParam()` 需对所有支持参数占位符的字段调用 `paramReplaceFunction` 进行替换。
- `sendMessage()` 允许抛出异常，FDL 框架会捕获并记录发送失败信息。
