# Advanced Tutorial - Chart

## Introduction
In order to develop a custom chart plugin, we need to know about the provider and data structure of the chart library we use. Take the 3-D column chart of Echarts as an example, we should be aware of:
1. data structure
    
    | X-Axis Data | Y-Axis Data | Z-Axis Data |
    | ---- | ---- | ----|
    | ... | ... | ... |
    | ... | ... | ... |
2. Echarts API
    
    Instantiate an object: *var instance = echarts.init(dom)*
    
    Set options: *instance.setOptions(options)*

## Example
The example uses the 3-D column chart of Echarts to illustrate how to quickly build a chart plugin for FineReport.

### 1) Define Data Structure
Define the data structure used in the chart as xxxDataConfig, which extends *AbstractDataConfig*.
```java
public class DemoDataConfig extends AbstractDataConfig {
    private ExtendedField x = new ExtendedField();
    private ExtendedField y = new ExtendedField();
    private ExtendedField z = new ExtendedField();
 
    public ExtendedField getX() {
        return x;
    }
 
    public void setX(ExtendedField x) {
        this.x = x;
    }
 
    public ExtendedField getY() {
        return y;
    }
 
    public void setY(ExtendedField y) {
        this.y = y;
    }
 
    public ExtendedField getZ() {
        return z;
    }
 
    public void setZ(ExtendedField z) {
        this.z = z;
    }
 
    @Override
    protected void readAttr(XMLableReader reader) {
        readExtendedField(x, "x", reader);
        readExtendedField(y, "y", reader);
        readExtendedField(z, "z", reader);
    }
 
    @Override
    protected void writeAttr(XMLPrintWriter writer) {
        writeExtendedField(x, "x", writer);
        writeExtendedField(y, "y", writer);
        writeExtendedField(z, "z", writer);
    }
 
    @Override
    public ExtendedField[] dataSetFields() {
        return new ExtendedField[]{
                x,
                y,
                z
        };
    }
 
    @Override
    public DemoDataConfig clone() throws CloneNotSupportedException {
        DemoDataConfig result = new DemoDataConfig();
        result.setX(this.getX().clone());
        result.setY(this.getY().clone());
        result.setZ(this.getZ().clone());
        return result;
    }
 
    @Override
    public int hashCode() {
        return super.hashCode() + AssistUtils.hashCode(this.getX(), this.getY(), this.getZ());
    }
 
    @Override
    public boolean equals(Object obj) {
        return obj instanceof DemoDataConfig
                && AssistUtils.equals(this.getX(), ((DemoDataConfig) obj).getX())
                && AssistUtils.equals(this.getY(), ((DemoDataConfig) obj).getY())
                && AssistUtils.equals(this.getZ(), ((DemoDataConfig) obj).getZ())
                ;
    }
}
```
The x field is used to store the x-axis data and so as the y, z field.

### 2) Define the Front-end Chart Object
The object is defined in demoWrapper.js.
```js
demoWrapper = ExtendedChart.extend({
 
    _init: function (dom, option) {
        var chart = echarts.init(dom);
        chart.setOption(option);
        return chart;
    }
 
});
```

