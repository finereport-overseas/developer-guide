# EmailServiceProvider

| 属性 | 值 |
| --- | --- |
| 接口类型 | extra-core |
| 完整类名 | `com.fr.stable.fun.EmailServiceProvider` |

## 背景与场景

该接口用于替换产品本身的邮件发送逻辑。与 `EmailSendProvider` 功能相似，区别在于 `EmailServiceProvider` 只能替换产品自身的邮件发送逻辑，适用性更为专一。建议优先使用该接口而非 `EmailSendProvider`。

## 接口定义

```java
package com.fr.stable.fun;

import com.fr.stable.email.EmailAttachmentProvider;
import com.fr.stable.fun.mark.Selectable;

import javax.mail.MessagingException;

/**
 * @author Cloud.Liu
 * @version 10.0
 * Created by Cloud.Liu on 2020/5/20
 */
public interface EmailServiceProvider extends Selectable {

    int CURRENT_LEVEL = 1;

    String XML_TAG = "EmailServiceProvider";

    /**
     * 发送邮件
     *
     * @param toAddress       收件人地址
     * @param ccAddress       抄送地址
     * @param bccAddress      密送地址
     * @param fromAddress     发件人地址
     * @param subject         主题
     * @param bodyContent     正文
     * @param attaches        附件
     * @param format          格式
     * @param contentAttaches 邮件正文显示的附件
     * @throws MessagingException 异常
     */
    void send(String toAddress, String ccAddress, String bccAddress, String fromAddress,
              String subject, String bodyContent, EmailAttachmentProvider[] attaches,
              String format, EmailAttachmentProvider[] contentAttaches) throws MessagingException;
}
```

## 支持版本

| 产品线 | 版本 | 支持情况 |
| --- | --- | --- |
| FR | 10.0 | 支持 |
| FR | 11.0 | 支持 |
| BI | 5.1.3 | 支持 |

## 插件注册

在 `plugin.xml` 中添加以下节点：

```xml
<extra-core>
    <EmailServiceProvider class="your class name"/>
</extra-core>
```

## 原理说明

`EmailManager` 实现了选择逻辑：若无 `EmailServiceProvider` 实现注册，则执行标准邮件发送；若有注册，选择器匹配的提供者负责邮件发送；若无匹配，则记录错误日志。

## 常用链接

- Demo：[demo-email-service-provider](https://code.fanruan.com/hugh/demo-email-service-provider)

## 开源案例

> 免责声明：所有文档中的开源示例均为开发者自行开发并提供，仅供参考和学习使用。开发者和官方均无义务对开源案例进行教学和指导。禁止用于任何商业用途，若作为商用，一切后果责任由使用者自行承担。

- [open-JSD-8153](https://code.fanruan.com/hugh/open-JSD-8153)
