---
title: Novidades do ASP.NET Core 2.2
author: tdykstra
description: Conheça os novos recursos do ASP.NET Core 2.2.
ms.author: tdykstra
ms.custom: mvc
ms.date: 12/18/2018
uid: aspnetcore-2.2
ms.openlocfilehash: cdc761b645b91777bdf6084c3ad4659fcea55039
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64883201"
---
# <a name="whats-new-in-aspnet-core-22"></a>Novidades do ASP.NET Core 2.2

Este artigo destaca as alterações mais significativas no ASP.NET Core 2.2, com links para a documentação relevante.

## <a name="openapi-analyzers--conventions"></a>Analisadores e convenções do OpenAPI

O Open API (anteriormente conhecido como Swagger) é uma especificação independente de linguagem para descrever APIs REST. O ecossistema do Open API tem ferramentas que permitem descobrir, testar e produzir o código do cliente usando a especificação. O suporte para gerar e visualizar documentos do OpenAPI no ASP.NET Core MVC é fornecido por meio de projetos controlados pela comunidade, como [NSwag](https://github.com/RSuter/NSwag) e [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore). O ASP.NET Core 2.2 fornece ferramentas e experiências de tempo de execução aprimoradas para a criação de documentos do OpenAPI.

Para obter mais informações, consulte os seguintes recursos:

* <xref:web-api/advanced/analyzers>
* <xref:web-api/advanced/conventions>
* [ASP.NET Core 2.2.0-preview1: analisadores e convenções do OpenAPI](https://blogs.msdn.microsoft.com/webdev/2018/08/23/asp-net-core-2-20-preview1-open-api-analyzers-conventions/)

## <a name="problem-details-support"></a>Suporte de detalhes do problema

O ASP.NET Core 2.1 introduziu o `ProblemDetails`, com base na especificação [RFC 7807](https://tools.ietf.org/html/rfc7807) para transmitir os detalhes de um erro com uma Resposta HTTP. No 2.2, `ProblemDetails` é a resposta padrão para códigos de erro do cliente em controladores atribuídos com `ApiControllerAttribute`. Um `IActionResult` que retorna um código de status de erro do cliente (4xx) retorna um corpo `ProblemDetails`. O resultado também inclui uma ID de correlação que pode ser usada para correlacionar o erro usando logs de solicitação. Para erros do cliente, `ProducesResponseType` usa como padrão `ProblemDetails` como o tipo de resposta. Isso é documentado na saída do OpenAPI/Swagger gerada com o NSwag ou o Swashbuckle.AspNetCore.

## <a name="endpoint-routing"></a>Roteamento de ponto de extremidade

O ASP.NET Core 2.2 usa um novo sistema de *roteamento de ponto de extremidade* para expedição aprimorada de solicitações. As alterações incluem novos membros da API de geração de link e transformadores de parâmetro de rota.

Para obter mais informações, consulte os seguintes recursos:

* [Roteamento de ponto de extremidade no 2.2](https://blogs.msdn.microsoft.com/webdev/2018/08/27/asp-net-core-2-2-0-preview1-endpoint-routing/)
* [Transformadores de parâmetro de rota](https://www.hanselman.com/blog/ASPNETCore22ParameterTransformersForCleanURLGenerationAndSlugsInRazorPagesOrMVC.aspx) (confira a seção **Roteamento**)
* [Diferenças entre o roteamento baseado em IRouter e em ponto de extremidade](xref:fundamentals/routing?view=aspnetcore-2.2#differences-from-earlier-versions-of-routing)

## <a name="health-checks"></a>Verificações de integridade

Um novo serviço de verificações de integridade facilita o uso do ASP.NET Core em ambientes que exigem verificações de integridade, como o Kubernetes. As verificações de integridade incluem middleware e um conjunto de bibliotecas que definem um serviço e uma abstração `IHealthCheck`.

As verificações de integridade são usadas por um orquestrador de contêineres ou um balanceador de carga para determinar rapidamente se um sistema está respondendo às solicitações normalmente. Um orquestrador de contêineres pode responder a uma verificação de integridade com falha interrompendo uma implantação sem interrupção ou reiniciando um contêiner. Um balanceador de carga pode responder a uma verificação de integridade encaminhando o tráfego para fora da instância com falha do serviço.

As verificações de integridade são expostas por um aplicativo como um ponto de extremidade HTTP usado por sistemas de monitoramento. As verificações de integridade podem ser configuradas para uma variedade de cenários de monitoramento em tempo real e sistemas de monitoramento. O serviço de verificações de integridade é integrado ao [projeto BeatPulse](https://github.com/Xabaril/BeatPulse). que facilita a adição de verificações para dezenas de sistemas e dependências populares.

Para obter mais informações, confira [Verificações de integridade no ASP.NET Core](xref:host-and-deploy/health-checks).

## <a name="http2-in-kestrel"></a>HTTP/2 no Kestrel

O ASP.NET Core 2.2 adiciona suporte ao HTTP/2. 

O HTTP/2 é uma revisão principal do protocolo HTTP. Alguns dos recursos importantes do HTTP/2 são o suporte à compactação de cabeçalho e fluxos totalmente multiplexados em uma única conexão. Embora o HTTP/2 preserve a semântica do HTTP (cabeçalhos HTTP, métodos etc.), ele é uma alteração da falha do HTTP/1.x com relação a como esses dados são estruturados e enviados pela conexão.

Como consequência dessa alteração no enquadramento, os servidores e os clientes precisam negociar a versão de protocolo usada. O recurso ALPN (Negociação de Protocolo da Camada de Aplicativo) é uma extensão TLS com a qual o servidor e o cliente podem negociar a versão de protocolo usada como parte do handshake TLS. Embora seja possível ter um conhecimento prévio entre o servidor e o cliente sobre o protocolo, todos os principais navegadores dão suporte ALPN como a única maneira de estabelecer uma conexão HTTP/2.

Para obter mais informações, confira [Suporte ao HTTP/2](xref:fundamentals/servers/index?view=aspnetcore-2.2#http2-support).

## <a name="kestrel-configuration"></a>Configuração do Kestrel

Em versões anteriores do ASP.NET Core, as opções do Kestrel são configuradas por meio da chamada a `UseKestrel`. No 2.2, as opções do Kestrel são configuradas por meio da chamada a `ConfigureKestrel` no construtor do host. Essa alteração resolve um problema com a ordem dos registros `IServer` para a hospedagem em processo. Para obter mais informações, consulte os seguintes recursos:

* [Atenuar conflitos do UseIIS](https://github.com/aspnet/KestrelHttpServer/issues/2760)
* [Configurar opções do servidor Kestrel com ConfigureKestrel](xref:fundamentals/servers/kestrel?view=aspnetcore-2.2#how-to-use-kestrel-in-aspnet-core-apps)

## <a name="iis-in-process-hosting"></a>Hospedagem em processo do IIS

Em versões anteriores do ASP.NET Core, o IIS funciona como um proxy reverso. No 2.2, o Módulo do ASP.NET Core pode inicializar o CoreCLR e hospedar um aplicativo dentro do processo de trabalho do IIS (*w3wp.exe*). A hospedagem em processo fornece ganhos de desempenho e diagnóstico durante a execução com o IIS.

Para obter mais informações, confira [Hospedagem em processo para IIS](xref:host-and-deploy/aspnet-core-module?view=aspnetcore-2.2#in-process-hosting-model).

## <a name="signalr-java-client"></a>Cliente Java do SignalR

O ASP.NET Core 2.2 introduz um Cliente Java para o SignalR. Esse cliente dá suporte à conexão a um Servidor do ASP.NET Core SignalR por meio do código Java, incluindo aplicativos Android.

Para obter mais informações, confira [Cliente Java do ASP.NET Core SignalR](https://docs.microsoft.com/aspnet/core/signalr/java-client?view=aspnetcore-2.2).

## <a name="cors-improvements"></a>Melhorias do CORS

Em versões anteriores do ASP.NET Core, o Middleware do CORS permite o envio dos cabeçalhos `Accept`, `Accept-Language`, `Content-Language` e `Origin`, independentemente dos valores configurados em `CorsPolicy.Headers`. No 2.2, uma correspondência de política do Middleware do CORS só é possível quando os cabeçalhos enviados em `Access-Control-Request-Headers` corresponder exatamente aos cabeçalhos indicados em `WithHeaders`.

Para obter mais informações, confira [Middleware do CORS](xref:security/cors?view=aspnetcore-2.2#set-the-allowed-request-headers).

## <a name="response-compression"></a>Compactação de resposta

O ASP.NET Core 2.2 pode compactar respostas com o [formato de compactação Brotli](https://tools.ietf.org/html/rfc7932).

Para obter mais informações, confira [O middleware de compactação de resposta dá suporte à compactação Brotli](xref:performance/response-compression?view=aspnetcore-2.2#brotli-compression-provider).

## <a name="project-templates"></a>Modelos de projeto

Os modelos de projeto Web ASP.NET Core foram atualizados para o [Bootstrap 4](https://getbootstrap.com/docs/4.1/migration/) e o [Angular 6](https://blog.angular.io/version-6-of-angular-now-available-cc56b0efa7a4). A nova aparência é visualmente mais simples e facilita a visualização das estruturas importantes do aplicativo.

![Página Inicial ou de Índice](~/tutorials/razor-pages/razor-pages-start/_static/home2.2.png)

## <a name="validation-performance"></a>Desempenho de validação

O sistema de validação do MVC foi projetado para ser extensível e flexível, permitindo que você determine a cada solicitação quais validadores se aplicam a determinado modelo. Isso é ótimo para a criação de provedores de validação complexa. No entanto, na maioria dos casos, um aplicativo usa apenas os validadores internos e não exige essa flexibilidade extra. Validadores internos incluem DataAnnotations como [Required] e [StringLength], e `IValidatableObject`.

No ASP.NET Core 2.2, o MVC poderá causar um curto-circuito na validação se ele determinar que um grafo de modelo fornecido não exige validação. Ignorar a validação resulta em melhorias significativas ao validar modelos que não podem ou não têm nenhum validador. Isso inclui objetos, como coleções de primitivos (como `byte[]`, `string[]`, `Dictionary<string, string>`), ou grafos de objeto complexo sem muitos validadores.

## <a name="http-client-performance"></a>Desempenho do Cliente HTTP

No ASP.NET Core 2.2, o desempenho do `SocketsHttpHandler` foi aprimorado, reduzindo a contenção de bloqueio do pool de conexão. Para aplicativos que fazem muitas solicitações HTTP de saída, como algumas arquiteturas de microsserviços, a taxa de transferência foi aprimorada. Sob carga, a taxa de transferência do `HttpClient` pode ser melhorada em até 60% no Linux e 20% no Windows.

Para obter mais informações, confira [a solicitação de pull que fez essa melhoria](https://github.com/dotnet/corefx/pull/32568).

## <a name="additional-information"></a>Informações adicionais

Para obter a lista completa de alterações, confira as [Notas sobre a versão do ASP.NET Core 2.2](https://github.com/aspnet/Home/releases/tag/2.2.0).
