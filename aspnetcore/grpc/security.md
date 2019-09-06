---
title: Considerações de segurança no gRPC para ASP.NET Core
author: jamesnk
description: Saiba mais sobre as considerações de segurança para o gRPC para ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 07/07/2019
uid: grpc/security
ms.openlocfilehash: f84bec0ef485b701b2be36384a2e49b9b28e473d
ms.sourcegitcommit: 8b36f75b8931ae3f656e2a8e63572080adc78513
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/05/2019
ms.locfileid: "70310362"
---
# <a name="security-considerations-in-grpc-for-aspnet-core"></a>Considerações de segurança no gRPC para ASP.NET Core

Por [James Newton – King](https://twitter.com/jamesnk)

Este artigo fornece informações sobre como proteger o gRPC com o .NET Core.

## <a name="transport-security"></a>Segurança de transporte

as mensagens gRPC são enviadas e recebidas usando HTTP/2. Recomendamos:

* O protocolo [TLS](https://tools.ietf.org/html/rfc5246) é usado para proteger mensagens em aplicativos gRPC de produção.
* os serviços gRPCs devem escutar e responder apenas por portas seguras.

O TLS está configurado em Kestrel. Para obter mais informações sobre como configurar pontos de extremidade do Kestrel, consulte [configuração do ponto de extremidades do Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration).

## <a name="exceptions"></a>Exceções

As mensagens de exceção geralmente são consideradas dados confidenciais que não devem ser revelados a um cliente. Por padrão, o gRPC não envia os detalhes de uma exceção gerada por um serviço gRPC para o cliente. Em vez disso, o cliente recebe uma mensagem genérica indicando que ocorreu um erro. A entrega de mensagem de exceção ao cliente pode ser substituída (por exemplo, em desenvolvimento ou teste) com [EnableDetailedErrors](xref:grpc/configuration#configure-services-options). As mensagens de exceção não devem ser expostas ao cliente em aplicativos de produção.

## <a name="message-size-limits"></a>Limites de tamanho de mensagem

As mensagens de entrada para clientes e serviços do gRPC são carregadas na memória. Os limites de tamanho de mensagem são um mecanismo para ajudar a impedir que o gRPC consuma recursos excessivos.

o gRPC usa limites de tamanho por mensagem para gerenciar mensagens de entrada e saída. Por padrão, o gRPC limita as mensagens de entrada para 4 MB. Não há limite para mensagens de saída.

No servidor, os limites de mensagens gRPC podem ser configurados para todos os serviços `AddGrpc`em um aplicativo com:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.MaxReceiveMessageSize = 1 * 1024 * 1024; // 1 MB
        options.MaxSendMessageSize = 1 * 1024 * 1024; // 1 MB
    });
}
```

Os limites também podem ser configurados para um `AddServiceOptions<TService>`serviço individual usando o. Para obter mais informações sobre como configurar limites de tamanho de mensagem, consulte [configuração do gRPC](xref:grpc/configuration).

## <a name="client-certificate-validation"></a>Validação de certificado do cliente

Os [certificados de cliente](https://tools.ietf.org/html/rfc5246#section-7.4.4) são inicialmente validados quando a conexão é estabelecida. Por padrão, o Kestrel não executa validação adicional do certificado de cliente de uma conexão.

Recomendamos que os serviços gRPCs protegidos por certificados de cliente usem o pacote [Microsoft. AspNetCore. Authentication. Certificate](xref:security/authentication/certauth) . ASP.NET Core autenticação de certificação executará validação adicional em um certificado de cliente, incluindo:

* O certificado tem um EKU (uso estendido de chave) válido
* Está dentro do período de validade
* Verificar revogação de certificado
