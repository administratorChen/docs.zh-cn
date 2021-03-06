---
title: 特性列表
ms.date: 07/20/2015
helpviewer_keywords:
- attribute list
- attributes [Visual Basic], applying
ms.assetid: 5880073a-68a4-4b6b-8a07-ace32959a4e2
ms.openlocfilehash: f9332f52622551bb6b944242f71bd80f439982e9
ms.sourcegitcommit: 17ee6605e01ef32506f8fdc686954244ba6911de
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/22/2019
ms.locfileid: "74354063"
---
# <a name="attribute-list-visual-basic"></a>特性列表 (Visual Basic)
指定要应用于已声明的编程元素的特性。 用逗号分隔多个属性。 下面是一个属性的语法。  
  
## <a name="syntax"></a>语法  
  
```vb  
[ attributemodifier ] attributename [ ( attributearguments | attributeinitializer ) ]  
```  
  
## <a name="parts"></a>部件  
|||
|---|---|
|`attributemodifier`|应用于源文件开头的属性是必需的。 可以是[程序集](../../../visual-basic/language-reference/modifiers/assembly.md)或[模块](../../../visual-basic/language-reference/modifiers/module-keyword.md)。|
|`attributename`| 必需。 属性的名称。|
|`attributearguments`|可选。 此特性的位置自变量列表。 多个参数之间用逗号分隔。|
|`attributeinitializer`|可选。 此特性的变量或属性初始值设定项的列表。 多个初始值设定项用逗号分隔。|
  
## <a name="remarks"></a>备注  
 可以将一个或多个属性应用于几乎所有编程元素（类型、过程、属性等）。 特性显示在程序集的元数据中，它们可帮助你批注代码或指定如何使用特定编程元素。 您可以应用 Visual Basic 和 .NET Framework 定义的属性，并且可以定义自己的属性。  

 有关何时使用属性的详细信息，请参阅[属性概述](../../../visual-basic/programming-guide/concepts/attributes/index.md)。 有关属性名称的信息，请参阅已[声明的元素名称](../../../visual-basic/programming-guide/language-features/declared-elements/declared-element-names.md)。  
  
## <a name="rules"></a>规则  
  
- **虚拟.** 您可以将特性应用于大多数已声明的编程元素。 若要应用一个或多个特性，请将*特性块*置于元素声明的开头。 "属性" 列表中的每个条目指定要应用的属性，以及用于此属性调用的修饰符和参数。  
  
- **尖括号。** 如果提供了属性列表，则必须将其括在尖括号（"`<`" 和 "`>`"）中。  
  
- **声明的一部分。** 特性必须是元素声明的一部分，而不是单独的语句。 您可以使用行继续符（"`_`"）将声明语句扩展到多个源代码行中。  
  
- **组成.** 在源文件开头应用于编程元素的每个特性都需要特性修饰符（`Assembly` 或 `Module`）。 应用于不在源文件开头的元素的特性上不允许使用特性修饰符。  
  
- **形参.** 特性的所有位置参数都必须在任何变量或属性初始值设定项之前。  
  
## <a name="example"></a>示例  
 下面的示例将 <xref:System.Runtime.InteropServices.DllImportAttribute> 特性应用于 `Function` 过程的主干定义。  
  
 [!code-vb[VbVbalrStatements#1](~/samples/snippets/visualbasic/VS_Snippets_VBCSharp/VbVbalrStatements/VB/Class1.vb#1)]  
  
 <xref:System.Runtime.InteropServices.DllImportAttribute> 指示特性化过程表示非托管动态链接库（DLL）中的入口点。 特性提供 DLL 名称作为位置参数，将其他信息作为变量初始值设定项提供。  
  
## <a name="see-also"></a>另请参阅

- [程序集](../../../visual-basic/language-reference/modifiers/assembly.md)
- [Module \<关键字>](../../../visual-basic/language-reference/modifiers/module-keyword.md)
- [属性概述](../../../visual-basic/programming-guide/concepts/attributes/index.md)
- [如何：在代码中拆分和合并语句](../../../visual-basic/programming-guide/program-structure/how-to-break-and-combine-statements-in-code.md)
