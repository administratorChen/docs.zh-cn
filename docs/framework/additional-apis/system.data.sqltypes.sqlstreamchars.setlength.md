---
title: SqlStreamChars.SetLength(Int64) 方法 (System.Data.SqlTypes)
author: douglaslMS
ms.author: douglasl
ms.date: 12/20/2018
ms.technology:
- dotnet-data
api_name:
- System.Data.SqlTypes.SqlStreamChars.SetLength
api_location:
- System.Data.dll
api_type:
- Assembly
ms.openlocfilehash: 7eb2b1bfe0a3d180b54c41cbca1d645dfb402be7
ms.sourcegitcommit: 4ac80713f6faa220e5a119d5165308a58f7ccdc8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2019
ms.locfileid: "54152760"
---
# <a name="sqlstreamcharssetlengthint64-method"></a><span data-ttu-id="6c483-102">SqlStreamChars.SetLength(Int64) 方法</span><span class="sxs-lookup"><span data-stu-id="6c483-102">SqlStreamChars.SetLength(Int64) Method</span></span>

<span data-ttu-id="6c483-103">当在派生类中重写时释放流使用的资源。</span><span class="sxs-lookup"><span data-stu-id="6c483-103">When overridden in a derived class, releases the resources used by the stream.</span></span> <span data-ttu-id="6c483-104">包含此方法的程序集具有与 SQLAccess.dll 友元关系。</span><span class="sxs-lookup"><span data-stu-id="6c483-104">The assembly that contains this method has a friend relationship with SQLAccess.dll.</span></span> <span data-ttu-id="6c483-105">它被用于 SQL server 上。</span><span class="sxs-lookup"><span data-stu-id="6c483-105">It's intended for use by SQL Server.</span></span> <span data-ttu-id="6c483-106">对于其他数据库，使用提供该数据库的宿主机制。</span><span class="sxs-lookup"><span data-stu-id="6c483-106">For other databases, use the hosting mechanism provided by that database.</span></span>

```csharp
public abstract void SetLength (long value);
```

## <a name="parameters"></a><span data-ttu-id="6c483-107">参数</span><span class="sxs-lookup"><span data-stu-id="6c483-107">Parameters</span></span>

`value`\
<span data-ttu-id="6c483-108">所需的当前流的长度（以字节表示）。</span><span class="sxs-lookup"><span data-stu-id="6c483-108">The desired length of the current stream in bytes.</span></span>

## <a name="remarks"></a><span data-ttu-id="6c483-109">备注</span><span class="sxs-lookup"><span data-stu-id="6c483-109">Remarks</span></span>

> [!WARNING]
> <span data-ttu-id="6c483-110">`SqlStreamChars.SetLength`方法是私有的不适合直接在代码中使用。</span><span class="sxs-lookup"><span data-stu-id="6c483-110">The `SqlStreamChars.SetLength` method is private and is not meant to be used directly in your code.</span></span>
>
> <span data-ttu-id="6c483-111">Microsoft 不支持在生产应用程序在任何情况下使用此字段。</span><span class="sxs-lookup"><span data-stu-id="6c483-111">Microsoft does not support the use of this field in a production application under any circumstance.</span></span>

## <a name="requirements"></a><span data-ttu-id="6c483-112">要求</span><span class="sxs-lookup"><span data-stu-id="6c483-112">Requirements</span></span>

<span data-ttu-id="6c483-113">**Namespace**：<xref:System.Data.SqlTypes></span><span class="sxs-lookup"><span data-stu-id="6c483-113">**Namespace:** <xref:System.Data.SqlTypes></span></span>

<span data-ttu-id="6c483-114">**程序集：** System.Data （在 System.Data.dll 中)</span><span class="sxs-lookup"><span data-stu-id="6c483-114">**Assembly:** System.Data (in System.Data.dll)</span></span>

<span data-ttu-id="6c483-115">**.NET framework 版本：** 自 2.0 之后可用。</span><span class="sxs-lookup"><span data-stu-id="6c483-115">**.NET Framework versions:** Available since 2.0.</span></span>