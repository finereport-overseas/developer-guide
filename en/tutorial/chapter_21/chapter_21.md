# Advanced Tutorial - Export

## Introduction
In this tutorial, we will learn how to extend the functionality of export, including modifying the default export and adding our own ways of export.

First, let's learn the classes used in this tutorial.

### AppExporter
This is the actual place to execute export. Usually, we inherit *AbstractAppExporter* to implement this interface.
```java
public class TestAppExporter extends AbstractAppExporter {
    /**
     * We only need to implement this method, which is the one to execute export process.
     * @param outputStream  
     * @param resultWorkBook Export is essentially turn this resultWorkBook into file.
     * @throws Exception
     */
    @Override
    public void export(OutputStream outputStream, ResultWorkBook resultWorkBook) throws Exception {
 
    }
 
}
```

### ExportCollection
*ExportCollection* is a wrapper for *AppExporter*. It provides a method to call the *export* method from *AppExporter*. Here is the source code for *ExportCollection*.
```java
public class ExportCollection {
  
    private AppExporter exporter;
    private DeclareRecordType recordType;
  
    public static ExportCollection create() {
        return new ExportCollection();
    }
  
    private ExportCollection() {
  
    }
  
    public AppExporter getExporter() {
        return exporter;
    }
  
    public void setExporter(AppExporter exporter) {
        this.exporter = exporter;
    }
  
    public DeclareRecordType getRecordType() {
        return recordType;
    }
  
    public void setRecordType(DeclareRecordType recordType) {
        this.recordType = recordType;
    }
  
    public void doExport(HttpServletRequest req, HttpServletResponse res, ReportSessionIDInfor sessionIDInfor, String format) throws Exception {
  
        if (getRecordType() == null) {
            ErrorHandlerHelper.getErrorHandler().error(req, res, "Cannot recognize the specifed export format:" + format + ",\nThe correct format can be PDF,Excel,Word,SVG,CSV,Text or Image.");
            return;
        }
  
        OutputStream outputStream = res.getOutputStream();
        try {
			// export and log
            LogUtils.exportAndLogRecord(exporter, outputStream, new ReportRepositoryDeal(req, sessionIDInfor), recordType);
        } catch (Exception e) {
            FRContext.getLogger().error(e.getMessage(), e);
            ErrorHandlerHelper.getErrorHandler().error(req, res, e);
        }
        try {
            outputStream.flush();
            outputStream.close();
        } catch (IOException e) {

        }
    }
}
```
This class provides getter and setter for *AppExporter*. One *ExportCollection* is related to one *AppExporter*.

### Operate
This one is used to create an *ExportCollection* instance and set the Content-type of the response. We usually inherit *DefaultOperate* to use it.
```java
public class DemoOperate extends DefaultOperate {
    /**
     * This method has to be implemented. It returns an ExportCollection instance.
     * @param req
     * @param res
     * @param reportSessionIDInfor
     * @param fileName
     * @return
     */
    @Override
    public ExportCollection createCollection(HttpServletRequest req, HttpServletResponse res, ReportSessionIDInfor reportSessionIDInfor, String fileName) {
        return null;
    }
 
    /**
     * set Content-type based on export type
     * @param req
     * @param res
     * @param fileName
     * @param isEmbed
     */
    @Override
    public void setContent(HttpServletRequest req, HttpServletResponse res, String fileName, boolean isEmbed) {
        super.setContent(req, res, fileName, isEmbed);
    }
}
```

## Meet Different Export Requirements
Before we talk about how to meet some export requirements, let's look at the provider for registering an export plugin.
```java
public interface ExportExtensionProcessor extends Immutable {
    String MARK_TAG = "ExportExtensionProcessor";
    int CURRENT_LEVEL = 1;
 
    /**
     * return the filename of the exported file
     * @param req export request
     * @param templateSessionIDInfo session info of the report
     * @return filename
     * @throws Exception
     */
    String fileName(HttpServletRequest req, TemplateSessionIDInfo templateSessionIDInfo) throws Exception;
 
    /**
     * return an ExportCollection instance
     * @param req
     * @param res
     * @param reportSessionIDInfor
     * @param format export type
     * @param filaName
     * @param isEmbed whether it is an embeded type
     * @return
     * @throws Exception
     */
    ExportCollection createCollection(HttpServletRequest req, HttpServletResponse res, ReportSessionIDInfor reportSessionIDInfor, String format, String filaName, boolean isEmbed) throws Exception;
}
```
Register the plugin in plugin.xml.
```xml
<extra-report>
    <ExportExtensionProcessor class="com.fr.plugin.EncodeExportExtension"/>
</extra-report>
```

