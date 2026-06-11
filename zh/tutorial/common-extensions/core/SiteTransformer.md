# SiteTransformer

| 属性 | 值 |
| --- | --- |
| 接口类型 | processor |
| 所属模块 | extra-core |
| 完整类名 | `com.fr.stable.fun.SiteTransformer` |
| 官方文档 | [查看文档](https://wiki.fanruan.com/display/PD/com.fr.stable.fun.SiteTransformer) |

## 简介

用于替换 FineReport 显示界面中内置的一些网址链接，一般在 OEM 定制场景下使用。

## 接口定义

```java
/**
 * 网址转换器，用于替换一些内置的网址，一般在 OEM 的时候使用
 */
public interface SiteTransformer extends Mutable {

    String MARK_STRING = "SiteTransformer";

    int CURRENT_LEVEL = 1;

    /**
     * 匹配的键
     * @param key 键
     * @return 如果是需要修改的地址，则返回 true，否则返回 false
     */
    boolean match(String key);

    /**
     * 修改后的新地址
     * @return 新的地址
     */
    String transform();
}
```

## 可替换的键

| 键 | 说明 |
| --- | --- |
| `help.register` | 注册地址 |
| `help` | 在线帮助文档地址 |
| `register.url` | 注册帮助文档地址 |
| `register.email` | 注册帮助邮箱 |
| `bbs` | 论坛地址 |
| `bbs.video` | 视频教学地址 |
| `bbs.questions` | 提问的地址 |

## 使用方式

在插件的 `plugin.xml` 中，通过 `extra-core` 标签注册实现类：

```xml
<extra-core>
    <SiteTransformer class="com.fr.plugin.xxx.YourSiteTransformer"/>
</extra-core>
```

## 示例代码

```java
public class YourSiteTransformer implements SiteTransformer {

    @Override
    public boolean match(String key) {
        // 只替换论坛地址
        return "bbs".equals(key);
    }

    @Override
    public String transform() {
        return "https://your-custom-forum.com";
    }
}
```
