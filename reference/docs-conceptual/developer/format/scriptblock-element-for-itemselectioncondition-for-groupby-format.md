---
description: ScriptBlock Element for ItemSelectionCondition for GroupBy
ms.date: 08/24/2021
title: ScriptBlock Element for ItemSelectionCondition for GroupBy
---
# ScriptBlock Element for ItemSelectionCondition for GroupBy

Specifies the script that triggers the condition. When this script is evaluated to `true`, the
condition is met, and the control is used. This element is used when defining how a new group of
objects is displayed.

## Schema

- Configuration Element
- ViewDefinitions Element
- View Element
- GroupBy Element
- CustomControl Element
- CustomEntries Element
- CustomEntry Element
- CustomItem Element
- ExpressionBinding Element
- ItemSelectionCondition Element
- ScriptBlock Element

## Syntax

```xml
<ScriptBlock>ScriptToEvaluate</ScriptBlock>
```

## Attributes and Elements

The following sections describe attributes, child elements, and the parent element of the
`ScriptBlock` element.

### Attributes

None.

### Child Elements

None.

### Parent Elements

|Element|Description|
|-------------|-----------------|
|[ItemSelectionCondition Element for ExpressionBinding for GroupBy](./itemselectioncondition-element-for-expressionbinding-for-groupby-format.md)|Defines the condition that must exist for this control to be used.|

## Text Value

Specify the script that is evaluated.

## Remarks

If this element is used, you cannot specify the [PropertyName](./propertyname-element-for-itemselectioncondition-for-groupby-format.md)
element when defining the selection condition.

## See Also

[ItemSelectionCondition Element for ExpressionBinding for GroupBy](./itemselectioncondition-element-for-expressionbinding-for-groupby-format.md)

[PropertyName Element for ItemSelectionCondition for GroupBy](./propertyname-element-for-itemselectioncondition-for-groupby-format.md)

[Writing a PowerShell Formatting File](./writing-a-powershell-formatting-file.md)
