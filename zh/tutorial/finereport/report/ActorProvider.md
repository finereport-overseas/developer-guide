# ActorProvider

| 属性 | 值 |
| --- | --- |
| 所属模块 | extra-report |
| 完整类名 | `com.fr.report.fun.ActorProvider` |
| 官方文档 | [查看文档](https://wiki.fanruan.com/display/PD/ActorProvider) |

---

## 一、特殊名词介绍

无

## 二、背景、场景介绍

帆软报表中是通过预览方式的不同，从而使得同一个模板满足不同的展现和交互场景需求。标准产品中常用的预览方式有3种，分页预览，填报预览，数据分析预览。这三种预览方式满足了绝大部分用户的展现和交互诉求。不过有部分用户因为业务和用户习惯的特殊性，需要对现有的预览效果和交互进行诸多细节的调整甚至是需要一种全新的展现方式。那么就需要使用ActorProvider接口进行处理了。比如插件中常见的H5预览方式。

## 三、接口介绍


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

import com.fr.base.BaseFormula;
import com.fr.base.Style;
import com.fr.base.chart.BaseChartPainter;
import com.fr.base.present.Present;
import com.fr.calculate.cell.BoxCEProvider;
import com.fr.general.DeclareRecordType;
import com.fr.json.JSONArray;
import com.fr.json.JSONException;
import com.fr.json.JSONObject;
import com.fr.main.TemplateWorkBook;
import com.fr.main.workbook.ResultWorkBook;
import com.fr.page.PageSetChainProvider;
import com.fr.plugin.injectable.SpecialLevel;
import com.fr.regist.FunctionPoint;
import com.fr.report.cell.CellElement;
import com.fr.report.cell.cellattr.core.attribute.CellElementAttribute;
import com.fr.report.cell.cellattr.core.attribute.OptionalAttribute;
import com.fr.report.core.block.PolyWorkSheetExecutor;
import com.fr.report.core.cal.BoxFactory;
import com.fr.report.core.cal.SE;
import com.fr.report.core.sheet.SheetExecutor;
import com.fr.report.core.sheet.WorkBookExecutor;
import com.fr.report.poly.PolyWorkSheet;
import com.fr.report.poly.ResultChartBlock;
import com.fr.report.poly.ResultECBlock;
import com.fr.report.web.ToolBarManager;
import com.fr.report.worksheet.AbstractResECWorkSheet;
import com.fr.script.Calculator;
import com.fr.stable.fun.Level;
import com.fr.stable.script.ExTool;
import com.fr.stable.web.Repository;
import com.fr.web.cache.ReportCache;
import com.fr.web.cache.ReportEntry;
import com.fr.web.core.ReportSessionIDInfor;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.util.Map;

/**
 * Created with IntelliJ IDEA.
 * User: richie
 * Date: 14-1-24
 * Time: 下午5:26
 * 用于执行不同报表预览方式的操作类
 */
public interface Actor extends Level, OperatorBuilder {

    String XML_TAG = SpecialLevel.Actor.getTagName();
    int CURRENT_LEVEL = 1;

    /**
     * 该操作对应的名称，如填报预览、分页预览、分析预览
     *
     * @return 操作名称
     */
    String description();

    /**
     * 判断该操作方式是否可以按需读取数据库数据，主要用于行式引擎判断
     *
     * @return 可以按需读取数据库则返回true，否则返回false
     */
    boolean canCalculateOnDemand();

    /**
     * 是否能够进行预计算，主要在分页预览的时候需要进行一些预处理
     *
     * @return 会进行预计算则返回true，否则返回false
     */
    boolean willPreCalculate();

    /**
     * 生成该种预览方式所用的盒子工厂
     *
     * @return 盒子工厂
     */
    BoxFactory createBoxFactory();

    /**
     * 生成该种预览方式所使用的结果报表块
     *
     * @return 结果报表块
     */
    ResultECBlock createResultECBlock();

    /**
     * 生成该种预览方式所使用的结果报表页
     *
     * @param se 报表计算引擎
     * @return 结果报表页
     */
    AbstractResECWorkSheet createResultECWorkSheet(SE se);

    /**
     * 将格子缓存到磁盘
     *
     * @param sheet           结果报表页
     * @param maxCount        所活跃的最大格子数
     * @param activePoolCount 缓存池最大容量
     * @param cacheFirst      是否优先缓存
     */
    void cacheCellElement(AbstractResECWorkSheet sheet, int maxCount, int activePoolCount, boolean cacheFirst);

    /**
     * 是否需要建立格子间关系
     *
     * @return 需要简历格间关系返回true，否则返回false
     */
    boolean considerBuildRelation();

    /**
     * 释放报表计算中所使用的资源
     *
     * @param se 报表计算引擎
     */
    void release(SheetExecutor se);

    /**
     * 克隆给定的单元格属性
     *
     * @param em 单元格属性
     * @return 克隆出来的单元格属性
     */
    OptionalAttribute cloneOptionalAttribute(OptionalAttribute em);

    /**
     * 生成超级链接属性
     *
     * @return 单元格的超级链接属性
     */
    CellElementAttribute createHyperCellAttr();

    /**
     * 生成单元格自动调整相关属性
     *
     * @return 单元格UI相关属性
     */
    CellElementAttribute createCellGUIAttr();

    /**
     * 生成单元格控件属性
     *
     * @return 单元格控件属性
     */
    CellElementAttribute createWidgetAttr();

    /**
     * 建立格子之间涉及公式时的联系
     *
     * @param exTool     处理格子间关系的工具
     * @param calculator 算子
     * @param curFa      当前的公式
     * @param oriFa      原始的公式
     * @param boxCe      二维格子对象
     */
    void buildRelation(ExTool exTool, Calculator calculator, BaseFormula curFa, BaseFormula oriFa, BoxCEProvider boxCe);

    /**
     * 是否动态改变脚本， 主要用于分页预览的情况
     *
     * @return 是否需要动态改变脚本
     */
    boolean isDynamicJavaScript();

    /**
     * 判断当前的预览方式是否可以用于定时任务
     *
     * @return 如果可以用于定时任务则返回true，否则返回false
     */
    boolean canBeUseForSchedule();

    /**
     * 设置当前预览方式下格子形态后的值
     * (分页直接去形态后的值, 填报数据分析保留原值)
     *
     * @param ce           当前格子
     * @param present      形态
     * @param presentValue 形态得到的值
     * @date 2014-9-24-上午8:49:38
     */
    void present(CellElement ce, Present present, Object presentValue);

    /**
     * 设置当前预览方式下格子形态后的样式
     *
     * @param ce           当前格子
     * @param present      形态
     * @param presentStyle 样式
     */
    void present(CellElement ce, Present present, Style presentStyle);

    /**
     * 该预览方式是否有可能将控件显示到单元格中
     *
     * @return 如果可能显示控件则返回true，否则返回false
     */
    boolean hasWidget();


    /**
     * 生成用于处理聚合报表聚合块的处理器
     *
     * @param tpl          聚合报表页
     * @param parameterMap 参数集
     * @param actor        预览方式处理器
     * @return 报表块处理器
     */
    PolyWorkSheetExecutor createPolySequenceExecutor(PolyWorkSheet tpl, Map parameterMap, Actor actor);

    /**
     * 注册功能
     *
     * @return 预览时对注册功能的限制
     */
    FunctionPoint getBookFUNC();

    /**
     * 生成一个全新的带有指定参数集的结果报表簿
     *
     * @param parameterMap 参数集
     * @return 结果报表薄
     */
    ResultWorkBook createResultBook(Map parameterMap);

    /**
     * 执行报表簿
     *
     * @param workBook     报表簿
     * @param parameterMap 参数集合
     * @return 结果报表簿
     */
    ResultWorkBook executeWorkBook(TemplateWorkBook workBook, Map parameterMap);

    /**
     * 执行报表簿
     *
     * @param workBook     报表簿
     * @param parameterMap 参数集合
     * @param sheetIndex   当前要计算的sheet索引
     * @return 结果报表簿
     */
    ResultWorkBook executeWorkBook(TemplateWorkBook workBook, Map parameterMap, int sheetIndex);


    /**
     * 生成报表薄跨sheet计算器
     *
     * @param workBook     报表薄
     * @param parameterMap 参数集
     * @return 跨sheet计算器
     */
    WorkBookExecutor createWorkBookExecutor(TemplateWorkBook workBook, Map parameterMap);

    /**
     * 获取该预览方式下的日志记录类型
     *
     * @return 日志记录类型
     */
    DeclareRecordType getRecordType();

    /**
     * 是否需要记录该预览方式下产生的日志
     *
     * @return 如果需要记录日志则返回true，否则返回false
     */
    boolean shouldRecord();

    /**
     * 更新缓存中的时间
     *
     * @param tpl 报表
     * @return 新的时间
     */
    long updateCacheTime(TemplateWorkBook tpl);

    /**
     * 生成模板的缓存对象
     *
     * @param tpl          报表薄
     * @param reportEntry  用于缓存报表的对象
     * @param parameterMap 参数集
     * @param useCache     是否启用了报表缓存
     * @return 模板缓存对象
     */
    ReportCache createReportCache(TemplateWorkBook tpl,
                                  ReportEntry reportEntry,
                                  Map parameterMap,
                                  boolean useCache);

    /**
     * 从缓存中读取结果报表（如果有缓存）
     *
     * @param tpl          报表薄
     * @param tplPath      报表路径
     * @param reportCache  用于缓存报表的对象
     * @param parameterMap 参数集
     * @param sheetIndex   sheet索引
     * @return 结果报表薄
     */
    ResultWorkBook getResultBookFromCacheIfNeed(TemplateWorkBook tpl,
                                                String tplPath,
                                                ReportCache reportCache,
                                                Map<String, Object> parameterMap,
                                                int sheetIndex);

    /**
     * 是否能够缩放页面，主要用于移动端，分页预览时可以缩放，填报时不能缩放
     *
     * @return 可以缩放则返回true，否则返回false
     */
    boolean shouldNotBeScale();

    /**
     * 是否逐页输出报表内容
     *
     * @param req HTTP请求
     * @return 逐页输出则返回true，否则返回false
     */
    boolean isPageByPage(HttpServletRequest req);

    /**
     * 根据结果报表返回分页对象集合
     *
     * @param wb 结果报表
     * @return 分页对象集合
     */
    PageSetChainProvider getPageSet(ResultWorkBook wb);

    /**
     * 获取当前预览方式需要显示的工具栏
     *
     * @param repo 上下文内容
     * @return 工具栏组成的集合
     */
    ToolBarManager[] toolbarManagers(Repository repo);

    /**
     * 生成报表参数界面以及报表主体组成所需要的配置文件
     *
     * @param repository 上下文内容
     * @return 配置文件
     * @throws JSONException 如果产生配置文件失败则抛出此异常
     */
    JSONObject panelConfig(Repository repository) throws JSONException;

    /**
     * 返回用于表示当前预览方式对应的JavaScript中的字符串
     *
     * @return 表示预览方式的字符串
     */
    String panelType();

    /**
     * 各种预览方式所对应的特殊js的路径
     *
     * @return js文件所在的路径
     */
    String mainJavaScriptPath();

    /**
     * 从Session中获取当前访问报表的报表页的数量，主要用于分页预览时的获取
     *
     * @param repository Session相关上下文
     * @return 报表页数量
     */
    int getReportCountInRepo(Repository repository);

    /**
     * 从Session中获取当前访问的报表页的序号
     *
     * @param repository Session相关上下文
     * @return 报表页序号
     */
    int calculateCurrentSheetIndex(Repository repository);

    /**
     * 生成不同预览方式下的图表block
     *
     * @param chartPainter 图表的painter
     */
    ResultChartBlock getChartBlock4Ploy(BaseChartPainter chartPainter);

    /**
     * 是否支持聚合报表
     *
     * @return 支持聚合报表则返回true，否则返回false
     */
    boolean supportPolyExecute();

    /**
     * 生成上下文信息集合
     *
     * @param req            HTTP请求
     * @param sessionIDInfor 报表会话
     * @return 上下文信息集合
     */
    Map<String, Object> createContext4Tpl(HttpServletRequest req, ReportSessionIDInfor sessionIDInfor);

    /**
     * 输出html
     *
     * @param req            HTTP请求
     * @param res            HTTP相应
     * @param map4Tpl        上下文信息集合
     * @param sessionIDInfor 报表会话
     */
    void flushHtml(HttpServletRequest req, HttpServletResponse res, Map<String, Object> map4Tpl, ReportSessionIDInfor sessionIDInfor) throws IOException;


    /**
     * 生成移动端的报表web属性
     *
     * @param req            HTTP请求
     * @param sessionIDInfor 报表会话
     * @return 返回报表web属性
     * @throws JSONException e
     */
    JSONObject createReportWebAttr4Mobile(HttpServletRequest req, ReportSessionIDInfor sessionIDInfor) throws JSONException;

    /**
     * 处理多sheet时生成的sheet配置数组
     *
     * @param repository 报表会话上下文
     * @return sheet配置数组
     */
    JSONArray processMultipleSheet(Repository repository);

    /**
     * 针对不同的预览方式不同的需求，修改session对象属性
     *
     * @param repo    报表会话上下文
     * @param req     请求
     * @param c       算子
     * @param map     已有的参数配置
     * @param isdebug __isdebug__参数
     */
    void dealWithSessionInfo(Repository repo, HttpServletRequest req, Calculator c, Map<String, Object> map, boolean isdebug);

    /**
     * 定时任务结果文件展示类型
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
 * 报表执行器
 */
public interface WorkBookExecutor {

	/**
	 * 生成结果报表
	 * @param currentIndex 序号
	 * @param report 模板
	 * @return 结果报表
	 */
	ResultReport execute(int currentIndex, TemplateReport report);

	/**
	 * 生成结果报表
	 * @return 结果报表
	 */
	ResultWorkBook execute();

	/**
	 * 生成总的结果报表
	 * @return 结果报表
	 */
	ResultWorkBook result();

	/**
	 * 执行类型
	 * @return 预览类型
	 */
	Actor getExeType();


	/**
	 * 添加sheet
	 * @param index 序号
	 * @param resEC 报表
	 */
	void addResult(int index, ResultReport resEC);

	/**
	 * 根据索引获取对应的报表块
	 * @param index 索引
	 * @return 报表块
	 */
	ResultElementCase getResultByIndex(int index);

	/**
	 * 初始化当前预览方式的结果报表
	 *
	 * @param parameterMap 参数map
	 *
	 * @return 当前预览方式的结果报表
	 *
	 */
	ResultWorkBook initResultBook(Map<String, Object> parameterMap);
}


```

## 四、支持版本

| 产品线 | 版本 | 支持情况 | 备注 |
| --- | --- | --- | --- |
| FR | 8.0 | 支持 |  |
| FR | 9.0 | 支持 |  |
| FR | 10.0 | 支持 |  |
| FR | 11.0 | 支持 |

## 五、插件注册


```xml
<extra-report>
        <ActorProvider class="your class name"/>
</extra-report>
```

## 六、原理说明

产品中会通过ActorFactory向调用者提供对应的actor。在ActorFactory加载时，申明了一个插件的监控事件，当有插件注册或停用事件发生时会读取插件中申明的ActorProvider接口并缓存到map对象中。


```java
package com.fr.stable;

import com.fr.general.GeneralContext;
import com.fr.general.web.ParameterConstants;
import com.fr.plugin.context.PluginContext;
import com.fr.plugin.manage.PluginFilter;
import com.fr.plugin.observer.PluginEvent;
import com.fr.plugin.observer.PluginEventListener;
import com.fr.report.ExtraReportClassManager;
import com.fr.report.fun.ActorProvider;
import com.fr.report.stable.fun.Actor;
import com.fr.web.utils.WebUtils;

import javax.servlet.http.HttpServletRequest;
import java.util.HashMap;
import java.util.Map;
import java.util.Set;
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReadWriteLock;
import java.util.concurrent.locks.ReentrantReadWriteLock;

/**
 * Created with IntelliJ IDEA.
 * User: richie
 * Date: 14-2-14
 * Time: 下午3:27
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
    
    private ActorFactory() {

    }

    /**
     * 注册当前type的actor，插件中的actor不要调用这个方法，Factory会自动注册
     *
     * @param type 预览的类型
     * @param actor 预览的Actor
     */
    public synchronized static void registerActor(String type, Actor actor) {
    
        reportMap.put(type, actor);
        merge();
    }

    /**
     * 获取指定类型的Actor
     *
     * @param type 指定的类型
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
     * 从request中获取Actor
     *
     * @param req http请求
     */
    public static Actor getActor(HttpServletRequest req) {
        return getActor(req, null);
    }

    /**
     * 从request中获取Actor
     *
     * @param req http请求
     */
    public static Actor getActor(HttpServletRequest req, Actor defaultActor) {
        String op = WebUtils.getHTTPRequestParameter(req, ParameterConstants.OP);
        //api打印的op也走这边的, op=getSessionID
        if (StringUtils.isEmpty(op) || actorMap.get(op) == null) {
            // 优先返回外部传入的 defaultActor，
            // 否则，若通过平台预览（op=fs_main），ActorFactory.getActor(req) 得到的结果始终是默认的 PageActor。
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

## 七、特殊限制说明

ActorProvider接口本身非常简单，复杂的是Actor对象的实现。

Actor接口几乎包括了所有模板计算的关键方法的入口，也就是实际中展现诉求的差异化处理很大一部分可以通过这些入口切入后进行处理。想要完整的开发一个预览方式，需要对帆软报表本身各种报表对象有非常深的了解才行，所以一般情况下，该接口更多是用来对现有的预览方式做调整。实际开发过程中，往往我们只需要继承PageActor【分页预览】、WriteActor【填报预览】、ViewActor【数据分析预览】这三种预览方式，然后选择对应的切入点接口去做处理即可。

一般而言，比较常用的切入点是 WorkBookExecutor createWorkBookExecutor(TemplateWorkBook workBook, Map parameterMap)； 该接口方法是整个报表计算的入口，不论我们是要调整模板还是对计算的结果进行调整都可以从这里切入。

而预览的类型是通过panelType()接口方法指定的，也就是预览时url上的那个op参数

另外一个配合的接口是WorkBookExecutor，该接口的主要职责就是计算了。该接口的常用切入点是ResultWorkBook execute();该方法是模板计算的实际入口点，计算前预处理和计算后的模板调整都可以在这里处理。

因为两个配合的接口涉及的切入点和方法非常的多，所以ActorProvider接口可以实现的功能就非常的多，开发者在实现具体的场景时，应当根据当前场景的诉求，按照最小入口原则去选择一个恰好满足需要的入口点即可。

## 八、常用链接

demo地址：[demo-actor-provider](https://code.fanruan.com/hugh/demo-actor-provider)

## 九、开源案例

免责声明：所有文档中的开源示例，均为开发者自行开发并提供。仅用于参考和学习使用，开发者和官方均无义务对开源案例所涉及的所有成果进行教学和指导。禁止用于任何商业用途，若作为商用一切后果责任由使用者自行承担。

[demo-dynamic-sheet](https://code.fanruan.com/fanruan/demo-dynamic-sheet)

[open-JSD-8210](https://code.fanruan.com/hugh/open-JSD-8210)
