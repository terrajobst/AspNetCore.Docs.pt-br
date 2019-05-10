---
title: Solicitar recursos no ASP.NET Core
author: ardalis
description: Saiba mais sobre detalhes de implementação de servidor Web relacionados a solicitações HTTP e as respostas que são definidas em interfaces para o ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
uid: fundamentals/request-features
ms.openlocfilehash: d0f3ae521d1f314dd04cb581d9a921da4719273d
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65087028"
---
# <a name="request-features-in-aspnet-core"></a><span data-ttu-id="ffac2-103">Solicitar recursos no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ffac2-103">Request Features in ASP.NET Core</span></span>

<span data-ttu-id="ffac2-104">Por [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="ffac2-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="ffac2-105">Os detalhes de implementação de servidor Web relacionados a solicitações HTTP e respostas são definidos em interfaces.</span><span class="sxs-lookup"><span data-stu-id="ffac2-105">Web server implementation details related to HTTP requests and responses are defined in interfaces.</span></span> <span data-ttu-id="ffac2-106">Essas interfaces são usadas pelas implementações de servidor e pelo middleware para criar e modificar o pipeline de hospedagem do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ffac2-106">These interfaces are used by server implementations and middleware to create and modify the application's hosting pipeline.</span></span>

## <a name="feature-interfaces"></a><span data-ttu-id="ffac2-107">Interfaces de recurso</span><span class="sxs-lookup"><span data-stu-id="ffac2-107">Feature interfaces</span></span>

<span data-ttu-id="ffac2-108">O ASP.NET Core define várias interfaces de recurso HTTP em `Microsoft.AspNetCore.Http.Features`, que são usadas pelos servidores para identificar os recursos para os quais eles dão suporte.</span><span class="sxs-lookup"><span data-stu-id="ffac2-108">ASP.NET Core defines a number of HTTP feature interfaces in `Microsoft.AspNetCore.Http.Features` which are used by servers to identify the features they support.</span></span> <span data-ttu-id="ffac2-109">As seguintes interfaces de recurso manipulam solicitações e respostas de retorno:</span><span class="sxs-lookup"><span data-stu-id="ffac2-109">The following feature interfaces handle requests and return responses:</span></span>

<span data-ttu-id="ffac2-110">`IHttpRequestFeature` Define a estrutura de uma solicitação HTTP, incluindo o protocolo, o caminho, a cadeia de caracteres de consulta, os cabeçalhos e o corpo.</span><span class="sxs-lookup"><span data-stu-id="ffac2-110">`IHttpRequestFeature` Defines the structure of an HTTP request, including the protocol, path, query string, headers, and body.</span></span>

<span data-ttu-id="ffac2-111">`IHttpResponseFeature` Define a estrutura de uma resposta HTTP, incluindo o código de status, os cabeçalhos e o corpo da resposta.</span><span class="sxs-lookup"><span data-stu-id="ffac2-111">`IHttpResponseFeature` Defines the structure of an HTTP response, including the status code, headers, and body of the response.</span></span>

<span data-ttu-id="ffac2-112">`IHttpAuthenticationFeature` Define o suporte para identificar os usuários com base em um `ClaimsPrincipal` e especificando um manipulador de autenticação.</span><span class="sxs-lookup"><span data-stu-id="ffac2-112">`IHttpAuthenticationFeature` Defines support for identifying users based on a `ClaimsPrincipal` and specifying an authentication handler.</span></span>

<span data-ttu-id="ffac2-113">`IHttpUpgradeFeature` Define o suporte para [Upgrades de HTTP](https://tools.ietf.org/html/rfc2616.html#section-14.42), que permitem ao cliente especificar quais protocolos adicionais ele desejará usar se o servidor quiser mudar os protocolos.</span><span class="sxs-lookup"><span data-stu-id="ffac2-113">`IHttpUpgradeFeature` Defines support for [HTTP Upgrades](https://tools.ietf.org/html/rfc2616.html#section-14.42), which allow the client to specify which additional protocols it would like to use if the server wishes to switch protocols.</span></span>

<span data-ttu-id="ffac2-114">`IHttpBufferingFeature` Define métodos para desabilitar o buffer de solicitações e/ou respostas.</span><span class="sxs-lookup"><span data-stu-id="ffac2-114">`IHttpBufferingFeature` Defines methods for disabling buffering of requests and/or responses.</span></span>

<span data-ttu-id="ffac2-115">`IHttpConnectionFeature` Define propriedades para portas e endereços locais e remotos.</span><span class="sxs-lookup"><span data-stu-id="ffac2-115">`IHttpConnectionFeature` Defines properties for local and remote addresses and ports.</span></span>

<span data-ttu-id="ffac2-116">`IHttpRequestLifetimeFeature` Define o suporte para anular conexões ou detectar se uma solicitação foi encerrada prematuramente, como por uma desconexão do cliente.</span><span class="sxs-lookup"><span data-stu-id="ffac2-116">`IHttpRequestLifetimeFeature` Defines support for aborting connections, or detecting if a request has been terminated prematurely, such as by a client disconnect.</span></span>

<span data-ttu-id="ffac2-117">`IHttpSendFileFeature` Define um método para enviar arquivos de forma assíncrona.</span><span class="sxs-lookup"><span data-stu-id="ffac2-117">`IHttpSendFileFeature` Defines a method for sending files asynchronously.</span></span>

<span data-ttu-id="ffac2-118">`IHttpWebSocketFeature` Define uma API para dar suporte a soquetes da Web.</span><span class="sxs-lookup"><span data-stu-id="ffac2-118">`IHttpWebSocketFeature` Defines an API for supporting web sockets.</span></span>

<span data-ttu-id="ffac2-119">`IHttpRequestIdentifierFeature` Adiciona uma propriedade que pode ser implementada para identificar as solicitações de forma exclusiva.</span><span class="sxs-lookup"><span data-stu-id="ffac2-119">`IHttpRequestIdentifierFeature` Adds a property that can be implemented to uniquely identify requests.</span></span>

<span data-ttu-id="ffac2-120">`ISessionFeature` Define abstrações `ISessionFactory` e `ISession` para dar suporte a sessões de usuário.</span><span class="sxs-lookup"><span data-stu-id="ffac2-120">`ISessionFeature` Defines `ISessionFactory` and `ISession` abstractions for supporting user sessions.</span></span>

<span data-ttu-id="ffac2-121">`ITlsConnectionFeature` Define uma API para recuperar os certificados de cliente.</span><span class="sxs-lookup"><span data-stu-id="ffac2-121">`ITlsConnectionFeature` Defines an API for retrieving client certificates.</span></span>

<span data-ttu-id="ffac2-122">`ITlsTokenBindingFeature` Define métodos para trabalhar com parâmetros de associação de token de TLS.</span><span class="sxs-lookup"><span data-stu-id="ffac2-122">`ITlsTokenBindingFeature` Defines methods for working with TLS token binding parameters.</span></span>

> [!NOTE]
> <span data-ttu-id="ffac2-123">`ISessionFeature` não é um recurso de servidor, mas é implementado por `SessionMiddleware` (consulte [Gerenciando o estado do aplicativo](app-state.md)).</span><span class="sxs-lookup"><span data-stu-id="ffac2-123">`ISessionFeature` isn't a server feature, but is implemented by the `SessionMiddleware` (see [Managing Application State](app-state.md)).</span></span>

## <a name="feature-collections"></a><span data-ttu-id="ffac2-124">Coleções de recursos</span><span class="sxs-lookup"><span data-stu-id="ffac2-124">Feature collections</span></span>

<span data-ttu-id="ffac2-125">A propriedade `Features` de `HttpContext` fornece uma interface para obter e definir os recursos HTTP disponíveis para a solicitação atual.</span><span class="sxs-lookup"><span data-stu-id="ffac2-125">The `Features` property of `HttpContext` provides an interface for getting and setting the available HTTP features for the current request.</span></span> <span data-ttu-id="ffac2-126">Como a coleção de recursos é mutável, mesmo no contexto de uma solicitação, o middleware pode ser usado para modificar a coleção e adicionar suporte para recursos adicionais.</span><span class="sxs-lookup"><span data-stu-id="ffac2-126">Since the feature collection is mutable even within the context of a request, middleware can be used to modify the collection and add support for additional features.</span></span>

## <a name="middleware-and-request-features"></a><span data-ttu-id="ffac2-127">Middleware e recursos de solicitação</span><span class="sxs-lookup"><span data-stu-id="ffac2-127">Middleware and request features</span></span>

<span data-ttu-id="ffac2-128">Embora os servidores sejam responsáveis por criar a coleção de recursos, o middleware pode adicionar recursos a essa coleção e consumi-los.</span><span class="sxs-lookup"><span data-stu-id="ffac2-128">While servers are responsible for creating the feature collection, middleware can both add to this collection and consume features from the collection.</span></span> <span data-ttu-id="ffac2-129">Por exemplo, o `StaticFileMiddleware` acessa o recurso `IHttpSendFileFeature`.</span><span class="sxs-lookup"><span data-stu-id="ffac2-129">For example, the `StaticFileMiddleware` accesses the `IHttpSendFileFeature` feature.</span></span> <span data-ttu-id="ffac2-130">Se o recurso existir, ele será usado para enviar o arquivo estático solicitado de seu caminho físico.</span><span class="sxs-lookup"><span data-stu-id="ffac2-130">If the feature exists, it's used to send the requested static file from its physical path.</span></span> <span data-ttu-id="ffac2-131">Caso contrário, um método alternativo mais lento será usado para enviar o arquivo.</span><span class="sxs-lookup"><span data-stu-id="ffac2-131">Otherwise, a slower alternative method is used to send the file.</span></span> <span data-ttu-id="ffac2-132">Quando disponível, o `IHttpSendFileFeature` permite que o sistema operacional abra o arquivo e faça uma cópia direta de modo kernel para a placa de rede.</span><span class="sxs-lookup"><span data-stu-id="ffac2-132">When available, the `IHttpSendFileFeature` allows the operating system to open the file and perform a direct kernel mode copy to the network card.</span></span>

<span data-ttu-id="ffac2-133">Além disso, o middleware pode adicionar recursos à coleção de recursos estabelecida pelo servidor.</span><span class="sxs-lookup"><span data-stu-id="ffac2-133">Additionally, middleware can add to the feature collection established by the server.</span></span> <span data-ttu-id="ffac2-134">Os recursos existentes podem até mesmo ser substituídos pelo middleware, permitindo que o middleware aumente a funcionalidade do servidor.</span><span class="sxs-lookup"><span data-stu-id="ffac2-134">Existing features can even be replaced by middleware, allowing the middleware to augment the functionality of the server.</span></span> <span data-ttu-id="ffac2-135">Os recursos adicionados à coleção ficam disponíveis imediatamente para outro middleware ou para o próprio aplicativo subjacente posteriormente no pipeline de solicitação.</span><span class="sxs-lookup"><span data-stu-id="ffac2-135">Features added to the collection are available immediately to other middleware or the underlying application itself later in the request pipeline.</span></span>

<span data-ttu-id="ffac2-136">Combinando implementações personalizadas de servidor e melhorias específicas de middleware, o conjunto preciso de recursos exigido por um aplicativo pode ser construído.</span><span class="sxs-lookup"><span data-stu-id="ffac2-136">By combining custom server implementations and specific middleware enhancements, the precise set of features an application requires can be constructed.</span></span> <span data-ttu-id="ffac2-137">Isso permite que os recursos ausentes sejam adicionados, sem a necessidade de uma alteração no servidor, além de garantir que somente a quantidade mínima de recursos seja exposta, limitando a área da superfície de ataque e melhorando o desempenho.</span><span class="sxs-lookup"><span data-stu-id="ffac2-137">This allows missing features to be added without requiring a change in server, and ensures only the minimal amount of features are exposed, thus limiting attack surface area and improving performance.</span></span>

## <a name="summary"></a><span data-ttu-id="ffac2-138">Resumo</span><span class="sxs-lookup"><span data-stu-id="ffac2-138">Summary</span></span>

<span data-ttu-id="ffac2-139">As interfaces de recurso definem recursos HTTP específicos para os quais uma solicitação específica pode dar suporte.</span><span class="sxs-lookup"><span data-stu-id="ffac2-139">Feature interfaces define specific HTTP features that a given request may support.</span></span> <span data-ttu-id="ffac2-140">Os servidores definem coleções de recursos e o conjunto inicial de recursos compatíveis com o servidor, mas o middleware pode ser usado para aprimorar esses recursos.</span><span class="sxs-lookup"><span data-stu-id="ffac2-140">Servers define collections of features, and the initial set of features supported by that server, but middleware can be used to enhance these features.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ffac2-141">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="ffac2-141">Additional resources</span></span>

* [<span data-ttu-id="ffac2-142">Servidores</span><span class="sxs-lookup"><span data-stu-id="ffac2-142">Servers</span></span>](xref:fundamentals/servers/index)
* [<span data-ttu-id="ffac2-143">Middleware</span><span class="sxs-lookup"><span data-stu-id="ffac2-143">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="ffac2-144">OWIN (Open Web Interface para .NET)</span><span class="sxs-lookup"><span data-stu-id="ffac2-144">Open Web Interface for .NET (OWIN)</span></span>](xref:fundamentals/owin)
