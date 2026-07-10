# PDFExporterCreator

| Property | Value |
| --- | --- |
| Interface Type | extra-core |
| Full Class Name | `com.fr.io.exporter.PDFExporterCreator` |

## Background and Use Cases

FanRuan Report provides fine-grained PDF export scenarios, including common cases such as normal export, print preview, and auto-print. Use the `PDFExporterCreator` interface when you need to define a custom PDF export type or adjust an existing export type. This interface is intended for targeted adjustments to specific export types, not for uniformly handling all PDF exports.

## Interface Definition

```java
package com.fr.io.exporter;

import com.fr.stable.fun.mark.Immutable;

public interface PDFExporterCreator extends Immutable {
    String MARK_STRING = "PDFExporterCreator";
    int CURRENT_LEVEL = 1;

    @Deprecated
    PDFExporterProcessor createPDFExporter(boolean isPrint);

    /**
     * PDF export
     * @param exportType export type: normal, print preview, auto-print
     */
    PDFExporterProcessor createPDFExporter(PDFExportType exportType);
}
```

## Supported Versions

| Product | Version | Support | Notes |
| --- | --- | --- | --- |
| FR | 8.0 | Supported | — |
| FR | 9.0 | Supported | — |
| FR | 10.0 | Supported | — |
| FR | 11.0 | Supported | |
| BI | 3.6 | Supported | Dashboard not supported |
| BI | 4.0 | Supported | Dashboard not supported |
| BI | 5.1 | Supported | Dashboard not supported |
| BI | 5.1.2 | Supported | Dashboard not supported |
| BI | 5.1.3 | Supported | Dashboard not supported |

## Plugin Registration

Add the following node in `plugin.xml`:

```xml
<extra-core>
    <PDFExporterCreator class="your class name"/>
</extra-core>
```

## How It Works

All PDF exports are created through `PDFExporterFactory`. When `PDFExporterFactory` loads, it reads the `PDFExporterCreator` instances declared by plugins. When `PDFExporterFactory#getPDFExporter` is invoked, it calls the loaded `PDFExporterCreator` instance to generate the specific export instance.

This interface is `Immutable` (exclusive) — only one plugin implementation takes effect in the environment. Ensure there are no conflicting plugins in your environment. `PDFExporterProcessor` is merely an abstract wrapper over `AppExporter`; it is recommended to extend `PDFExporter`, `PDFExporterForPrint`, or `PDFEmbExporter` directly.

## Useful Links

- Demo: [demo-pdf-exporter-creator](https://code.fanruan.com/hugh/demo-pdf-exporter-creator)
