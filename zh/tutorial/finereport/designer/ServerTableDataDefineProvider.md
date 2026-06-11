# ServerTableDataDefineProvider

| 属性 | 值 |
| --- | --- |
| 所属模块 | extra-designer |
| 完整类名 | `com.fr.design.fun.ServerTableDataDefineProvider` |
| 官方文档 | [查看文档](https://wiki.fanruan.com/display/PD/ServerTableDataDefineProvider) |

---

## 一、特殊名词介绍

无

## 二、背景、场景介绍

标准的帆软报表产品中只支持基于标准JDBC或JNDI的数据查询和几种基础的文件数据。对于非标JDBC数据库、WebService、REST、实时库、RPC等等的数据接口系统的对接需要依赖数据集接口接入。

以往开发者大多都接触过[程序数据集](https://help.fanruan.com/finereport/doc-view-637.html)的基本实现方法。程序数据集从功能上是可以满足开发者对数据的绝大部分诉求的。不过对于报表制作人员来说，程序数据集存在着非常明显的短板就是使用并不友好。制作人员需要记住大量的类名和业务的关联关系，或者需要手动添加大量的参数实现“配置化”,但是又无法解决参数配置化导致的参数引入问题（比如某些敏感配置只能在制作模板的时候指定，禁止从URL/参数面板等地方引入）。为了给报表制作人员提供更好的使用体验和安全体验，产品内设计了ServerTableDataDefineProvider接口，让开发者得以用插件的形式向用户提供数据集扩展。需要注意的是，该接口不同于TableDataDefineProvider，仅作用于设计器上编辑服务器数据集扩展。

## 三、接口介绍


```java
package com.fr.design.fun;

/**
 * @author : richie
 * @since : 8.0
 * 自定义服务器数据集设计界面接口
 */
public interface ServerTableDataDefineProvider extends TableDataDefineProvider {

    String XML_TAG = "ServerTableDataDefineProvider";
}

```

TableDataDefineProvider

## 四、支持版本

| 产品线 | 版本 | 支持情况 | 备注 |
| --- | --- | --- | --- |
| FR | 8.0 | 支持 |  |
| FR | 9.0 | 支持 |  |
| FR | 10.0 | 支持 |  |
| FR | 11.0 | 支持 |
| BI | 3.6 | 支持 |  |
| BI | 4.0 | 支持 |  |
| BI | 5.1 | 支持 |  |
| BI | 5.1.2 | 支持 |  |
| BI | 5.1.3 | 支持 |  |

## 五、插件注册


```xml
<extra-designer>
        <ServerTableDataDefineProvider class="your class name"/>
</extra-designer>
```

## 六、原理说明

当数据集类型选择列表被触发时，会调用TableDataCreatorProducer#createReportTableDataCreator方法。此时，会读取所有插件中申明的ServerTableDataDefineProvider实例。因为该接口对应的服务其数据集添加是在设计器上操作的，而实际数据集的计算是属于服务器的功能范畴，所以ServerTableDataDefineProvider还有个作用就是解耦，对于一个具体的服务器数据集最终会把tabledata的class名保存到finedb中（注：9.0及更低版本保存到datasources.xml）。

## 七、特殊限制说明

iconPathForTableData ：返回的图标路径（如：/com/demo/hg/resources/images/main.png）。需要注意，因为这个接口没有做图片的统一缩放，所以需要开发者自己准备好16*16(px)大小的图标。否则会显得很不协调。

classForTableData: 接口需要返回一个TableData接口类。实际开发时为了方便开发提高效率和稳定性，要求开发者全部继承AbstractParameterTableData[注意点]

classForInitTableData：这个接口如果已经实现了classForTableData的话，目前没有什么实质性的作用，不过一般要求开发者保留性的与classForTableData返回相同的class。

## 八、常用链接

demo地址：[demo-table-data-define](https://code.fanruan.com/hugh/demo-table-data-define)

TableDataDefineProvider

UniversalServerTableDataProvider

## 九、开源案例

免责声明：所有文档中的开源示例，均为开发者自行开发并提供。仅用于参考和学习使用，开发者和官方均无义务对开源案例所涉及的所有成果进行教学和指导。若作为商用一切后果责任由使用者自行承担。

[demo-tabledata-redis](https://code.fanruan.com/fanruan/demo-tabledata-redis)
