---
title: <typeparam> - C# 编程指南
ms.date: 07/20/2015
f1_keywords:
- typeparam
helpviewer_keywords:
- <typeparam> C# XML tag
- typeparam C# XML tag
ms.assetid: 9b99d400-e911-4e55-99c6-64367c96aa4f
ms.openlocfilehash: 04145b82cbed0e9a5cae38ff9ef33d061ee792c9
ms.sourcegitcommit: 5f236cd78cf09593c8945a7d753e0850e96a0b80
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/07/2020
ms.locfileid: "75694525"
---
# <a name="typeparam-c-programming-guide"></a>\<typeparam>（C# 编程指南）
## <a name="syntax"></a>语法  
  
```xml  
<typeparam name="name">description</typeparam>  
```  
  
## <a name="parameters"></a>参数  
 `name`  
 类型参数的名称。 用双引号 (" ") 将名称引起来。  
  
 `description`  
 类型参数的说明。  
  
## <a name="remarks"></a>备注  
 在泛型类型或方法声明的注释中，应使用 `<typeparam>` 标记来描述类型参数。 为泛型类型或方法的每个类型参数添加标记。  
  
 有关详细信息，请参阅[泛型](../generics/index.md)。  
  
 `<typeparam>` 标记的文本将显示在 IntelliSense、[对象浏览器窗口](/visualstudio/ide/viewing-the-structure-of-code#BKMK_ObjectBrowser)代码注释 Web 报表。  
  
 使用 [-doc](../../language-reference/compiler-options/doc-compiler-option.md) 进行编译以便将文档注释处理到文件中。  
  
## <a name="example"></a>示例  
 [!code-csharp[csProgGuideDocComments#13](~/samples/snippets/csharp/VS_Snippets_VBCSharp/csProgGuideDocComments/CS/DocComments.cs#13)]  
  
## <a name="see-also"></a>请参阅

- [C# 参考](../../language-reference/index.md)
- [C# 编程指南](../index.md)
- [建议的文档注释标记](./recommended-tags-for-documentation-comments.md)
