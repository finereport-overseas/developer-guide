# ExcelRowCreatorProvider

| 属性 | 值 |
| --- | --- |
| 所属模块 | extra-core (FineBI) |
| 官方文档 | [查看文档](https://wiki.fanruan.com/pages/viewpage.action?pageId=158402889) |

---

## 接口作用

提供可以自定义excel行创建器的接口。

## 接口内容

### 主要接口


```java
/**
 * @version 5.1.3
 * Created by sunmondong on 2021/4/9
 */
@Open
public interface ExcelRowCreatorProvider extends Immutable {

    String XML_TAG = "ExcelRowCreatorProvider";

    int CURRENT_LEVEL = 1;

    /**
     * 获取Excel行生成器
     */
    WorkbookRowCreator getRowCreator(Workbook workbook, String sheetName, ExportContext context);
}


```


```java
package com.finebi.dashboard.api.service.export.provider;


import com.finebi.dashboard.api.service.export.TableRangeAddress;
import com.finebi.dashboard.api.service.export.TableRow;
import com.fr.common.annotations.Open;
import org.jetbrains.annotations.Nullable;

/**
 * 绘图以及绘制excel是不同的创建器
 * 抽象成为分页,逻辑行号以及不同渲染接口对接的接口
 *
 * @author andrew_asa
 * @date 2018/8/25.
 */
@Open
public interface WorkbookRowCreator {

    /**
     * 生成列
     */
    TableRow createRow(int rowNum);

    /**
     * 添加合并单元格
     */
    void addMergedRegion(TableRangeAddress region);

    /**
     * 获取列
     */
    @Nullable
    TableRow getRow(int rowNum);

}


```

## 接口接入

|   |
| --- |

## 提供给插件的抽象类


```java
/**
 * @version 5.1.3
 * Created by sunmondong on 2021/4/9
 */
@API(level = ExcelRowCreatorProvider.CURRENT_LEVEL)
public abstract class AbstractExcelRowCreatorProvider implements ExcelRowCreatorProvider {

    public int currentAPILevel() {
        return CURRENT_LEVEL;
    }

    @Override
    public int layerIndex() {
        return 0;
    }

}


```


```java
public abstract class AbstractExcelRowCreator implements WorkbookRowCreator {

}


```
