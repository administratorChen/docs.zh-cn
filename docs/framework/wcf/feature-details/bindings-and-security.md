---
title: 绑定与安全
ms.date: 03/30/2017
helpviewer_keywords:
- bindings [WCF], security
- WCF security
- Windows Communication Foundation, security
- bindings [WCF]
ms.assetid: 4de03dd3-968a-4e65-af43-516e903d7f95
ms.openlocfilehash: 47d0df1e93e97c6398b794e62d9fd2e821d54f93
ms.sourcegitcommit: d6e27023aeaffc4b5a3cb4b88685018d6284ada4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2019
ms.locfileid: "67663240"
---
# <a name="bindings-and-security"></a>绑定与安全

系统提供的绑定包括与 Windows Communication Foundation (WCF) 提供程序的 WCF 应用程序的快速方法。 但有一个例外，就是所有绑定都启用了默认的安全方案。 本主题将帮助你根据安全需要来选择正确的绑定。

有关 WCF 安全的概述，请参阅[安全性概述](../../../../docs/framework/wcf/feature-details/security-overview.md)。 有关对 WCF 使用绑定进行编程的详细信息，请参阅[编程 WCF 安全](../../../../docs/framework/wcf/feature-details/programming-wcf-security.md)。

如果您已经选择一个绑定，您可以找出有关中的安全性与相关联的运行时行为的详细信息[安全行为](../../../../docs/framework/wcf/feature-details/security-behaviors-in-wcf.md)。

部分安全性功能无法用系统提供的绑定进行编程。 使用自定义绑定的更多控制，请参阅[使用自定义绑定的安全功能](../../../../docs/framework/wcf/feature-details/security-capabilities-with-custom-bindings.md)。

## <a name="security-functions-of-bindings"></a>绑定的安全功能

WCF 包含大量的系统提供满足大多数需求的绑定。 如果某个特定绑定不能满足要求，您还可以创建自定义绑定。 有关系统提供的绑定的列表，请参阅[System-Provided Bindings](../../../../docs/framework/wcf/system-provided-bindings.md)。 有关自定义绑定的详细信息，请参阅[自定义绑定](../../../../docs/framework/wcf/extending/custom-bindings.md)。

WCF 中的每个绑定有两种形式： 为 API 和配置文件中使用的 XML 元素形式。 例如， `WSHttpBinding` (API) 中具有对应[ \<wsHttpBinding >](../../../../docs/framework/configure-apps/file-schema/wcf/wshttpbinding.md)。

下一节列出了每个绑定的两种形式，并概括了安全功能。

### <a name="basichttp"></a>BasicHttp

在代码中，使用<xref:System.ServiceModel.BasicHttpBinding>类; 在配置中，使用[ \<basicHttpBinding >](../../../../docs/framework/configure-apps/file-schema/wcf/basichttpbinding.md)。

此绑定的目的是与如下一系列现有技术一起使用：

- ASP.NET Web 服务 (ASMX) 版本 1。

- Web Service Enhancements (WSE) 应用程序。

