# Designer API (fr-designer)

## I. Special Terms

None

## II. Interface / Method / Object Source

### 1. Get the current designer workspace

```java
DesignerFrame frame = DesignerContext.getDesignerFrame();
```

### 2. Get the template component in the current designer editing area

```java
JTemplate template = DesignerContext.getDesignerFrame().getSelectedJTemplate();
```

### 3. Create an icon button

```java
new UIButton(IOUtils.readIcon("/com/fr/base/images/cell/control/remove.png"));
```

### 4. Get the sheet of the cpt report currently being edited in the designer

```java
((JWorkBook) template).getEditingElementCase();
```

### 5. Refresh the current workspace state

```java
template.fireTargetModified();
```

### 6. Show a designer message dialog

```java
FineJOptionPane.showMessageDialog(parentComponent, messageText);
```

> If you are unsure what to use as the parent component, pass `frame`.

### 7. Get the currently edited template object

```java
BaseBook target = DesignerContext.getDesignerFrame().getSelectedJTemplate().getTarget();
```

### 8. Get the current form designer

```java
FormDesigner designer = WidgetPropertyPane.getInstance().getEditingFormDesigner();
```

### 9. Get the currently selected component in the designer (decision reports and parameter panels only)

```java
XCreator xCreator = designer.getSelectionModel().getSelection().getSelectedCreator();
```

## III. Interface / Method / Object Description

None

## IV. Useful Links

None

## V. Open-Source Examples

None
