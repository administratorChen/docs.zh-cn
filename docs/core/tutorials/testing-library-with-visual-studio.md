---
title: 在 Visual Studio 2017 中使用 .NET Core 测试 .NET Standard 类库
description: 为 .NET Core 类库创建单元测试项目。 验证 .NET Core 类库能否正确地进行单元测试。
author: BillWagner
ms.author: wiwagn
ms.date: 08/07/2017
dev_langs:
- csharp
- vb
ms.custom: vs-dotnet, seodoc18
ms.openlocfilehash: 242234d93bc1b8f9b88749f2e3bcfb37c2bde86d
ms.sourcegitcommit: ad800f019ac976cb669e635fb0ea49db740e6890
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73037963"
---
# <a name="test-a-net-standard-library-with-net-core-in-visual-studio-2017"></a>在 Visual Studio 2017 中使用 .NET Core 测试 .NET Standard 库

[在 Visual Studio 2017 中使用 C# 和 .NET Core 生成 .NET Standard 库](library-with-visual-studio.md)或[在 Visual Studio 2017 中使用 Visual Basic 和 .NET Core 生成 .NET Standard 库](vb-library-with-visual-studio.md)中，创建了简单的类库，用于向 <xref:System.String> 类添加扩展方法。 现在，将创建一个单元测试，用于确保此类库能够按预期运行。 向在上一篇文章中创建的解决方案添加单元测试项目。

## <a name="creating-a-unit-test-project"></a>创建单元测试项目

若要创建单元测试项目，请执行以下操作：

<!-- markdownlint-disable MD025 -->

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

1. 在“解决方案资源管理器”  中，打开“ClassLibraryProjects”  解决方案节点的上下文菜单，再依次选择“添加”   > “新项目”  。

1. 在“添加新项目”  对话框中，选择“Visual C#”  节点。 然后，依次选择“.NET Core”  节点和“MSTest 测试项目(.NET Core)”  项目模板。 在“名称”  文本框中，输入项目名称“StringLibraryTest”。 选择“确定”  ，创建单元测试项目。

   ![“添加新项目”对话框，其中显示单元测试项目 - C#](./media/testing-library-with-visual-studio/create-new-test-project.png)

   > [!NOTE]  
   > 除了 MSTest 测试项目之外，还可以使用 Visual Studio 为 .NET Core 创建 xUnit 测试项目。

1. 此时，Visual Studio 会创建项目，并在代码窗口中打开 UnitTest1.cs  文件。

   ![Visual Studio 代码窗口，用于单元测试项目的类和方法 - C#](./media/testing-library-with-visual-studio/unit-test-editor-window.png)

   单元测试模板创建的源代码负责执行以下操作：

   - 它会导入 <xref:Microsoft.VisualStudio.TestTools.UnitTesting?displayProperty=nameWithType> 命名空间，其中包含用于单元测试的类型。

   - 向 `UnitTest1` 类应用 <xref:Microsoft.VisualStudio.TestTools.UnitTesting.TestClassAttribute> 特性。 测试类中标记有 \[TestMethod\] 属性的所有测试方法都会在单元测试运行时自动执行。

   - 它会应用 <xref:Microsoft.VisualStudio.TestTools.UnitTesting.TestMethodAttribute> 属性，将 `TestMethod1` 定义为在单元测试运行时自动执行的测试方法。

1. 在“解决方案资源管理器”  中，右键单击“StringLibraryTest”  项目的“依赖项”  节点，并从上下文菜单中选择“添加引用”  。

   ![StringLibraryTest 依赖项的上下文菜单 - C#](./media/testing-library-with-visual-studio/add-reference-context-menu.png)

1. 在“引用管理器”  对话框中，展开“项目”  节点，并选中“StringLibrary”  旁边的框。 添加对 `StringLibrary` 程序集的引用后，编译器可以查找 StringLibrary  方法。 选择“确定”  按钮。 这会添加对类库项目 `StringLibrary` 的引用。

   ![Visual Studio“添加项目引用”对话框](./media/testing-library-with-visual-studio/project-reference-manager.png)

# <a name="visual-basictabvb"></a>[Visual Basic](#tab/vb)

1. 在“解决方案资源管理器”  中，打开“ClassLibraryProjects”  解决方案节点的上下文菜单，再依次选择“添加”   > “新项目”  。

1. 在“添加新项目”  对话框中，选择“Visual Basic”  节点。 然后，依次选择“.NET Core”  节点和“MSTest 测试项目(.NET Core)”  项目模板。 在“名称”  文本框中，输入项目名称“StringLibraryTest”。 选择“确定”  ，创建单元测试项目。

   ![“添加新项目”对话框，其中显示单元测试项目 - Visual Basic](./media/testing-library-with-visual-studio/vb-create-new-test-project.png)

   > [!NOTE]  
   > 除了 MSTest 测试项目之外，还可以使用 Visual Studio 为 .NET Core 创建 xUnit 测试项目。

1. 此时，Visual Studio 会创建项目，并在代码窗口中打开 UnitTest1.vb  文件。

   ![Visual Studio 代码窗口，用于单元测试项目的类和方法 - Visual Basic](./media/testing-library-with-visual-studio/vb-unit-test-editor-window.png)

   单元测试模板创建的源代码负责执行以下操作：

   - 它会导入 <xref:Microsoft.VisualStudio.TestTools.UnitTesting?displayProperty=nameWithType> 命名空间，其中包含用于单元测试的类型。

   - 向 `UnitTest1` 类应用 <xref:Microsoft.VisualStudio.TestTools.UnitTesting.TestClassAttribute> 特性。 测试类中标记有 <xref:Microsoft.VisualStudio.TestTools.UnitTesting.TestMethodAttribute> 特性的所有测试方法都会在单元测试运行时自动执行。

   - 它会应用 <xref:Microsoft.VisualStudio.TestTools.UnitTesting.TestMethodAttribute> 属性，将 `TestMethod1` 定义为在单元测试运行时自动执行的测试方法。

1. 在“解决方案资源管理器”  中，右键单击“StringLibraryTest”  项目的“依赖项”  节点，并从上下文菜单中选择“添加引用”  。

   ![StringLibraryTest 依赖项的上下文菜单](./media/testing-library-with-visual-studio/add-reference-context-menu.png)

1. 在“引用管理器”  对话框中，展开“项目”  节点，并选中“StringLibrary”  旁边的框。 添加对 `StringLibrary` 程序集的引用后，编译器可以查找 StringLibrary  方法。 选择“确定”  按钮。 这会添加对类库项目 `StringLibrary` 的引用。

   ![Visual Studio“添加项目引用”对话框 - Visual Basic](./media/testing-library-with-visual-studio/project-reference-manager.png)

---

## <a name="adding-and-running-unit-test-methods"></a>添加并运行单元测试方法

运行单元测试时，Visual Studio 执行单元测试类（对其应用了 <xref:Microsoft.VisualStudio.TestTools.UnitTesting.TestClassAttribute> 特性的类）中标记有 <xref:Microsoft.VisualStudio.TestTools.UnitTesting.TestMethodAttribute> 特性的所有方法。 当第一次遇到测试不通过或测试方法中的所有测试均已成功通过时，测试方法终止。

最常见的测试调用 <xref:Microsoft.VisualStudio.TestTools.UnitTesting.Assert> 类的成员。 许多断言方法至少包含两个参数，其中一个是预期的测试结果，另一个是实际的测试结果。 下表显示了最常调用的一些方法：

断言方法 | 函数
--- | ---
`Assert.AreEqual` | 验证两个值或对象是否相等。 如果值或对象不相等，则断言失败。
`Assert.AreSame` | 验证两个对象变量引用的是否是同一个对象。 如果这些变量引用不同的对象，则断言失败。
`Assert.IsFalse` | 验证条件是否为 `false`。 如果条件为 `true`，则断言失败。
`Assert.IsNotNull` | 验证对象是否不为 `null`。 如果对象为 `null`，则断言失败。

还可向测试方法应用 <xref:Microsoft.VisualStudio.TestTools.UnitTesting.ExpectedExceptionAttribute> 特性。 它指明了测试方法预计会引发的异常类型。 如果未抛出指定异常，则测试不通过。

测试 `StringLibrary.StartsWithUpper` 方法时，需要提供许多以大写字符开头的字符串。 在这种情况下，此方法应返回 `true`，以便可以调用 <xref:Microsoft.VisualStudio.TestTools.UnitTesting.Assert.IsTrue%2A> 方法。 同样，需要提供许多以非大写字符开头的字符串。 在这种情况下，此方法应返回 `false`，以便可以调用 <xref:Microsoft.VisualStudio.TestTools.UnitTesting.Assert.IsFalse%2A> 方法。

由于库方法处理的是字符串，因此还需要确保它能够成功处理[空字符串 (`String.Empty`)](xref:System.String.Empty)（不含字符且 <xref:System.String.Length> 为 0 的有效字符串）和 `null` 字符串（尚未初始化的字符串）。 如果对 <xref:System.String> 实例调用 `StartsWithUpper` 作为扩展方法，无法向其传递 `null` 字符串。 不过，还可以直接将其作为静态方法进行调用，并向其传递一个 <xref:System.String> 自变量。

将定义三个方法，每个方法都会对字符串数组中的各个元素反复调用它的 <xref:Microsoft.VisualStudio.TestTools.UnitTesting.Assert> 方法。 由于测试方法在第一次遇到测试不通过时会立即失败，因此将调用方法重载，以便传递字符串来指明方法调用中使用的字符串值。

创建测试方法：

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

1. 将 UnitTest1.cs  代码窗口中的代码替换为以下代码：

   [!code-csharp[Test#1](~/samples/snippets/csharp/getting_started/with_visual_studio_2017/testlib1.cs)]

   请注意，`TestStartsWithUpper` 方法中测试的大写字符包括希腊文大写字母 alpha (U+0391) 和西里尔文大写字母 EM (U+041C)，`TestDoesNotStartWithUpper` 方法中测试的小写字符包括希腊文小写字母 alpha (U+03B1) 和西里尔文小写字母 Ghe (U+0433)。

1. 在菜单栏上，选择“文件”   > “将 UnitTest1.cs 另存为”  。 在“文件另存为”  对话框中，选择“保存”  按钮旁边的箭头，然后选择“保存时使用编码”  。

   ![Visual Studio“文件另存为”对话框 - C#](./media/testing-library-with-visual-studio/save-file-as-dialog.png)

# <a name="visual-basictabvb"></a>[Visual Basic](#tab/vb)

1. 将 UnitTest1.vb  代码窗口中的代码替换为以下代码：

    [!code-vb[Test#1](~/samples/snippets/core/tutorials/vb-library-with-visual-studio/testlib.vb)]

   请注意，`TestStartsWithUpper` 方法中测试的大写字符包括希腊文大写字母 alpha (U+0391) 和西里尔文大写字母 EM (U+041C)，`TestDoesNotStartWithUpper` 方法中测试的小写字符包括希腊文小写字母 alpha (U+03B1) 和西里尔文小写字母 Ghe (U+0433)。

1. 在菜单栏上，依次选择“文件”   > “将 UnitTest1.vb 另存为”  。 在“文件另存为”  对话框中，选择“保存”  按钮旁边的箭头，然后选择“保存时使用编码”  。

   ![Visual Studio“文件另存为”对话框 - Visual Basic](./media/testing-library-with-visual-studio/save-file-as-dialog.png)

---

1. 在“确认另存为”  对话框中，选择“是”  按钮，保存文件。

1. 在“高级保存选项”  对话框的“编码”  下拉列表中，选择“Unicode (UTF-8 带签名) - 代码页 65001”  ，然后选择“确定”  。

   ![Visual Studio“高级保存选项”对话框](./media/testing-library-with-visual-studio/advanced-save-options.png)

   如果无法将源代码保存为 UTF8 编码文件，Visual Studio 可能会将其另存为 ASCII 文件。 在这种情况下，运行时将无法准确解码 ASCII 范围以外的 UTF8 字符，且测试结果也会不准确。

1. 在菜单栏上，选择“测试”   > “运行”   > “所有测试”  。 此时，“测试资源管理器”  窗口打开并显示测试已成功运行。 “通过的测试”  部分列出了三个测试，“摘要”  部分报告了测试运行结果。

   ![通过测试的测试资源管理器窗口](./media/testing-library-with-visual-studio/test-explorer-window.png)

## <a name="handling-test-failures"></a>处理未通过的测试

由于运行的测试均通过，因此需进行少量改动，以使其中一个测试方法失败：

1. 通过修改 `TestDoesNotStartWithUpper` 方法中的 `words` 数组来包含字符串“Error”。 由于 Visual Studio 将在生成运行测试的解决方案时自动保存打开的文件，因此无需手动保存。

   ```csharp
   string[] words = { "alphabet", "Error", "zebra", "abc", "αυτοκινητοβιομηχανία", "государство",
                      "1234", ".", ";", " " };
   ```

   ```vb
   Dim words() As String = { "alphabet", "Error", "zebra", "abc", "αυτοκινητοβιομηχανία", "государство",
                      "1234", ".", ";", " " }

   ```

1. 从菜单栏中选择“测试”   > “运行”   > “所有测试”  ，运行测试。 “测试资源管理器”  窗口指示有两个测试成功，还有一个失败。

   ![未通过测试的测试资源管理器窗口](./media/testing-library-with-visual-studio/failed-test-window.png)

1. 在“未通过的测试”  部分中，选择未通过的测试 `TestDoesNotStartWith`。 “测试资源管理器”窗口显示断言生成的消息：  “Assert.IsFalse 失败。 “Error”应返回 false；实际返回 True”。 由于此次失败，数组中“Error”之后的所有字符串都未进行测试。

   ![显示 Is False 断言失败的“测试资源管理器”窗口](./media/testing-library-with-visual-studio/failed-test-detail.png)

1. 撤消在步骤 1 中执行的修改并删除字符串“Error”。 重新运行测试，测试将通过。

## <a name="testing-the-release-version-of-the-library"></a>测试库的发行版本

现已测试库的调试版本。 至此，测试已全部通过，且已充分测试库，应对库的发布版本再运行一次这些测试。 许多因素（包括编译器优化）有时可能会导致调试版本和发行版本出现行为差异。

若要测试发行版本，请执行以下操作：

1. 在 Visual Studio 工具栏中，将生成配置从 **“调试”** 更改为 **“发行”** 。

   ![Visual Studio 工具栏，其中突出显示发布版本](./media/testing-library-with-visual-studio/visual-studio-toolbar-release.png)

1. 在“解决方案资源管理器”  中，右键单击“StringLibrary”  项目，从上下文菜单中选择“生成”  ，重新编译库。

   ![带有生成命令的 StringLibrary 上下文菜单](./media/testing-library-with-visual-studio/build-library-context-menu.png)

1. 从菜单栏中选择“测试”   > “运行”   > “所有测试”  ，运行单元测试。 测试通过。

至此，已完成对库的测试，下一步就是使其可供调用方使用。 可以将类库与一个或多个应用程序捆绑在一起，也可以 NuGet 包的形式分发类库。 有关详细信息，请参阅[使用 .NET Standard 类库](./consuming-library-with-visual-studio.md)。