### Secondary Process of Exported File
One of the many export requirements is that the content and style of the default export file do not need to be changed, but the file itself needs to be processed a second time, such as calling the customer’s own API to encrypt the file.

Combined with the *ExportExtensionProcessor* interface introduced above, we customize an *ExportCollention* for each export method, which encapsulates the custom *AppExporter* export processing. However, there is a problem during implementation. It is that we are not familiar with the *ResultWorkBook* interface. It seems very troublesome to use this interface to export files. When this happens, we don’t have to panic. We can use the default *AppExporter* to export a file on the server, and then directly manipulate the file. Anyway, the entire file needs to be processed twice.
 
Let’s look at an encryption process for the exported pdf file.

First of all, we still need to create an *AppExporter* to encrypt and export pdf.
```java
/**
 * Directly extend the defualt PDF exporter
 */
public class EncodePDFExporter extends PDFExporter {
    /**
     * override export method
     *
     * @param outputStream
     * @param resultWorkBook
     * @throws Exception
     */
    @Override
    public void export(OutputStream outputStream, ResultWorkBook resultWorkBook) throws Exception {
        /**
         * Here we use a custom API to export a temp PDF on the server.
         * Then we retrieve the encoded file object
         */
        DealOtherFileImp imp = new DealOtherFileImp(new PDFExporter(), ".pdf");
        File newFile = imp.encodeFile(resultWorkBook, false, null);
        if (newFile == null) {
            super.export(outputStream, resultWorkBook);
            return;
        }
        // export the new encoded file
        IEUtils.exportFile(outputStream, newFile);
    }
}
```
After implementing *EncodePDFExporter*, the next thing to do is to construct an *ExportCollection* that contains *EncodePDFExporter*. As mentioned in the introduction of Operate, the *ExportCollection* is generally created through the *Operate* interface. The following step is to implement the Operate.
```java
 public class EncodePDFOperate extends DefaultOperate {
    /**
     * create an ExportCollection with an EncodePDFExporter
     * @param req
     * @param res
     * @param reportSessionIDInfor
     * @param fileName
     * @return
     */
    @Override
    public ExportCollection createCollection(HttpServletRequest req, HttpServletResponse res, ReportSessionIDInfor reportSessionIDInfor, String fileName) {
        ExportCollection exportCollection = ExportCollection.create();
        exportCollection.setExporter(new EncodePDFExporter());
        exportCollection.setRecordType(DeclareRecordType.EXPORT_TYPE_PDF);
        this.setContent(req, res, fileName, false);
        return exportCollection;
    }
 
    /**
     * set the content-type and filename
     * @param req
     * @param res
     * @param fileName
     * @param isEmbed
     */
    @Override
    public void setContent(HttpServletRequest req, HttpServletResponse res, String fileName, boolean isEmbed) {
        ExportUtils.setPDFContent(res, fileName, isEmbed);
    }
}
```
The next step is to replace the default logic of exporting pdf with our own custom logic. We use the *ExportExtensionProcessor* interface and rewrite the *createCollection* method.
```java
/**
 * Default export logic is implemented inside DefaultExportExtension, and we only need to modify PDF export part.
 */
public class EncodeExportExtension extends DefaultExportExtension {
    /**
     * Define a map to store the format (e.g. 'pdf') and Operate.
     * Initialization.
     */
    private  static Map<String, Operate> operateMap=new HashMap<String, Operate>();
    static {
        operateMap.put("pdf",new EncodePDFOperate());
 
    }
    @Override
    public ExportCollection createCollection(HttpServletRequest req, HttpServletResponse res, ReportSessionIDInfor reportSessionIDInfor, String format, String fileName, boolean isEmbed) throws Exception {
        Operate operate=operateMap.get(format.toLowerCase());// get Operate from the defined map
        if(operate!=null){
            return  operate.createCollection(req,res,reportSessionIDInfor,fileName);// use Operate#createCollection to return the exportCollection
        }
        return super.createCollection(req, res, reportSessionIDInfor, format, fileName, isEmbed);
    }
}
```

