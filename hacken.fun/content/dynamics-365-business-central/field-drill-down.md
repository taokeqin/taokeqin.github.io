Title: OnDrillDown 触发器的处理
Date: 2023-03-03 23:00
Modified: 2023-03-03 23:00
Category: dynamics-365-business-central
Tags: business-central, ondrilldown
Slug: field-drill-down
Authors: Hacken
Summary: 如何正确的处理 OnDrillDown 触发器，让所有的东西可以被点击

简单来说 OnDrillDown 事件是用来处理 page field 的点击的，
我们可以在定义 Page 的时候通过 OnDrillDown trigger,如下

```AL
trigger OnDrillDown()
begin
    ...
end;
```

值得注意的是 OnDrillDown 在用全局变量渲染的 field 上面是不能工作的。
能工作的只有来自 SourceTable 的 filed

什么意思呢？

也就是说如果需要在某个字段上能点击，运行一些代码，或者跳转到其他文档，不能用全局变量来做字段的值的来源，虽然在有些场景数据可以存在计算出来的全局变量里面作为 field 的值，但是为了能让 DrillDown 工作，我们需要至少设计临时数据库来作为数据源。

代码例子

```AL
page 50000 "My Orders"
{
    Caption = 'My Orders';
    UsageCategory = Lists;
    ApplicationArea = All;
    PageType = ListPart;
    SourceTable = "Sales Header";
    Editable = true;
    DelayedInsert = true;
    layout
    {
        area(content)
        {
            repeater(Control1)
            {
                ShowCaption = false;
                Editable = true;
                field("Order Number"; Rec."No.")
                {
                    ApplicationArea = All;
                    ToolTip = 'Order Number';
                    trigger OnDrillDown()
                    begin
                        //这个函数会被调用
                    end;
                }
                field("GlobalData"; GlobalData)
                {
                    ApplicationArea = All;
                    ToolTip = 'GlobalData';
                    trigger OnDrillDown()
                    begin
                        //这个函数不会被调用
                    end;
                }
    }

    var
        GlobalData: Code[10];
}

```

通过设置 field 的 DrillDown Property 也不能解决这个问题。

参考文档：
https://learn.microsoft.com/en-us/dynamics365/business-central/dev-itpro/developer/properties/devenv-drilldown-property

https://learn.microsoft.com/en-us/dynamics365/business-central/dev-itpro/developer/triggers-auto/pagefield/devenv-ondrilldown-pagefield-trigger?source=recommendations
