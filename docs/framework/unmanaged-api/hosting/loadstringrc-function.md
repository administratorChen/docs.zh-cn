---
title: LoadStringRC 函数
ms.date: 03/30/2017
api_name:
- LoadStringRC
api_location:
- mscoree.dll
api_type:
- DLLExport
f1_keywords:
- LoadStringRC
helpviewer_keywords:
- LoadStringRC function [.NET Framework hosting]
ms.assetid: 752e49b4-987c-4c28-a118-1a0c1ed510c5
topic_type:
- apiref
ms.openlocfilehash: 66d4c14234c7929af443922f86098b46a4aa6eb7
ms.sourcegitcommit: 559fcfbe4871636494870a8b716bf7325df34ac5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2019
ms.locfileid: "73122020"
---
# <a name="loadstringrc-function"></a>LoadStringRC 函数
使用当前线程的默认区域性将 HRESULT 值转换为错误消息。  
  
 此函数已在 .NET Framework 4 中弃用。  
  
## <a name="syntax"></a>语法  
  
```cpp  
HRESULT LoadStringRC (  
    [in]  UINT    iResourceID,   
    [out] LPWSTR  szBuffer,   
    [in]  int     iMax,   
    [in]  int     bQuiet  
);  
```  
  
## <a name="parameters"></a>参数  
 `iResourceID`  
 中HRESULT。  
  
 `szBuffer`  
 弄一个缓冲区，其中包含成功完成后的错误消息。  
  
 `iMax`  
 中错误消息缓冲区的大小。  
  
 `bQuiet`  
 中掉.  
  
## <a name="return-value"></a>返回值  
 除以下值外，此方法还返回 Winerror.h 中定义的标准组件对象模型（COM）错误代码。  
  
|返回代码|描述|  
|-----------------|-----------------|  
|S_OK|该方法已成功完成。|  
|E_INVALIDARG|`szBuffer` 为 null 或 `iMax` 为零（0）。|  
  
## <a name="remarks"></a>备注  
 如果该方法未成功完成，`szBuffer` 将包含空字符串。  
  
## <a name="requirements"></a>要求  
 **平台：** 请参阅[系统要求](../../../../docs/framework/get-started/system-requirements.md)。  
  
 **标头：** Mscoree.dll  
  
 **库：** Mscoree.dll 和 Mscorwks.dll。 使用 Mscoree.dll 而不是 Mscorwks.dll 来确保目标为正确的 .NET Framework 版本。  
  
 **.NET Framework 版本：** [!INCLUDE[net_current_v20plus](../../../../includes/net-current-v20plus-md.md)]  
  
## <a name="see-also"></a>请参阅

- [LoadStringRCEx 函数](../../../../docs/framework/unmanaged-api/hosting/loadstringrcex-function.md)
- [弃用的 CLR 承载函数](../../../../docs/framework/unmanaged-api/hosting/deprecated-clr-hosting-functions.md)
