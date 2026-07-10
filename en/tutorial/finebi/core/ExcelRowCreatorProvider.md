# ExcelRowCreatorProvider

| Property | Value |
| --- | --- |
| Module | extra-core (FineBI) |
| Official Docs | [View Documentation](https://wiki.fanruan.com/pages/viewpage.action?pageId=158402889) |

---

## Purpose

Provides an interface for customizing the Excel row creator.

## Interface Content

### Main Interface

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
     * Returns the Excel row creator
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
 * Rendering and Excel writing use different creators.
 * Abstracted into pagination, logical row numbers, and an interface
 * for adapting to different rendering backends.
 *
 * @author andrew_asa
 * @date 2018/8/25.
 */
@Open
public interface WorkbookRowCreator {

    /**
     * Creates a row
     */
    TableRow createRow(int rowNum);

    /**
     * Adds a merged cell region
     */
    void addMergedRegion(TableRangeAddress region);

    /**
     * Gets a row
     */
    @Nullable
    TableRow getRow(int rowNum);

}
```

## Registration

|   |
| --- |

## Abstract Classes Provided to Plugins

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