### 3) Define the Back-end Chart Class
The back-end chart object extends *AbstractChart*, and do not forget to provide the generic type *DemoDataConfig*.
```java
@FunctionRecorder
public class DemoChart extends AbstractChart<DemoDataConfig> {
    private static final String ID = "DEMO_CHART";
    private static final String NAME = "Demo Chart";
 
    @Override
    protected String getChartID() {
        return ID;
    }
 
    @Override
    public String getChartName() {
        return NAME;
    }
 
    @Override
    protected String demoImagePath() {
        return "com/fr/plugin/extended/chart/demo/demo.png";
    }
 
    /**
     * Create a JSON Object when previewing. Use this object to pass parameters to the front end.
     * @param dataConfig
     * @param jsonObject
     * @param repo
     * @param para
     * @throws JSONException
     */
    @Override
    @ExecuteFunctionRecord
    protected void addJSON(DemoDataConfig dataConfig, JSONObject jsonObject, Repository repo, JSONPara para) throws JSONException {
 
        JSONArray array = JSONFactory.createJSON(JSON.ARRAY);
 
        List<Object> xValues = dataConfig.getX().getValues();
        List<Object> yValues = dataConfig.getY().getValues();
        List<Object> zValues = dataConfig.getZ().getValues();
 
        double maxValue = Double.MIN_VALUE;
        for (int i = 0, len = xValues.size(); i < len; i++) {
            maxValue = Math.max(GeneralUtils.objectToNumber(zValues.get(i)).doubleValue(), maxValue);
 
            array.put(JSONFactory.createJSON(JSON.ARRAY).put(xValues.get(i)).put(yValues.get(i)).put(zValues.get(i)));
        }
 
        jsonObject.put("series", JSONFactory.createJSON(JSON.OBJECT).put("type", "bar3D").put("data", array)
                .put("bevelSize", 0.2).put("bevelSmoothness", 2).put("shading", "color"));
 
        jsonObject.put("xAxis3D", JSONFactory.createJSON(JSON.OBJECT).put("type", "category"))
                .put("yAxis3D", JSONFactory.createJSON(JSON.OBJECT).put("type", "category"))
                .put("zAxis3D", JSONFactory.createJSON(JSON.OBJECT).put("type", "value"));
 
        jsonObject.put("grid3D", JSONFactory.createJSON(JSON.OBJECT).put("boxWidth", 200).put("boxDepth", 80));
 
        jsonObject.put("visualMap", JSONFactory.createJSON(JSON.OBJECT)
                .put("max", maxValue)
                .put("color", JSONFactory.createJSON(JSON.ARRAY).put("#d94e5d").put("#eac736").put("#50a3ba")));
    }
 
    @Override
    protected String[] requiredJS() {
        return new String[]{
                "com/fr/plugin/chart/demo/web/demoWrapper.js",
                "com/fr/plugin/chart/demo/web/echarts.js",
                "com/fr/plugin/chart/demo/web/echarts-gl.js"
        };
    }
 
    /**
     * the same as the front-end object name
     * @return
     */
    @Override
    protected String wrapperName() {
        return "demoWrapper";
    }
 
    @Override
    protected HyperLinkPara[] hyperLinkParas() {
        return new HyperLinkPara[0];
    }
 
    @Override
    protected List<StringFormula> formulas() {
        return null;
    }
 
}
```
### 4) Create the Data Config UI
There are two data types that can be used in a chart: table data and report data.
```java
public class DemoTableDataPane extends AbstractExtendedChartTableDataPane<DemoDataConfig> {
    private UIComboBox xComboBox;
    private UIComboBox yComboBox;
    private UIComboBox zComboBox;
 
    @Override
    protected String[] fieldLabels() {
        return new String[]{
                "X-Axis",
                "Y-Axis",
                "Z-Axis"
        };
    }
 
    @Override
    protected UIComboBox[] filedComboBoxes() {
        if (xComboBox == null) {
            xComboBox = new UIComboBox();
            yComboBox = new UIComboBox();
            zComboBox = new UIComboBox();
        }
        return new UIComboBox[]{
                xComboBox,
                yComboBox,
                zComboBox
        };
    }
 
    @Override
    protected void populate(DemoDataConfig dataConf) {
        populateField(xComboBox, dataConf.getX());
        populateField(yComboBox, dataConf.getY());
        populateField(zComboBox, dataConf.getZ());
    }
 
    @Override
    protected DemoDataConfig update() {
        DemoDataConfig dataConfig = new DemoDataConfig();
 
        updateField(xComboBox, dataConfig.getX());
        updateField(yComboBox, dataConfig.getY());
        updateField(zComboBox, dataConfig.getZ());
 
        return dataConfig;
    }
}
```
```java
public class DemoReportDataPane extends AbstractExtendedChartReportDataPane<DemoDataConfig> {
    private TinyFormulaPane xPane;
    private TinyFormulaPane yPane;
    private TinyFormulaPane zPane;
 
    @Override
    protected String[] fieldLabel() {
        return new String[]{
                "X-Axis",
                "Y-Axis",
                "Z-Axis"
        };
    }
 
    @Override
    protected TinyFormulaPane[] formulaPanes() {
        if (xPane == null) {
            xPane = new TinyFormulaPane();
            yPane = new TinyFormulaPane();
            zPane = new TinyFormulaPane();
        }
        return new TinyFormulaPane[]{
                xPane,
                yPane,
                zPane
        };
    }
 
    @Override
    protected void populate(DemoDataConfig dataConf) {
        populateField(xPane, dataConf.getX());
        populateField(yPane, dataConf.getY());
        populateField(zPane, dataConf.getZ());
    }
 
    @Override
    protected DemoDataConfig update() {
        DemoDataConfig dataConfig = new DemoDataConfig();
 
        updateField(xPane, dataConfig.getX());
        updateField(yPane, dataConfig.getY());
        updateField(zPane, dataConfig.getZ());
 
        return dataConfig;
    }
}
```

### 5) Register the Chart
```java
public class Demo extends AbstractExtentChartProvider {
    @Override
    protected AbstractChart createChart() {
        return new DemoChart();
    }
}
```

### 6) Register the Chart UI
```java
public class DemoUI extends AbstractExtendedChartUIProvider {
 
    @Override
    protected AbstractExtendedChartTableDataPane getTableDataSourcePane() {
        return new DemoTableDataPane();
    }
 
    @Override
    protected AbstractReportDataContentPane getReportDataSourcePane() {
        return new DemoReportDataPane();
    }
 
    @Override
    public String[] getDemoImagePath() {
        return new String[] {
                "com/fr/plugin/chart/demo/images/demo.png"
        };
    }
 
    @Override
    public String getIconPath() {
        return "com/fr/plugin/chart/demo/images/icon.png";
    }
}
```

### 7) Register the Plugin
Create a plugin.xml to register the implementation.
```xml
<extra-chart>
    <IndependentChartProvider class="com.fr.plugin.chart.demo.Demo" plotID="DEMO_CHART"/>
</extra-chart>
 
<extra-chart-designer>
    <IndependentChartUIProvider class="com.fr.plugin.chart.demo.DemoUI" plotID="DEMO_CHART"/>
</extra-chart-designer>
```

The full code of this example is placed here: https://github.com/finereport-overseas/report-starter-10/tree/master/plugin-chart-demo

<link rel="stylesheet" href="//cdn.bootcss.com/gitalk/1.7.0/gitalk.min.css"></link>
<script src="//cdn.bootcss.com/gitalk/1.7.0/gitalk.min.js"></script>
<div id="gitalk-container"></div>
<script>
    var gitalk = new Gitalk({
        clientID: '08230253bee67abb4384',
        clientSecret: '509e24756efaf3cc4423400c03fa755c1bcf2785',
        repo: 'developer-guide',
        owner: 'finereport-joe',
        admin: ['finereport-joe'],
        id: location.pathname
    })
</script>