- Web 服务互操作性中定义的基本配置文件 (WS-我) 规范 (<https://go.microsoft.com/fwlink/?LinkId=38955>)。

- WS-I 中定义的基本安全配置文件。

默认情况下，此绑定是不安全的。 它的目的是与 ASMX 服务进行互操作。 启用安全性后，此绑定可以与 Internet 信息服务 (IIS) 安全机制（例如基本身份验证、摘要和 Windows 集成安全性）进行无缝的互操作。 有关详细信息，请参阅[传输安全概述](../../../../docs/framework/wcf/feature-details/transport-security-overview.md)。 此绑定支持以下功能：

- HTTPS 传输安全。

- HTTP 基本身份验证。

- WS-Security。

有关详细信息，请参阅<xref:System.ServiceModel.BasicHttpSecurity>, <xref:System.ServiceModel.BasicHttpMessageSecurity>, <xref:System.ServiceModel.BasicHttpMessageCredentialType>和<xref:System.ServiceModel.BasicHttpSecurityMode>.

### <a name="wshttpbinding"></a>WSHttpBinding

在代码中，使用<xref:System.ServiceModel.WSHttpBinding>类; 在配置中，使用[ \<wsHttpBinding >](../../../../docs/framework/configure-apps/file-schema/wcf/wshttpbinding.md)。

默认情况下，此绑定实现 WS-Security 规范，并提供与实现 WS-* 规范的服务的互操作性。 它支持以下功能：

- HTTPS 传输安全。

- WS-Security。

- 使用 SOAP 消息凭据安全对调用方进行身份验证的 HTTPS 传输保护。

有关详细信息，请参阅<xref:System.ServiceModel.WSHttpSecurity>， <xref:System.ServiceModel.MessageSecurityOverHttp>， <xref:System.ServiceModel.MessageCredentialType>， <xref:System.ServiceModel.SecurityMode>， <xref:System.ServiceModel.HttpTransportSecurity>， <xref:System.ServiceModel.HttpClientCredentialType>，和<xref:System.ServiceModel.HttpProxyCredentialType>。

### <a name="wsdualhttpbinding"></a>WSDualHttpBinding

在代码中，使用<xref:System.ServiceModel.WSDualHttpBinding>类; 在配置中，使用[ \<wsDualHttpBinding >](../../../../docs/framework/configure-apps/file-schema/wcf/wsdualhttpbinding.md)。

此绑定的目的是启用双工服务应用程序。 此绑定实现了 WS-Security 规范，以便获得基于消息的传送安全。 传输安全不可用。 默认情况下，它提供下列功能：

- 实现 WS-Reliable Messaging 以保证可靠性。

- 实现 WS-Security 以保证传送安全和身份验证。

- 使用 HTTP 进行消息传递。

- 使用文本/XML 消息编码。

 使用 WS-Security（消息层安全性），可通过此绑定配置下列参数：

- 用来确定加密算法的安全算法组。

- 下列功能的绑定选项：

  - 提供可在客户端带外使用的服务凭据。

  - 提供从服务协商的服务凭据作为通道设置的一部分。

有关详细信息，请参阅 <xref:System.ServiceModel.WSDualHttpSecurity> 和 <xref:System.ServiceModel.WSDualHttpSecurityMode>。

### <a name="nettcpbinding"></a>NetTcpBinding

在代码中，使用<xref:System.ServiceModel.NetTcpBinding>类; 在配置中，使用[ \<netTcpBinding >](../../../../docs/framework/configure-apps/file-schema/wcf/nettcpbinding.md)。

此绑定针对计算机之间的通信进行了优化。 默认情况下，它具有以下特征：

- 实现传输层安全性。

- 利用 Windows 安全性来实现传送安全性和身份验证。

- 使用 TCP 进行传输。

- 实现二进制消息编码。

- 实现 WS-Reliable Messaging。

此绑定具有下列选项：

- 消息层安全性（使用 WS-Security）。

- 使用消息凭据实现传输安全性：保密性和完整性由 Transport Layer Security (TLS) over TCP 提供，授权凭据由 WS-Security 提供。

有关详细信息，请参阅<xref:System.ServiceModel.NetTcpSecurity>， <xref:System.ServiceModel.TcpTransportSecurity>， <xref:System.ServiceModel.TcpClientCredentialType>， <xref:System.ServiceModel.MessageSecurityOverTcp>，和<xref:System.ServiceModel.MessageCredentialType>。

### <a name="netnamedpipebinding"></a>NetNamedPipeBinding

在代码中，使用<xref:System.ServiceModel.NetNamedPipeBinding>类; 在配置中，使用[ \<netNamedPipeBinding >](../../../../docs/framework/configure-apps/file-schema/wcf/netnamedpipebinding.md)。

此绑定针对进程之间的通信（通常在同一台计算机上）进行了优化。 默认情况下，此绑定具有以下特征：

- 使用传输安全性来实现消息传输和身份验证。

- 使用命名管道进行消息传递。

- 实现二进制消息编码。

- 加密和消息签名。

此绑定具有下列选项：

- 使用 Windows 安全性进行身份验证。

有关详细信息，请参阅<xref:System.ServiceModel.NetNamedPipeSecurity>、<xref:System.ServiceModel.NetNamedPipeSecurityMode>和<xref:System.ServiceModel.NamedPipeTransportSecurity>。

### <a name="msmqintegrationbinding"></a>MsmqIntegrationBinding

在代码中，使用<xref:System.ServiceModel.MsmqIntegration.MsmqIntegrationBinding>类; 在配置中，使用[ \<msmqIntegrationBinding >](../../../../docs/framework/configure-apps/file-schema/wcf/msmqintegrationbinding.md)。

此绑定最适合于与非-WCF Microsoft 消息队列 MSMQ 终结点创建 WCF 客户端和服务进行互操作。

默认情况下，此绑定使用传输安全性并提供下列安全特征：

- 可以禁用安全性 (None)。

- MSMQ 传输安全性 (Transport)。

有关详细信息，请参阅 <xref:System.ServiceModel.NetMsmqSecurity> 和 <xref:System.ServiceModel.NetMsmqSecurityMode>。

### <a name="netmsmqbinding"></a>NetMsmqBinding

在代码中，使用<xref:System.ServiceModel.NetMsmqBinding>类; 在配置中，使用[ \<netMsmqBinding >](../../../../docs/framework/configure-apps/file-schema/wcf/netmsmqbinding.md)。

创建 WCF 服务需要 MSMQ 排队消息支持时，此绑定旨在用于。

默认情况下，此绑定使用传输安全性并提供下列安全特征：

- 可以禁用安全性 (None)。

- MSMQ 传输安全性 (Transport)。

- 基于 SOAP 的消息安全性 (Message)。

- 同时启用传输安全性和消息安全性 (Both)。

- 支持的客户端凭据类型：None、 Windows、 用户名、 证书、 IssuedToken。

仅当安全模式设置为 <xref:System.ServiceModel.MessageCredentialType.Certificate> 或 <xref:System.ServiceModel.NetMsmqSecurityMode.Both> 时，才支持 <xref:System.ServiceModel.NetMsmqSecurityMode.Message> 凭据。

有关详细信息，请参阅 <xref:System.ServiceModel.MessageSecurityOverMsmq> 和 <xref:System.ServiceModel.MsmqTransportSecurity>。

### <a name="wsfederationhttpbinding"></a>WSFederationHttpBinding

在代码中，使用<xref:System.ServiceModel.WSFederationHttpBinding>类; 在配置中，使用[ \<wsFederationHttpBinding >](../../../../docs/framework/configure-apps/file-schema/wcf/wsfederationhttpbinding.md)。

默认情况下，此绑定使用 WS-Security（消息层安全性）。

有关详细信息，请参阅[联合身份验证](../../../../docs/framework/wcf/feature-details/federation.md)， <xref:System.ServiceModel.WSFederationHttpSecurity>，和<xref:System.ServiceModel.WSFederationHttpSecurityMode>。

## <a name="custom-bindings"></a>自定义绑定

如果系统提供的绑定都不能满足您的需求，则可以使用自定义安全绑定元素来创建自定义绑定。 有关详细信息，请参阅[使用自定义绑定的安全功能](../../../../docs/framework/wcf/feature-details/security-capabilities-with-custom-bindings.md)。

## <a name="binding-choices"></a>绑定选择

下表概括了安全模式设置中提供的功能，也就是说，它列出了当安全模式设置为 `Transport`、`Message` 或 `TransportWithMessageCredential` 时可以使用的功能。 使用此表可帮助您找到应用程序所需的安全功能。

|设置|功能|
|-------------|--------------|
|传输|服务器身份验证<br /><br /> 客户端身份验证<br /><br /> 点对点安全性<br /><br /> 互操作性<br /><br /> 硬件加速<br /><br /> 高吞吐量<br /><br /> 安全防火墙<br /><br /> 高延迟应用程序<br /><br /> 跨越多个跃点重新加密|
|消息|服务器身份验证<br /><br /> 客户端身份验证<br /><br /> 端对端安全性<br /><br /> 互操作性<br /><br /> 丰富的声明<br /><br /> 联合<br /><br /> 多因素身份验证<br /><br /> 自定义令牌<br /><br /> 公证人/时间戳服务<br /><br /> 高延迟应用程序<br /><br /> 消息签名的持久性|
|TransportWithMessageCredential|服务器身份验证<br /><br /> 客户端身份验证<br /><br /> 点对点安全性<br /><br /> 互操作性<br /><br /> 硬件加速<br /><br /> 高吞吐量<br /><br /> 丰富的客户端声明<br /><br /> 联合<br /><br /> 多因素身份验证<br /><br /> 自定义令牌<br /><br /> 安全防火墙<br /><br /> 高延迟应用程序<br /><br /> 跨越多个跃点重新加密|

下表列出了支持各种模式设置的绑定。 请从该表中选择用来创建服务终结点的绑定。

|绑定|是否支持 Transport 模式|是否支持 Message 模式|是否支持 TransportWithMessageCredential|
|-------------|----------------------------|--------------------------|--------------------------------------------|
|`BasicHttpBinding`|是|是|是|
|`WSHttpBinding`|是|是|是|
|`WSDualHttpBinding`|No|是|No|
|`NetTcpBinding`|是|是|是|
|`NetNamedPipeBinding`|是|否|否|
|`NetMsmqBinding`|是|是|No|
|`MsmqIntegrationBinding`|是|否|否|
|`wsFederationHttpBinding`|否|是|是|

## <a name="transport-credentials-in-bindings"></a>绑定中的传输凭据

下表列出了在传输安全模式下使用 `BasicHttpBinding` 或 `WSHttpBinding` 时可用的客户端凭据类型。

|类型|描述|
|----------|-----------------|
|None|指定客户端不需要提供任何凭据。 这相当于匿名客户端。|
|Basic|基本身份验证 有关详细信息，请参阅 RFC 2617 – HTTP 身份验证：基本和摘要式身份验证，可在<https://go.microsoft.com/fwlink/?LinkId=84023>。|
|摘要|摘要式身份验证。 有关详细信息，请参阅 RFC 2617 – HTTP 身份验证：基本和摘要式身份验证，可在<https://go.microsoft.com/fwlink/?LinkId=84023>。|
|NTLM|NT LAN Manager (NTLM) 身份验证。|
|Windows|Windows 身份验证。|
|证书|使用证书执行的身份验证。|
|IssuedToken|允许服务要求客户端进行身份验证使用或由 CardSpace 颁发的安全令牌服务的令牌。 有关详细信息，请参阅[联合身份验证和颁发令牌](../../../../docs/framework/wcf/feature-details/federation-and-issued-tokens.md)。|

### <a name="message-client-credentials-in-bindings"></a>绑定中的消息客户端凭据

下表列出在 Message 安全模式下使用绑定时可用的客户端凭据类型。

|类型|描述|
|----------|-----------------|
|None|允许服务与匿名客户端交互。|
|Windows|允许在 Windows 凭据的已通过身份验证的上下文中执行 SOAP 消息交换。|
|UserName|允许服务要求使用用户名凭据对客户端进行身份验证。 请注意，当安全模式设置为`TransportWithMessageCredential`，WCF 不支持发送密码摘要，也派生密钥密码并使用此类密钥用于 Message 模式安全。 在这种情况下，WCF 强制执行传输的安全性时使用用户名凭据。|
|证书|允许服务要求使用证书对客户端进行身份验证。|
|IssuedToken|允许服务使用安全令牌服务来提供自定义令牌。|

## <a name="see-also"></a>请参阅

- [安全性概述](../../../../docs/framework/wcf/feature-details/security-overview.md)
- [保护服务和客户端的安全](../../../../docs/framework/wcf/feature-details/securing-services-and-clients.md)
- [选择凭据类型](../../../../docs/framework/wcf/feature-details/selecting-a-credential-type.md)
- [使用自定义绑定的安全功能](../../../../docs/framework/wcf/feature-details/security-capabilities-with-custom-bindings.md)
- [安全行为](../../../../docs/framework/wcf/feature-details/security-behaviors-in-wcf.md)
- [Windows Server App Fabric 的安全模型](https://go.microsoft.com/fwlink/?LinkID=201279&clcid=0x409)
