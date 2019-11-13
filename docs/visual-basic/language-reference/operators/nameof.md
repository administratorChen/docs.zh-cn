---
title: NameOf 运算符-Visual Basic
description: 了解如何在 Visual Basic 中使用 NameOf 运算符
ms.date: 10/27/2019
helpviewer_keywords:
- NameOf operator [Visual Basic]
ms.openlocfilehash: 8416bb1a1715c1c37b62cac6a9e0b427a9c72547
ms.sourcegitcommit: ad800f019ac976cb669e635fb0ea49db740e6890
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73041351"
---
# <a name="nameof-operator---visual-basic"></a><span data-ttu-id="d79dd-103">NameOf 运算符-Visual Basic</span><span class="sxs-lookup"><span data-stu-id="d79dd-103">NameOf operator - Visual Basic</span></span>

<span data-ttu-id="d79dd-104">`NameOf` 运算符获取变量、类型或成员的名称作为字符串常量：</span><span class="sxs-lookup"><span data-stu-id="d79dd-104">The `NameOf` operator obtains the name of a variable, type, or member as the string constant:</span></span>

```vb
Console.WriteLine(NameOf(System.Collections.Generic))  ' output: Generic
Console.WriteLine(NameOf(List(Of Integer)))  ' output: List
Console.WriteLine(NameOf(List(Of Integer).Count))  ' output: Count
Console.WriteLine(NameOf(List(Of Integer).Add))  ' output: Add

Dim numbers As New List(Of Integer) From { 1, 2, 3 }
Console.WriteLine(NameOf(numbers))  ' output: numbers
Console.WriteLine(NameOf(numbers.Count))  ' output: Count
Console.WriteLine(NameOf(numbers.Add))  ' output: Add
```

<span data-ttu-id="d79dd-105">如前面的示例所示，对于类型和命名空间，生成的名称通常不是[完全限定的](~/_csharplang/spec/basic-concepts.md#fully-qualified-names)名称。</span><span class="sxs-lookup"><span data-stu-id="d79dd-105">As the preceding example shows, in the case of a type and a namespace, the produced name is usually not [fully qualified](~/_csharplang/spec/basic-concepts.md#fully-qualified-names).</span></span>

<span data-ttu-id="d79dd-106">`NameOf` 运算符在编译时进行求值，在运行时无效。</span><span class="sxs-lookup"><span data-stu-id="d79dd-106">The `NameOf` operator is evaluated at compile time, and has no effect at run time.</span></span>

<span data-ttu-id="d79dd-107">可以使用 `NameOf` 运算符使参数检查代码更易于维护：</span><span class="sxs-lookup"><span data-stu-id="d79dd-107">You can use the `NameOf` operator to make the argument-checking code more maintainable:</span></span>

```vb
Private _name As String

Public Property Name As String
    Get
        Return _name
    End Get
    Set
        If value Is Nothing Then
            Throw New ArgumentNullException(NameOf(value), $"{NameOf(name)} cannot be null.")
        End If
    End Set
End Property
```

<span data-ttu-id="d79dd-108">`NameOf` 运算符在 Visual Basic 14 及更高版本中可用。</span><span class="sxs-lookup"><span data-stu-id="d79dd-108">The `NameOf` operator is available in Visual Basic 14 and later.</span></span>

## <a name="see-also"></a><span data-ttu-id="d79dd-109">请参阅</span><span class="sxs-lookup"><span data-stu-id="d79dd-109">See also</span></span>

- [<span data-ttu-id="d79dd-110">Visual Basic 语言参考</span><span class="sxs-lookup"><span data-stu-id="d79dd-110">Visual Basic Language Reference</span></span>](../index.md)
- [<span data-ttu-id="d79dd-111">运算符（Visual Basic）</span><span class="sxs-lookup"><span data-stu-id="d79dd-111">Operators (Visual Basic)</span></span>](index.md)