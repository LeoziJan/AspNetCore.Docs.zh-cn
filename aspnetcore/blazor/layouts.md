---
title: ASP.NET Core Blazor 布局
author: guardrex
description: 了解如何为 Blazor 应用创建可重用布局组件。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/layouts
ms.openlocfilehash: 5b6e1c7ceb4a6e41230e31bbe379bde1bb0a8286
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "78647922"
---
# <a name="aspnet-core-opno-locblazor-layouts"></a>ASP.NET Core Blazor 布局

作者：[Rainer Stropek](https://www.timecockpit.com) 和 [Luke Latham](https://github.com/guardrex)

有些应用元素（例如菜单、版权消息和公司徽标）通常是应用整体布局的一部分，并被应用中的每个组件使用。 将这些元素的代码复制到应用的所有组件中并不是高效的方法 &mdash; 每当其中一个元素需要更新，每个组件都必须更新。 此类复制难以维护，并会随时间推移导致内容不一致。 *布局*可解决此问题。

从技术上讲，布局也是一个组件。 布局在 Razor 模板或 C# 代码中定义，并可使用[数据绑定](xref:blazor/data-binding)、[依赖项注入](xref:blazor/dependency-injection)和其他组件方案。

若要将*组件*转换为*布局*，该组件应：

* 继承自 `LayoutComponentBase`，后者为布局内的呈现内容定义 `Body` 属性。
* 使用 Razor 语法 `@Body` 在布局标记中指定呈现内容的位置。

以下代码示例显示布局组件 *MainLayout.razor* 的 Razor 模板。 布局继承 `LayoutComponentBase` 并在导航栏和页脚之间设置 `@Body`：

[!code-razor[](layouts/sample_snapshot/3.x/MainLayout.razor?highlight=1,13)]

在基于其中一个 Blazor 应用模板的应用中，`MainLayout` 组件 (*MainLayout.razor*) 位于应用的 *Shared* 文件夹中。

## <a name="default-layout"></a>默认布局

在应用的 `Router`App.razor*文件的* 组件中指定默认应用布局。 默认 `Router` 模板提供的以下 Blazor 组件将默认布局设置为 `MainLayout` 组件：

[!code-razor[](layouts/sample_snapshot/3.x/App1.razor?highlight=3)]

若要为 `NotFound` 内容提供默认布局，请为 `LayoutView` 内容指定 `NotFound`：

[!code-razor[](layouts/sample_snapshot/3.x/App2.razor?highlight=6-9)]

有关 `Router` 组件的详细信息，请参阅 <xref:blazor/routing>。

在路由器中将布局指定为默认布局是一种有用的做法，因为可以按组件或按文件夹进行替代。 最好使用路由器来设置应用的默认布局，因为这是最常规的方法。

## <a name="specify-a-layout-in-a-component"></a>在组件中指定布局

使用 Razor 指令 `@layout` 将布局应用于组件。 编译器将 `@layout` 转换为 `LayoutAttribute`，后者应用于组件类。

以下 `MasterList` 组件的内容插入到 `MasterLayout` 中 `@Body` 的位置：

[!code-razor[](layouts/sample_snapshot/3.x/MasterList.razor?highlight=1)]

直接在组件中指定布局会替代路由器中设置的*默认布局*或从 `@layout`_Imports.razor*导入的* 指令。

## <a name="centralized-layout-selection"></a>集中式布局选择

应用的每个文件夹都可以选择包含名为 *_Imports.razor* 的模板文件。 编译器将导入文件中指定的指令包括在同一文件夹中的所有 Razor 模板内，并在其所有子文件夹中以递归方式包括。 因此，包含 *的*_Imports.razor`@layout MyCoolLayout` 文件可确保文件夹中的所有组件都使用 `MyCoolLayout`。 无需将 `@layout MyCoolLayout` 重复添加到文件夹和子文件夹内的所有 *.razor* 文件。 `@using` 指令也以相同的方式应用于组件。

以下 *_Imports.razor* 文件导入：

* `MyCoolLayout`
* 同一文件夹以及任何子文件夹中的所有 Razor 组件。
* `BlazorApp1.Data` 命名空间。
 
[!code-razor[](layouts/sample_snapshot/3.x/_Imports.razor)]

*_Imports.razor* 文件类似于 [Razor 视图和页面的 _ViewImports.cshtml 文件](xref:mvc/views/layout#importing-shared-directives)，但专门应用于 Razor 组件文件。

在 *_Imports.razor* 中指定布局会替代指定为路由器*默认布局*的布局。

## <a name="nested-layouts"></a>嵌套布局

应用可以包含嵌套布局。 组件可以引用一个布局，该布局反过来引用另一个布局。 例如，嵌套布局用于创建多级菜单结构。

以下示例演示如何使用嵌套布局。 *EpisodesComponent.razor* 文件是要显示的组件。 该组件引用 `MasterListLayout`：

[!code-razor[](layouts/sample_snapshot/3.x/EpisodesComponent.razor?highlight=1)]

*MasterListLayout.razor* 文件提供 `MasterListLayout`。 该布局引用另一个布局 `MasterLayout` 并在其中呈现。 `EpisodesComponent` 在显示 `@Body` 的位置呈现：

[!code-razor[](layouts/sample_snapshot/3.x/MasterListLayout.razor?highlight=1,9)]

最后，`MasterLayout`MasterLayout.razor*中的* 包含顶级布局元素，例如页眉、主菜单和页脚。 具有 `MasterListLayout` 的 `EpisodesComponent` 在 `@Body` 显示的位置呈现：

[!code-razor[](layouts/sample_snapshot/3.x/MasterLayout.razor?highlight=6)]

## <a name="share-a-razor-pages-layout-with-integrated-components"></a>与集成组件共享 Razor Pages 布局

当可路由组件集成到 Razor Pages 应用中时，该应用的共享布局可与这些组件配合使用。 有关更多信息，请参见 <xref:blazor/integrate-components>。

## <a name="additional-resources"></a>其他资源

* <xref:mvc/views/layout>
