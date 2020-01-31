---
title: gRPC em aplicativos de navegador
author: jamesnk
description: Saiba como configurar os serviços gRPCs em ASP.NET Core para que possam ser chamados de aplicativos de navegador usando o gRPC-Web.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 01/24/2020
uid: grpc/browser
ms.openlocfilehash: 6359c3b76b3cb1ba2b6d9f9a989f64cbf4c4379d
ms.sourcegitcommit: b5ceb0a46d0254cc3425578116e2290142eec0f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2020
ms.locfileid: "76830656"
---
# <a name="grpc-in-browser-apps"></a>gRPC em aplicativos de navegador

Por [James Newton – King](https://twitter.com/jamesnk)

> [!IMPORTANT]
> **gRPC-o suporte da Web no .NET é experimental**
>
> gRPC-Web para .NET é um projeto experimental, não um produto confirmado. Queremos:
>
> * Teste a nossa abordagem para implementar o gRPC-Web Works.
> * Obtenha comentários sobre se essa abordagem é útil para os desenvolvedores do .NET em comparação com a maneira tradicional de configurar o gRPC-Web por meio de um proxy.
>
> Deixe comentários em [https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet) para garantir que criamos algo que os desenvolvedores gostam e que são produtivos com o.

Não é possível chamar um serviço gRPC HTTP/2 de um aplicativo baseado em navegador. [gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) é um protocolo que permite que aplicativos JavaScript e de mais alto do navegador chamem serviços gRPCs. Este artigo explica como usar o gRPC-Web no .NET Core.

## <a name="configure-grpc-web-in-aspnet-core"></a>Configurar gRPC-Web no ASP.NET Core

os serviços gRPCs hospedados no ASP.NET Core podem ser configurados para dar suporte a gRPC-Web juntamente com HTTP/2 gRPC. gRPC-Web não requer nenhuma alteração nos serviços. A única modificação é a configuração de inicialização.

Para habilitar o gRPC-Web com um serviço de gRPC de ASP.NET Core:

* Adicione uma referência ao pacote [Grpc. AspNetCore. Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) .
* Configure o aplicativo para usar o gRPC-Web adicionando `AddGrpcWeb` e `UseGrpcWeb` ao *Startup.cs*:

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=3,10,14)]

O código anterior:

* Adiciona o gRPC-middleware da Web, `UseGrpcWeb`, após o roteamento e antes dos pontos de extremidade.
* Especifica o método de `endpoints.MapGrpcService<GreeterService>()` dá suporte a gRPC-Web com `EnableGrpcWeb`. 

Como alternativa, configure todos os serviços para dar suporte a gRPC-Web adicionando `services.AddGrpcWeb(o => o.GrpcWebEnabled = true);` a configuraservices.

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=5,12,16)]

Algumas configurações adicionais podem ser necessárias para chamar gRPC-Web do navegador, como configurar ASP.NET Core para dar suporte a CORS. Para obter mais informações, consulte [support CORS](xref:security/cors).

## <a name="call-grpc-web-from-the-browser"></a>Chamar gRPC-Web do navegador

Os aplicativos de navegador podem usar gRPC-Web para chamar serviços gRPCs. Há alguns requisitos e limitações ao chamar serviços gRPCs com o gRPC-Web a partir do navegador:

* O servidor deve ter sido configurado para dar suporte a gRPC-Web.
* Não há suporte para streaming de cliente e chamadas de streaming bidirecionais. Há suporte para o streaming do servidor.
* A chamada de serviços gRPCs em um domínio diferente requer que o [CORS](xref:security/cors) seja configurado no servidor.

### <a name="javascript-grpc-web-client"></a>JavaScript gRPC-cliente Web

Há um cliente JavaScript gRPC-Web. Para obter instruções sobre como usar o gRPC-Web do JavaScript, consulte [gravar código de cliente JavaScript com gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).

### <a name="configure-grpc-web-with-the-net-grpc-client"></a>Configurar o gRPC-Web com o cliente .NET gRPC

O cliente .NET gRPC pode ser configurado para fazer chamadas gRPC-Web. Isso é útil para aplicativos [Webassembly](xref:blazor/index#blazor-webassembly) mais úteis, que são hospedados no navegador e têm as mesmas limitações de http do código JavaScript. Chamar gRPC-Web com um cliente .NET é [o mesmo que http/2 gRPC](xref:grpc/client). A única modificação é como o canal é criado.

Para usar o gRPC-Web:

* Adicione uma referência ao pacote [Grpc .net. Client. Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) .
* Configure o canal para usar o `GrpcWebHandler`:

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

O código anterior:

* Configura um canal para usar gRPC-Web.
* Cria um cliente e faz uma chamada usando o canal.

O `GrpcWebHandler` tem as seguintes opções de configuração quando criadas:

* **InnerHandler**: o <xref:System.Net.Http.HttpMessageHandler> subjacente que faz a chamada http, por exemplo, `HttpClientHandler`.
* **Modo**: `GrpcWebMode` enum. `GrpcWebMode.GrpcWebText` configura o conteúdo para ser codificado em base64, o que é necessário para dar suporte a chamadas de streaming de servidor.
* **HttpVersion**: `Version`de protocolo http. gRPC-Web não requer um protocolo específico e não especifica um ao fazer uma solicitação, a menos que seja configurado.

## <a name="additional-resources"></a>Recursos adicionais

* [gRPC para o projeto GitHub de clientes Web](https://github.com/grpc/grpc-web)
* <xref:security/cors>
