# NoticeChannelTypeProvider

| Property | Value |
| --- | --- |
| Interface Type | provider |
| Module | extra-fdl |
| Full Class Name | N/A |
| Official Docs | N/A |

## Overview

An extension interface for providing custom notification channel types in FDL (Fine Data Link). Supports scenarios such as task failure notifications, dirty data notifications, and scheduled task message notifications. By implementing this interface, you can integrate custom channels such as email, WeCom, and DingTalk.

## POM Dependency

```xml
<dependencies>
    <dependency>
        <groupId>com.fr.intelligence</groupId>
        <artifactId>fdl-plugin-interface</artifactId>
        <version>4.1-PERESIST-SNAPSHOT</version>
    </dependency>
</dependencies>
```

## Core Class Overview

Implementing a complete notification channel requires extending the following abstract classes:

### 1. AbstractNoticeTypeProvider (Entry Point)

The main entry point registered to `extra-fdl`. Declares the channel type and associates it with other components:

```java
// Notification channel type identifier (must match the frontend type)
public abstract String type();

// Whether this feature is supported
public abstract boolean isSupport();

// Feature validation (throws an exception if conditions are not met)
public abstract void checkSupport();

// Associates the channel implementation class for task failure / dirty data notifications
public abstract Class<? extends AbstractNoticeChannelProvider> getNoticeChannelProviderClass();

// Associates the configuration class for scheduled task message notification nodes
public abstract Class<? extends AbstractMessageEntityProvider> getMessageEntityProviderClass();
```

### 2. AbstractNoticeChannelProvider (Notification Channel Configuration)

The configuration class for task failure and dirty data notification channels. Must support deserialization (corresponding to frontend configuration):

```java
// Builds the message sending implementation class
public abstract AbstractMessageNoticeProvider buildNotice();
```

### 3. AbstractMessageNoticeProvider (Message Sending Implementation)

```java
// Sends the message; truncate and append a suffix when the content is too long
public abstract void send(String title, String content, String suffix);
```

### 4. AbstractMessageEntityProvider (Scheduled Task Notification Node Configuration)

The configuration class for scheduled task message notification nodes. Must support deserialization, and implement parameter substitution and configuration validation:

```java
// Deep copy of the object (all referenced objects must be copied)
public abstract MessageEntityProvider clone();

// Parameter substitution (parses and replaces values in fields that support placeholders)
public abstract void renderParam(Function<String, String> paramReplaceFunction);

// Configuration completeness validation; returns a collection of validation failure reasons
public abstract List<String> check();

// Builds the scheduled task message sender implementation class
public abstract AbstractMessageSenderProvider buildSender();
```

### 5. AbstractMessageSenderProvider (Scheduled Task Message Sending)

```java
// Executes the scheduled task message send
public abstract void sendMessage() throws Exception;
```

## Usage

Register the `AbstractNoticeTypeProvider` subclass in the plugin's `plugin.xml` via the `extra-fdl` tag:

```xml
<extra-fdl>
    <NoticeChannelTypeProvider class="com.fr.plugin.dp.notice.EmailNoticeChannel"/>
</extra-fdl>
```

Complete `plugin.xml` example:

```xml
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<plugin>
    <id>com.fr.plugin.fdl.generic.jdbc</id>
    <name><![CDATA[Email Notification]]></name>
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

## Example Code

```java
public class EmailNoticeChannel extends AbstractNoticeTypeProvider {

    @Override
    public String type() {
        return "EMAIL";  // Must match the frontend channel type identifier
    }

    @Override
    public boolean isSupport() {
        return true;
    }

    @Override
    public void checkSupport() {
        // Validate that the email service configuration is complete; throw an exception if not
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

    private String recipients;  // Recipients (supports deserialization)

    @Override
    public AbstractMessageNoticeProvider buildNotice() {
        return new EmailMessageNoticeProvider(this.recipients);
    }
}

public class EmailMessageNoticeProvider extends AbstractMessageNoticeProvider {

    private final String recipients;

    @Override
    public void send(String title, String content, String suffix) {
        // Truncate content if too long, then append the suffix
        String body = content.length() > 500
            ? content.substring(0, 500) + suffix
            : content + suffix;
        EmailUtils.send(recipients, title, body);
    }
}
```

## Notes

- Both `AbstractNoticeChannelProvider` and `AbstractMessageEntityProvider` must support deserialization; their fields should correspond to the frontend form configuration items.
- `AbstractMessageEntityProvider.check()` should return all validation failure reasons (internationalization is supported); return an empty collection to indicate the configuration is valid.
- `renderParam()` must call `paramReplaceFunction` for every field that supports parameter placeholders.
- `sendMessage()` is allowed to throw exceptions; the FDL framework will catch and log the send failure information.