### Change Path to Export File
Here is another export requirement. The customer wants the exported file not downloaded to everyone's client but stored in the server or another file system. The realization is actually similar to the secondary processing of the file. However, this requirement will have an additional point to note, which is the interaction with the front end.

Because there is no more file export, front-end users will not know whether the export is successful. In this case, we need to modify the front-end js that sends the export request.

First look briefly at the implementation of *AppExporter* and *Operate*.
```java
public class DemoPDFExporter extends PDFExporter {
    @Override
    public void export(OutputStream outputStream, ResultWorkBook resultWorkBook) throws Exception {
        FileOutputStream fileOutputStream=null;
        JSONObject result=JSONObject.create();
        try {
            /**
             * export file to the server
            */
            fileOutputStream=new FileOutputStream(new File("D:/ceshi.pdf"));
            (new PDFExporter()).export(fileOutputStream,resultWorkBook);
            fileOutputStream.flush();
            fileOutputStream.close();
            result.put("success",true);
        }catch (Exception e){
            result.put("success",false);
        }
        /**
         * return the result to the front-end
         */
        outputStream.write(result.toString().getBytes("utf-8"));
        outputStream.flush();
        outputStream.close();
    }
}
```
```java
/**
 * Since the client doesn't download the file by browser, content-type is no more needed.
 */
public class DemoPDFOperate extends DefaultOperate {
    @Override
    public ExportCollection createCollection(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, ReportSessionIDInfor reportSessionIDInfor, String s) {
        ExportCollection exportCollection = ExportCollection.create();
        exportCollection.setExporter(new DemoPDFExporter());
        exportCollection.setRecordType(DeclareRecordType.EXPORT_TYPE_PDF);
        return exportCollection;
    }
}
```
The front-end js is shown below:
```js
;(function () {
    $.extend(FR.BasePane.prototype, {
        /**
         * The default implementation of this method is to open url by window.open.
         * Now we use ajax to send a request and display different result through callback
         * @param url
         * @param type
         */
        downloadExportFile: function (url, type) {
            // the new export plugin
            if (FR.Report.Plugin.XHR2FileSaveProcessor.item && FR.Plugin.validLevel(FR.Report.Plugin.XHR2FileSaveProcessor, FR.Report.Plugin.XHR2FileSaveProcessor.item)) {
                FR.Report.Plugin.XHR2FileSaveProcessor.item.action.call(this, url, type);
                return;
            }
            var attr = {title: "Export", contentHtml: "<div class='fr-core-panel-loading'>" + "Exporting..." + "</div>"};
            FR.showLoadingDialog(attr);
            FR.ajax({
                url: url,
                success: function (res) {
                    FR.hideLoadingDialog();
                    var result = FR.jsonDecode(res);
                    if (result.success) {
                        FR.Msg.toast("Export Success");
                    } else {
                        FR.Msg.toast("Export Failure");
                    }
                }
            })
        }
    });
})()
```

### Modify the Content and Style of Export File
The two aforementioned requirements are for the special processing of exported files. In practice, there is another requirement for the processing of exported content. The realization is generally by modifying the report in the *ResultWorkBook* of the report, thereby modifying the content and style of the report when exporting. The first thing that comes to mind is to modify it in the export method in *AppExporter* because this is where the export is finally executed and the object can be obtained directly. This implementation is of course possible, but it will be more troublesome. Each export type must be re-implemented once. At this time, you may notice that the *createCollection* method of the *ExportExtensionProcessor* contains the *ReportSessionIDInfo* object. This can also get the *ResultWorkBook*, even though the method itself is not intended to do anything to the report itself.
```java
public class DemoExportExtension extends DefaultExportExtension {
    /**
     * Get ResultWorkBook from ReportSessionIDInfor, and modify ResultWorkBook.
     * @param httpServletRequest
     * @param httpServletResponse
     * @param reportSessionIDInfor
     * @param s
     * @param s1
     * @param b
     * @return
     * @throws Exception
     */
    @Override
    public ExportCollection createCollection(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, ReportSessionIDInfor reportSessionIDInfor, String s, String s1, boolean b) throws Exception {
        ResultWorkBook reslut =reportSessionIDInfor.getWorkBook2Show();
        dealReport(reslut);
        return super.createCollection(httpServletRequest, httpServletResponse, reportSessionIDInfor, s, s1, b);
    }
    private void dealReport(ResultWorkBook reslut){
         
    }
}
```