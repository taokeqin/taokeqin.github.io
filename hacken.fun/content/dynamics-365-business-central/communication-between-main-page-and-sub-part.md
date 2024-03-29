Title: 主页面和子页面的通信
Date: 2023-03-07 23:00
Modified: 2023-03-07 23:00
Category: dynamics-365-business-central
Tags: business-central, main-page, sub-page, part
Slug: communication-between-main-page-and-sub-part
Authors: Hacken
Summary: 介绍常用的主页面和子页面的通信方式

这里讨论的 main 页面和子页面不是关于继承关系的两个页面，即不是用 extends 扩展的页面之间的通信，而是类似在 Sales Order 页面 Header 和 Sales Order Line 之间的通信。

简单来说要说明的就是

1. Header 发生了变化，怎么通知 Sales Order Line 页面。
2. Sales Order Line 页面发生了变化，怎么通知 Header 页面。

用下面这个例子来展示一种可能的通信方式，具体内容参见注释

注意在 Sales Order 页面的定义中有个 part，名字是：SalesLines，下面代码节选自: page 42 "Sales Order"

```cs
part(SalesLines; "Sales Order Subform")
{
    ApplicationArea = Basic, Suite;
    Editable = IsSalesLinesEditable;
    Enabled = IsSalesLinesEditable;
    SubPageLink = "Document No." = FIELD("No.");
    UpdatePropagation = Both; //这个属性很关键
}
```

```cs
pageextension 50000 "My Sales Order" extends "Sales Order"
{
    trigger OnOpenPage()
    begin
        //这里可以通过访问CurrPage.xxx
        //xxx表示field
        //这里通过SalesLines获取到这个part
        //通过CurrPage.SalesLines.Page获取这个子页面对象
        //CurrPage.SalesLines.Page.SetParentRec调用SalesLines页面的一个公共函数。
        //这样通过调用子页面的方法来进行向下数据传递
        CurrPage.SalesLines.Page.SetParentRec(Rec);
    end;
}
```

```cs
pageextension 50001 "My Sales Order Subform" extends "Sales Order Subform"
{
    layout
    {
        modify("No.")
        {
            trigger OnAfterValidate()
            begin
                // 下面这一行很重要，Get一下以获取最新的变更，否则保存的时候可能因为etag已经不是最新的了而保存失败
                ParentSalesHeaderRecord.Get(ParentSalesHeaderRecord."Document Type", ParentSalesHeaderRecord."No.");
                ParentSalesHeaderRecord."Location Code" := "MyLocation"//在这里改变上级页面的数据
                ParentSalesHeaderRecord.Modify();//保存变更到数据库
                CurrPage.Update();//触发更新
                //奇怪的是如果注释掉上面这一行功能也正常，这和UpdatePropagation属性描述的有一些出入。
            end;
        }
    }

    //这个函数用procedure定义，表示公共方法
    //参数是一个Sales Header的引用
    procedure SetParentRec(var PSalesHeaderRecord: Record "Sales Header")
    begin
        ParentSalesHeaderRecord := PSalesHeaderRecord;//这里会产生一个copy，所以在子页面中就得到了一个上级页面的数据
        ParentSalesHeaderRecord."Location Code" := "MyLocation"; // 注意，这一行不会改变主页面的Rec
        PSalesHeaderRecord."Location Code" := "MyLocation"; // 注意，这一行同样不会改变主页面的Rec
        //虽然PSalesHeaderRecord是引用出入，但却不是引用的行为，很奇怪，目前还没弄清楚为什么是这样！
    end;

    var
        ParentSalesHeaderRecord: Record "Sales Header";
}
```

所以简单来讲：

1. 向下通过 page 的方法调用传递数据，当然也可以传递主页面的 Rec 数据。
2. 保存主页面的 Rec 到 part，通过改变 Rec 的值，保存数据库，更新页面

在子页面如何获取主页面调用是传的参数呢？

可以通过如下函数测试看看在子页面中都获取到了哪些过滤器。

利用 Record.FilterGroup([Integer]) Method
https://learn.microsoft.com/en-us/dynamics365/business-central/dev-itpro/developer/methods-auto/record/record-filtergroup-method

```cs
trigger OnOpenPage()
begin
    Rec.FilterGroup(0);
    Message('OnOpenPage group 0 filter: %1', Rec.GetFilters());
    Rec.FilterGroup(1);
    Message('OnOpenPage group 1 filter: %1', Rec.GetFilters());
    Rec.FilterGroup(2);
    Message('OnOpenPage group 2 filter: %1', Rec.GetFilters());
    Rec.FilterGroup(3);
    Message('OnOpenPage group 3 filter: %1', Rec.GetFilters());
    Rec.FilterGroup(4);
    Message('OnOpenPage group 4 filter: %1', Rec.GetFilters());
    Rec.FilterGroup(0);//这里再把filter group设置回0
end;
```

一般如果只是希望查看 filter 的值，用完之后最好把 filter group 设置到之前使用的那个，默认是 0，否则后续的 filter 都会加到这个 group 中。
