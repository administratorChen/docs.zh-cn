---
ms.openlocfilehash: 3f702febc78488b9413ec9303ded211493650f02
ms.sourcegitcommit: 5a28f8eb071fcc09b045b0c4ae4b96898673192e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/31/2019
ms.locfileid: "73198364"
---
### <a name="mvc-precompilation-tool-deprecated"></a>MVC：预编译工具已弃用

在 ASP.NET Core 1.1 中，引入了 `Microsoft.AspNetCore.Mvc.Razor.ViewCompilation`（MVC 预编译工具）包以添加对发布时进行 Razor 文件（.cshtml  文件）编译的支持。 在 ASP.NET Core 2.1 中，引入了 [Razor SDK](/aspnet/core/razor-pages/sdk?view=aspnetcore-2.1)，以扩展预编译工具的功能。 Razor SDK 添加了对生成时和发布时进行 Razor 文件编译的支持。 SDK 在生成时验证 .cshtml  文件的正确性，同时缩短应用的启动时间。 默认情况下，Razor SDK 处于启用状态，并且不需要任何手势即可开始使用。

在 ASP.NET Core 3.0 中，已删除 ASP.NET Core 1.1 时代的 MVC 预编译工具。 早期的包版本将继续收到修补版本中的重要 Bug 和安全修补程序。

#### <a name="version-introduced"></a>引入的版本

3.0

#### <a name="old-behavior"></a>旧行为

`Microsoft.AspNetCore.Mvc.Razor.ViewCompilation` 包用于预编译 MVC Razor 视图。

#### <a name="new-behavior"></a>新行为

Razor SDK 本机支持此功能。 `Microsoft.AspNetCore.Mvc.Razor.ViewCompilation` 包不再更新。

#### <a name="reason-for-change"></a>更改原因

Razor SDK 提供更多的功能并在生成时验证 .cshtml  文件的正确性。 SDK 还会缩短应用的启动时间。

#### <a name="recommended-action"></a>建议的操作

对于 ASP.NET Core 2.1 或更高版本的用户，更新以在 [Razor SDK](/aspnet/core/razor-pages/sdk?view=aspnetcore-3.0) 中使用本机支持进行预编译。 如果 Bug 或缺失的功能阻止迁移到 Razor SDK，请在 [aspnet/AspNetCore](https://github.com/aspnet/AspNetCore/issues) 中提出问题。

#### <a name="category"></a>类别

ASP.NET Core

#### <a name="affected-apis"></a>受影响的 API

无

<!-- 

### Affected APIs

Not detectable via API analysis

-->
