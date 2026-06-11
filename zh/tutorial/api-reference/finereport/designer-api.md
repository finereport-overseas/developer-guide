# 设计器 API (fr-designer)

## 一、特殊名词介绍

无

## 二、接口/方法/对象源码

### 1. 获取当前设计器的工作区

```java
DesignerFrame frame = DesignerContext.getDesignerFrame();
```

### 2. 获取当前设计器的编辑区模板组件

```java
JTemplate template = DesignerContext.getDesignerFrame().getSelectedJTemplate();
```

### 3. 创建一个图标按钮

```java
new UIButton(IOUtils.readIcon("/com/fr/base/images/cell/control/remove.png"));
```

### 4. 获取当前设计器编辑的 cpt 报表的 sheet 页

```java
((JWorkBook) template).getEditingElementCase();
```

### 5. 刷新当前的工作区状态

```java
template.fireTargetModified();
```

### 6. 设计器消息弹窗

```java
FineJOptionPane.showMessageDialog(弹窗归属的主体, 消息内容文本);
```

> 主体如果不知道是啥就设置成 `frame`。

### 7. 获取当前编辑的模板对象

```java
BaseBook target = DesignerContext.getDesignerFrame().getSelectedJTemplate().getTarget();
```

### 8. 获取当前表单设计器

```java
FormDesigner designer = WidgetPropertyPane.getInstance().getEditingFormDesigner();
```

### 9. 获取当前设计器被选中的组件（仅限决策报表或参数界面）

```java
XCreator xCreator = designer.getSelectionModel().getSelection().getSelectedCreator();
```

## 三、接口/方法/对象说明

无

## 四、常用链接

无

## 五、开源案例

无
