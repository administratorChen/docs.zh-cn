---
title: <behaviors>的工作流
ms.date: 03/30/2017
ms.topic: reference
ms.assetid: 3c6017b6-0c4f-4192-bd67-9515f5d1ec82
ms.openlocfilehash: 05a15cdf5c043eb5d94b36028324310d2b7a8413
ms.sourcegitcommit: 093571de904fc7979e85ef3c048547d0accb1d8a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70398881"
---
# <a name="behaviors-of-workflow"></a>\<工作流 > 行为
此元素包含**serviceBehaviors**集合。  集合中的每个元素定义工作流服务所使用的行为元素。 每个行为元素都由其唯一**名称**属性标识。  
  
[ **\<configuration>** ](../configuration-element.md)\
&nbsp;&nbsp;[ **\<主板.>** ](system-servicemodel-of-workflow.md)\
&nbsp;&nbsp;&nbsp;&nbsp; **\<行为 >**  
  
## <a name="syntax"></a>语法  
  
```xml  
<behaviors>  
  <serviceBehaviors>  
  </serviceBehaviors>  
</behaviors>  
```  
  
## <a name="attributes-and-elements"></a>特性和元素  
 下列各节描述了特性、子元素和父元素。  
  
### <a name="attributes"></a>特性  
 无  
  
### <a name="child-elements"></a>子元素  
  
|元素|描述|  
|-------------|-----------------|  
|[\<serviceBehaviors>](servicebehaviors-of-workflow.md)|此配置节表示为特定工作流服务定义的所有行为。|  
  
### <a name="parent-elements"></a>父元素  
  
|元素|描述|  
|-------------|-----------------|  
|[\<system.serviceModel>](../wcf/system-servicemodel.md)|所有工作流配置元素的根元素。|  
  
## <a name="see-also"></a>请参阅

- <xref:System.ServiceModel.Configuration.BehaviorsSection>
- <xref:System.ServiceModel.Configuration.ServiceBehaviorElementCollection>
- <xref:System.ServiceModel.Configuration.ServiceBehaviorElement>
- [使用行为配置和扩展运行时](../../../wcf/extending/configuring-and-extending-the-runtime-with-behaviors.md)
