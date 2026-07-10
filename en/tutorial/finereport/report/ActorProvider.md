# ActorProvider

| Property | Value |
| --- | --- |
| Module | extra-report |
| Full Class Name | `com.fr.report.fun.ActorProvider` |
| Official Docs | [View Documentation](https://wiki.fanruan.com/display/PD/ActorProvider) |

---

## 1. Terminology

None

## 2. Background and Use Cases

FineReport supports different preview modes so that the same template can serve various display and interaction scenarios. The three common built-in preview modes are: paginated preview, fill-in preview, and data analysis preview. These cover the needs of the vast majority of users. However, some users—due to specific business requirements or unique interaction habits—need to fine-tune the existing preview behavior or introduce an entirely new display mode. In such cases, the `ActorProvider` interface is the entry point. A typical example is the H5 preview mode found in many plugins.

## 3. Interface Overview


```java
package com.fr.report.fun;

import com.fr.plugin.injectable.SpecialLevel;
import com.fr.report.stable.fun.Actor;
import com.fr.stable.fun.Level;

/**
 * @author : richie
 * @since : 8.0
 */
public interface ActorProvider extends Level{

    String XML_TAG = SpecialLevel.ActorProvider.getTagName();
    int CURRENT_LEVEL = 1;

    Actor[] createActor();

}

```


```java
package com.fr.report.stable.fun;

// ... imports omitted for brevity ...

/**
 * Created with IntelliJ IDEA.
 * User: richie
 * Date: 14-1-24
 * Time: 5:26 PM
 * The operation class for executing different report preview modes
 */
public interface Actor extends Level, OperatorBuilder {

    String XML_TAG = SpecialLevel.Actor.getTagName();
    int CURRENT_LEVEL = 1;

    /**
     * The name corresponding to this operation, e.g. fill-in preview, paginated preview, analysis preview
     *
     * @return Operation name
     */
    String description();

    /**
     * Whether this operation mode supports on-demand database reads, mainly used by the row-based engine
     *
     * @return true if on-demand reads are supported, false otherwise
     */
    boolean canCalculateOnDemand();

    /**
     * Whether pre-calculation is supported; mainly needed during paginated preview for pre-processing
     *
     * @return true if pre-calculation will be performed, false otherwise
     */
    boolean willPreCalculate();

    /**
     * Creates the box factory used by this preview mode
     *
     * @return Box factory
     */
    BoxFactory createBoxFactory();

    /**
     * Creates the result report block used by this preview mode
     *
     * @return Result report block
     */
    ResultECBlock createResultECBlock();

    /**
     * Creates the result report sheet used by this preview mode
     *
     * @param se Report calculation engine
     * @return Result report sheet
     */
    AbstractResECWorkSheet createResultECWorkSheet(SE se);

    /**
     * Caches cells to disk
     *
     * @param sheet           Result report sheet
     * @param maxCount        Maximum number of active cells
     * @param activePoolCount Maximum capacity of the cache pool
     * @param cacheFirst      Whether to prioritize caching
     */
    void cacheCellElement(AbstractResECWorkSheet sheet, int maxCount, int activePoolCount, boolean cacheFirst);

    /**
     * Whether inter-cell relationships need to be established
     *
     * @return true if relationships need to be built, false otherwise
     */
    boolean considerBuildRelation();

    /**
     * Releases resources used during report calculation
     *
     * @param se Report calculation engine
     */
    void release(SheetExecutor se);

    /**
     * Clones the given cell attribute
     *
     * @param em Cell attribute
     * @return Cloned cell attribute
     */
    OptionalAttribute cloneOptionalAttribute(OptionalAttribute em);

    /**
     * Creates the hyperlink attribute for a cell
     *
     * @return Cell hyperlink attribute
     */
    CellElementAttribute createHyperCellAttr();

    /**
     * Creates the auto-fit attribute for a cell
     *
     * @return Cell UI-related attribute
     */
    CellElementAttribute createCellGUIAttr();

    /**
     * Creates the widget attribute for a cell
     *
     * @return Cell widget attribute
     */
    CellElementAttribute createWidgetAttr();

    /**
     * Builds formula-based relationships between cells
     *
     * @param exTool     Tool for handling inter-cell relationships
     * @param calculator Calculator
     * @param curFa      Current formula
     * @param oriFa      Original formula
     * @param boxCe      Two-dimensional cell object
     */
    void buildRelation(ExTool exTool, Calculator calculator, BaseFormula curFa, BaseFormula oriFa, BoxCEProvider boxCe);

    /**
     * Whether to dynamically change scripts; mainly applicable in paginated preview
     *
     * @return Whether dynamic script modification is needed
     */
    boolean isDynamicJavaScript();

    /**
     * Whether the current preview mode can be used for scheduled tasks
     *
     * @return true if it can be used for scheduled tasks, false otherwise
     */
    boolean canBeUseForSchedule();

    /**
     * Sets the cell value after applying the present (display format) in the current preview mode
     * (Paginated preview uses the formatted value; fill-in and analysis preview retain the original value)
     *
     * @param ce           Current cell
     * @param present      Display format
     * @param presentValue Value obtained after applying the format
     */
    void present(CellElement ce, Present present, Object presentValue);

    /**
     * Sets the cell style after applying the present in the current preview mode
     *
     * @param ce           Current cell
     * @param present      Display format
     * @param presentStyle Style
     */
    void present(CellElement ce, Present present, Style presentStyle);

    /**
     * Whether this preview mode may display widgets inside cells
     *
     * @return true if widgets may be displayed, false otherwise
     */
    boolean hasWidget();

    /**
     * Creates the executor for handling aggregate report blocks
     *
     * @param tpl          Aggregate report sheet
     * @param parameterMap Parameter map
     * @param actor        Preview mode handler
     * @return Report block executor
     */
    PolyWorkSheetExecutor createPolySequenceExecutor(PolyWorkSheet tpl, Map parameterMap, Actor actor);

    /**
     * Registered function point
     *
     * @return The function point restrictions for this preview mode
     */
    FunctionPoint getBookFUNC();

    /**
     * Creates a brand-new result workbook with the specified parameter map
     *
     * @param parameterMap Parameter map
     * @return Result workbook
     */
    ResultWorkBook createResultBook(Map parameterMap);

    /**
     * Executes a workbook
     *
     * @param workBook     Template workbook
     * @param parameterMap Parameter map
     * @return Result workbook
     */
    ResultWorkBook executeWorkBook(TemplateWorkBook workBook, Map parameterMap);

    /**
     * Executes a workbook for a specific sheet
     *
     * @param workBook     Template workbook
     * @param parameterMap Parameter map
     * @param sheetIndex   Index of the sheet to calculate
     * @return Result workbook
     */
    ResultWorkBook executeWorkBook(TemplateWorkBook workBook, Map parameterMap, int sheetIndex);

    /**
     * Creates a cross-sheet calculator for the workbook
     *
     * @param workBook     Template workbook
     * @param parameterMap Parameter map
     * @return Cross-sheet calculator
     */
    WorkBookExecutor createWorkBookExecutor(TemplateWorkBook workBook, Map parameterMap);

    /**
     * Gets the log record type for this preview mode
     *
     * @return Log record type
     */
    DeclareRecordType getRecordType();

    /**
     * Whether logs generated by this preview mode should be recorded
     *
     * @return true if logging is needed, false otherwise
     */
    boolean shouldRecord();

    /**
     * Updates the cache timestamp
     *
     * @param tpl Template
     * @return New timestamp
     */
    long updateCacheTime(TemplateWorkBook tpl);

    /**
     * Creates the cache object for a template
     *
     * @param tpl          Template workbook
     * @param reportEntry  Object used for caching the report
     * @param parameterMap Parameter map
     * @param useCache     Whether report caching is enabled
     * @return Template cache object
     */
    ReportCache createReportCache(TemplateWorkBook tpl,
                                  ReportEntry reportEntry,
                                  Map parameterMap,
                                  boolean useCache);

    /**
     * Reads the result workbook from cache if available
     *
     * @param tpl          Template workbook
     * @param tplPath      Template path
     * @param reportCache  Object used for caching the report
     * @param parameterMap Parameter map
     * @param sheetIndex   Sheet index
     * @return Result workbook
     */
    ResultWorkBook getResultBookFromCacheIfNeed(TemplateWorkBook tpl,
                                                String tplPath,
                                                ReportCache reportCache,
                                                Map<String, Object> parameterMap,
                                                int sheetIndex);

    /**
     * Whether the page can be scaled; mainly for mobile — paginated preview supports scaling, fill-in does not
     *
     * @return true if scaling is not allowed, false otherwise
     */
    boolean shouldNotBeScale();

    /**
     * Whether to output report content page by page
     *
     * @param req HTTP request
     * @return true if output is page-by-page, false otherwise
     */
    boolean isPageByPage(HttpServletRequest req);

    /**
     * Returns the page set based on the result workbook
     *
     * @param wb Result workbook
     * @return Page set collection
     */
    PageSetChainProvider getPageSet(ResultWorkBook wb);

    /**
     * Gets the toolbars to display for the current preview mode
     *
     * @param repo Context
     * @return Array of toolbar managers
     */
    ToolBarManager[] toolbarManagers(Repository repo);

    /**
     * Generates the configuration object for the report parameter panel and report body
     *
     * @param repository Context
     * @return Configuration object
     * @throws JSONException Thrown if configuration generation fails
     */
    JSONObject panelConfig(Repository repository) throws JSONException;

    /**
     * Returns the string representing the current preview mode in JavaScript
     *
     * @return String representing the preview mode
     */
    String panelType();

    /**
     * The path to the special JS file for each preview mode
     *
     * @return Path to the JS file
     */
    String mainJavaScriptPath();

    /**
     * Gets the number of report pages from the session; mainly used during paginated preview
     *
     * @param repository Session-related context
     * @return Number of report pages
     */
    int getReportCountInRepo(Repository repository);

    /**
     * Gets the index of the current report page from the session
     *
     * @param repository Session-related context
     * @return Report page index
     */
    int calculateCurrentSheetIndex(Repository repository);

    /**
     * Creates the chart block for aggregate reports under different preview modes
     *
     * @param chartPainter Chart painter
     */
    ResultChartBlock getChartBlock4Ploy(BaseChartPainter chartPainter);

    /**
     * Whether aggregate reports are supported
     *
     * @return true if aggregate reports are supported, false otherwise
     */
    boolean supportPolyExecute();

    /**
     * Creates the context information map
     *
     * @param req            HTTP request
     * @param sessionIDInfor Report session
     * @return Context information map
     */
    Map<String, Object> createContext4Tpl(HttpServletRequest req, ReportSessionIDInfor sessionIDInfor);

    /**
     * Outputs HTML
     *
     * @param req            HTTP request
     * @param res            HTTP response
     * @param map4Tpl        Context information map
     * @param sessionIDInfor Report session
     */
    void flushHtml(HttpServletRequest req, HttpServletResponse res, Map<String, Object> map4Tpl, ReportSessionIDInfor sessionIDInfor) throws IOException;

    /**
     * Creates the report web attributes for mobile
     *
     * @param req            HTTP request
     * @param sessionIDInfor Report session
     * @return Report web attributes
     * @throws JSONException e
     */
    JSONObject createReportWebAttr4Mobile(HttpServletRequest req, ReportSessionIDInfor sessionIDInfor) throws JSONException;

    /**
     * Processes the sheet configuration array generated for multi-sheet reports
     *
     * @param repository Report session context
     * @return Sheet configuration array
     */
    JSONArray processMultipleSheet(Repository repository);

    /**
     * Modifies session object properties based on the requirements of different preview modes
     *
     * @param repo    Report session context
     * @param req     Request
     * @param c       Calculator
     * @param map     Existing parameter configuration
     * @param isdebug The __isdebug__ parameter
     */
    void dealWithSessionInfo(Repository repo, HttpServletRequest req, Calculator c, Map<String, Object> map, boolean isdebug);

    /**
     * The display type for scheduled task result files
     */
    int getScheduleShowType();
}

```


```java
package com.fr.report.core.sheet;

import com.fr.main.workbook.ResultWorkBook;
import com.fr.report.elementcase.ResultElementCase;
import com.fr.report.report.ResultReport;
import com.fr.report.report.TemplateReport;
import com.fr.report.stable.fun.Actor;

import java.util.Map;

/**
 * Created by richie on 16/5/11.
 * Report executor
 */
public interface WorkBookExecutor {

	/**
	 * Generates a result report
	 * @param currentIndex Index
	 * @param report Template
	 * @return Result report
	 */
	ResultReport execute(int currentIndex, TemplateReport report);

	/**
	 * Generates a result workbook
	 * @return Result workbook
	 */
	ResultWorkBook execute();

	/**
	 * Generates the overall result workbook
	 * @return Result workbook
	 */
	ResultWorkBook result();

	/**
	 * Execution type
	 * @return Preview type
	 */
	Actor getExeType();

	/**
	 * Adds a sheet
	 * @param index Index
	 * @param resEC Report
	 */
	void addResult(int index, ResultReport resEC);

	/**
	 * Gets the report block at the given index
	 * @param index Index
	 * @return Report block
	 */
	ResultElementCase getResultByIndex(int index);

	/**
	 * Initializes the result workbook for the current preview mode
	 *
	 * @param parameterMap Parameter map
	 *
	 * @return Result workbook for the current preview mode
	 *
	 */
	ResultWorkBook initResultBook(Map<String, Object> parameterMap);
}


```

## 4. Supported Versions

| Product Line | Version | Supported | Notes |
| --- | --- | --- | --- |
| FR | 8.0 | Yes |  |
| FR | 9.0 | Yes |  |
| FR | 10.0 | Yes |  |
| FR | 11.0 | Yes |

## 5. Plugin Registration


```xml
<extra-report>
        <ActorProvider class="your class name"/>
</extra-report>
```

## 6. How It Works

The product uses `ActorFactory` to provide the corresponding actor to callers. When `ActorFactory` is loaded, it declares a plugin monitoring event. Whenever a plugin is registered or deactivated, it reads the `ActorProvider` interface declared in the plugin and caches it in a map object.


```java
package com.fr.stable;

// ... imports omitted for brevity ...

/**
 * Created with IntelliJ IDEA.
 * User: richie
 * Date: 14-2-14
 * Time: 3:27 PM
 */
public class ActorFactory {
    
    private static Map<String, Actor> actorMap = new HashMap<String, Actor>();
    private static Map<Actor, String> typeMap = new HashMap<Actor, String>();
    private static Map<String, Actor> reportMap = new HashMap<String, Actor>();
    private static Map<String, Actor> extraMap = new HashMap<String, Actor>();
    
    private static final ReadWriteLock LOCK = new ReentrantReadWriteLock();
    private static final Lock READ = LOCK.readLock();
    private static final Lock WRITE = LOCK.writeLock();
    
    static {
        GeneralContext.listenPluginRunningChanged(new PluginEventListener() {
            @Override
            public void on(PluginEvent event) {
                refresh();
            }
        }, new PluginFilter() {
            @Override
            public boolean accept(PluginContext context) {
                return context.contain(Actor.XML_TAG) || context.contain(ActorProvider.XML_TAG);
            }
        });
        refresh();
    }
    
    private static void refresh() {
        refreshExtra();
        merge();
    }
    
    private static synchronized void merge() {
        WRITE.lock();
        try {
            actorMap.clear();
            typeMap.clear();
            actorMap.putAll(reportMap);
            actorMap.putAll(extraMap);
            for (Map.Entry<String, Actor> entry : actorMap.entrySet()) {
                typeMap.put(entry.getValue(), entry.getKey());
            }
        } finally {
            WRITE.unlock();
        }
    }
    
    private synchronized static void refreshExtra() {
        extraMap.clear();
        Set<Actor> actors = ExtraReportClassManager.getInstance().getActors();
        for (Actor actor : actors) {
            extraMap.put(actor.panelType(), actor);
        }
        Set<ActorProvider> providers = ExtraReportClassManager.getInstance().getActorProviders();
        Actor[] providedActors;
        for (ActorProvider provider : providers) {
            providedActors = provider.createActor();
            if (providedActors != null) {
                for (Actor actor : providedActors) {
                    extraMap.put(actor.panelType(), actor);
                }
            }
        }
    }
    
    private ActorFactory() {}

    /**
     * Registers the actor for the given type. Plugin actors should not call this method;
     * the Factory registers them automatically.
     *
     * @param type  Preview type
     * @param actor Preview Actor
     */
    public synchronized static void registerActor(String type, Actor actor) {
        reportMap.put(type, actor);
        merge();
    }

    /**
     * Gets the Actor for the specified type
     *
     * @param type Specified type
     */
    public static Actor getActor(String type) {
        Actor actor;
        READ.lock();
        try {
            actor = actorMap.get(type);
            if (actor == null) {
                actor = actorMap.get(ActorConstants.TYPE_PAGE);
            }
        } finally {
            READ.unlock();
        }
        return actor;
    }

    public static String getType(Actor actor) {
        READ.lock();
        try {
            String type = typeMap.get(actor);
            if (type != null) {
                return type;
            }
        } finally {
            READ.unlock();
        }
        return ActorConstants.TYPE_PAGE;
    }

    public static Actor getActor(String type, boolean isMobile, boolean isNoPage) {
        if (ActorConstants.TYPE_VIEW.equalsIgnoreCase(type) && isMobile) {
            type = ActorConstants.TYPE_PAGE;
        }
        if (ActorConstants.TYPE_PAGE.equalsIgnoreCase(type) && isNoPage) {
            type = ActorConstants.TYPE_NO_PAGE;
        }
        return getActor(type);
    }

    /**
     * Gets the Actor from the request
     *
     * @param req HTTP request
     */
    public static Actor getActor(HttpServletRequest req) {
        return getActor(req, null);
    }

    /**
     * Gets the Actor from the request
     *
     * @param req          HTTP request
     * @param defaultActor Default actor returned when op is empty or unrecognized
     */
    public static Actor getActor(HttpServletRequest req, Actor defaultActor) {
        String op = WebUtils.getHTTPRequestParameter(req, ParameterConstants.OP);
        // The op for API printing also goes through here: op=getSessionID
        if (StringUtils.isEmpty(op) || actorMap.get(op) == null) {
            // Prefer returning the externally provided defaultActor;
            // otherwise, when previewing via the platform (op=fs_main),
            // ActorFactory.getActor(req) always returns the default PageActor.
            if (defaultActor != null) {
                return defaultActor;
            }
            op = ActorConstants.TYPE_PAGE;
        }
        Actor actor;
        READ.lock();
        try {
            actor = actorMap.get(op);
            if (actor == null) {
                throw new RuntimeException("Not support op:" + op + "@" + ActorFactory.class.getName());
            }
        } finally {
            READ.unlock();
        }
        return actor;
    }
}
```

## 7. Constraints and Notes

The `ActorProvider` interface itself is straightforward; the complexity lies in the implementation of the `Actor` object.

The `Actor` interface covers entry points for almost all key steps in template calculation, meaning that most display and interaction differences can be handled through these entry points. Developing a complete preview mode from scratch requires deep knowledge of FineReport's internal report objects. In practice, this interface is more commonly used to adjust existing preview modes. Most implementations simply extend `PageActor` (paginated preview), `WriteActor` (fill-in preview), or `ViewActor` (data analysis preview), and then override only the relevant entry point.

The most commonly used entry point is `WorkBookExecutor createWorkBookExecutor(TemplateWorkBook workBook, Map parameterMap)`. This is the entry point for the entire report calculation process; both pre-calculation template adjustments and post-calculation result adjustments can be made here.

The preview type is specified via the `panelType()` method, which corresponds to the `op` parameter in the preview URL.

The companion interface `WorkBookExecutor` is responsible for the actual calculation. Its most commonly used entry point is `ResultWorkBook execute()`, which is the actual calculation entry point — both pre-processing before calculation and template adjustments after calculation can be handled there.

Because the two companion interfaces expose many entry points and methods, `ActorProvider` can achieve a wide range of effects. When implementing a specific scenario, follow the principle of minimal footprint and select the entry point that exactly meets your requirements.

## 8. Useful Links

Demo: [demo-actor-provider](https://code.fanruan.com/hugh/demo-actor-provider)

## 9. Open-Source Examples

Disclaimer: All open-source examples in the documentation are developed and contributed by community developers, for reference and learning purposes only. Neither the developers nor FineReport are obligated to provide instruction or support for any results from these examples. Commercial use is prohibited; any consequences of commercial use are solely the responsibility of the user.

[demo-dynamic-sheet](https://code.fanruan.com/fanruan/demo-dynamic-sheet)

[open-JSD-8210](https://code.fanruan.com/hugh/open-JSD-8210)
