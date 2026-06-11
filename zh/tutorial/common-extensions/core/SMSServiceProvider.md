# SMSServiceProvider

| 属性 | 值 |
| --- | --- |
| 接口类型 | extra-core |
| 完整类名 | `com.fr.stable.fun.SMSServiceProvider` |

## 背景与场景

帆软决策平台从 9.0 版本开始引入短信服务功能，但部分用户已有自己的短信服务商，无法直接使用帆软内置的短信服务。为此，10.0 版本开放了 `SMSServiceProvider` 接口，支持接入第三方短信服务。

## 接口定义

```java
package com.fr.stable.fun;

import com.fr.json.JSONArray;
import com.fr.json.JSONObject;
import com.fr.stable.fun.mark.Selectable;
import java.util.List;
import java.util.Map;

public interface SMSServiceProvider extends Selectable {

    int CURRENT_LEVEL = 1;
    String XML_TAG = "SMSServiceProvider";

    /**
     * 将帆软短信模板ID映射到第三方平台模板
     */
    Map<String, String> mapping();

    /**
     * 发送测试短信
     */
    Response sendTest(String mobile);

    /**
     * 使用模板参数替换后发送短信
     */
    Response send(String template, String mobile, JSONObject para, String receiver) throws Exception;

    /**
     * 批量发送短信
     */
    Response batchSendSMS(String template, List<String> mobiles, JSONArray params,
                          List<String> receivers) throws Exception;

    class Response {
        public final static String RES_STATUS_SUCCESS = "success";
        public final static String RES_STATUS_FAILED = "failed";

        private String status;
        private String msg;
        private JSONObject content;

        // getters/setters and factory method omitted
    }

    interface Listener {
        void beforeSend(String text, List<String> mobiles, JSONArray params,
                        List<String> receivers);
        void afterSend(String text, List<String> mobiles, JSONArray params,
                       List<String> receivers, Response response);
    }
}
```

`mapping()` 方法需将帆软内置短信模板 ID 映射到第三方平台对应模板。常用模板 ID 示例：

| 功能 | 模板 ID | 默认模板内容 |
| --- | --- | --- |
| 登录验证码 | 20 | 验证码：#verifiecode# |
| 计划任务失败提醒 | 10 | 任务 #taskname# 于 #time# 执行失败 |
| 测试短信 | 18 | 短信服务配置成功 |
| 平台告警 | 17 | #webname# 内存告警 |

## 支持版本

| 产品线 | 版本 | 支持情况 |
| --- | --- | --- |
| FR | 10.0 | 支持 |
| FR | 11.0 | 支持 |

## 插件注册

在 `plugin.xml` 中添加以下节点：

```xml
<extra-core>
    <SMSServiceProvider class="your class name"/>
</extra-core>
```

## 原理说明

接口通过以下方式获取所有第三方短信扩展：

```java
Set<SMSServiceProvider> providers = PluginModule.ExtraCore.getAgent()
    .getArray(SMSServiceProvider.XML_TAG);
```

SMS Manager 作为主入口，优先检查插件扩展，无扩展时回退到内置服务。

**注意事项**：
1. 需要帆软短信服务连接正常
2. 必须在 `mapping()` 中提供完整的模板 ID 映射
3. before/after 监听器事件仅对第三方服务生效

## 常用链接

- Demo（10.0）：[demo-sms-provider](https://code.fanruan.com/hugh/demo-sms-provider)
- Demo（11.0）：[demo-third-sms-v2](https://code.fanruan.com/pioneer/demo-third-sms-v2)

## 开源案例

> 免责声明：所有文档中的开源示例均为开发者自行开发并提供，仅供参考和学习使用。开发者和官方均无义务对开源案例进行教学和指导。禁止用于任何商业用途，若作为商用，一切后果责任由使用者自行承担。

- [open-JSD-8144](https://code.fanruan.com/hugh/open-JSD-8144)
- [demo-third-sms](https://code.fanruan.com/fanruan/demo-third-sms)
