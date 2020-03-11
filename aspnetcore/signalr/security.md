---
title: Considerações sobre segurança no ASP.NET Core SignalR
author: bradygaster
description: Saiba como usar a autenticação e a autorização no ASP.NET Core SignalR.
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: mvc
ms.date: 01/16/2020
no-loc:
- SignalR
uid: signalr/security
ms.openlocfilehash: f92b56132d0fa55665568416d0760430cb698f8b
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78668143"
---
# <a name="security-considerations-in-aspnet-core-opno-locsignalr"></a><span data-ttu-id="3035f-103">Considerações sobre segurança no ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="3035f-103">Security considerations in ASP.NET Core SignalR</span></span>

<span data-ttu-id="3035f-104">Por [Andrew Stanton-enfermaria](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="3035f-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="3035f-105">Este artigo fornece informações sobre como proteger SignalR.</span><span class="sxs-lookup"><span data-stu-id="3035f-105">This article provides information on securing SignalR.</span></span>

## <a name="cross-origin-resource-sharing"></a><span data-ttu-id="3035f-106">Compartilhamento de recursos entre origens</span><span class="sxs-lookup"><span data-stu-id="3035f-106">Cross-origin resource sharing</span></span>

<span data-ttu-id="3035f-107">O [CORS (compartilhamento de recursos entre origens)](https://www.w3.org/TR/cors/) pode ser usado para permitir conexões de SignalR entre origens no navegador.</span><span class="sxs-lookup"><span data-stu-id="3035f-107">[Cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/) can be used to allow cross-origin SignalR connections in the browser.</span></span> <span data-ttu-id="3035f-108">Se o código JavaScript estiver hospedado em um domínio diferente do aplicativo SignalR, o [middleware CORS](xref:security/cors) deverá ser habilitado para permitir que o JavaScript se conecte ao aplicativo SignalR.</span><span class="sxs-lookup"><span data-stu-id="3035f-108">If JavaScript code is hosted on a different domain from the SignalR app, [CORS middleware](xref:security/cors) must be enabled to allow the JavaScript to connect to the SignalR app.</span></span> <span data-ttu-id="3035f-109">Permitir solicitações entre origens somente de domínios nos quais você confia ou controla.</span><span class="sxs-lookup"><span data-stu-id="3035f-109">Allow cross-origin requests only from domains you trust or control.</span></span> <span data-ttu-id="3035f-110">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="3035f-110">For example:</span></span>

* <span data-ttu-id="3035f-111">Seu site está hospedado no `http://www.example.com`</span><span class="sxs-lookup"><span data-stu-id="3035f-111">Your site is hosted on `http://www.example.com`</span></span>
* <span data-ttu-id="3035f-112">Seu aplicativo SignalR está hospedado em `http://signalr.example.com`</span><span class="sxs-lookup"><span data-stu-id="3035f-112">Your SignalR app is hosted on `http://signalr.example.com`</span></span>

<span data-ttu-id="3035f-113">O CORS deve ser configurado no aplicativo SignalR para permitir somente a origem `www.example.com`.</span><span class="sxs-lookup"><span data-stu-id="3035f-113">CORS should be configured in the SignalR app to only allow the origin `www.example.com`.</span></span>

<span data-ttu-id="3035f-114">Para obter mais informações sobre como configurar o CORS, consulte [habilitar solicitações entre origens (CORS)](xref:security/cors).</span><span class="sxs-lookup"><span data-stu-id="3035f-114">For more information on configuring CORS, see [Enable Cross-Origin Requests (CORS)](xref:security/cors).</span></span> SignalR<span data-ttu-id="3035f-115"> **requer** as seguintes políticas de CORS:</span><span class="sxs-lookup"><span data-stu-id="3035f-115"> **requires** the following CORS policies:</span></span>

* <span data-ttu-id="3035f-116">Permitir as origens esperadas específicas.</span><span class="sxs-lookup"><span data-stu-id="3035f-116">Allow the specific expected origins.</span></span> <span data-ttu-id="3035f-117">Permitir qualquer origem é possível, mas **não** é seguro ou recomendado.</span><span class="sxs-lookup"><span data-stu-id="3035f-117">Allowing any origin is possible but is **not** secure or recommended.</span></span>
* <span data-ttu-id="3035f-118">Os métodos HTTP `GET` e `POST` devem ser permitidos.</span><span class="sxs-lookup"><span data-stu-id="3035f-118">HTTP methods `GET` and `POST` must be allowed.</span></span>
* <span data-ttu-id="3035f-119">As credenciais devem ser permitidas para que as sessões adesivas baseadas em cookie funcionem corretamente.</span><span class="sxs-lookup"><span data-stu-id="3035f-119">Credentials must be allowed in order for cookie-based sticky sessions to work correctly.</span></span> <span data-ttu-id="3035f-120">Eles devem ser habilitados mesmo quando a autenticação não é usada.</span><span class="sxs-lookup"><span data-stu-id="3035f-120">They must be enabled even when authentication isn't used.</span></span>

<!--
::: moniker range=">= aspnetcore-5.0"  // Moniker here just to make sure this doesn't get missed in the 5.0 version update.
However, in 5.0 we have provided an option in the TypeScript client to not use credentials.
The not to use credentials option should only be used when you know 100% that credentials like Cookies are not needed in your app (cookies are used by azure app service when using multiple servers)

For more info, see https://github.com/dotnet/AspNetCore.Docs/issues/16003
.-->

<span data-ttu-id="3035f-121">Por exemplo, a seguinte política de CORS permite que um cliente de navegador SignalR hospedado em `https://example.com` acesse o aplicativo SignalR hospedado no `https://signalr.example.com`:</span><span class="sxs-lookup"><span data-stu-id="3035f-121">For example, the following CORS policy allows a SignalR browser client hosted on `https://example.com` to access the SignalR app hosted on `https://signalr.example.com`:</span></span>

::: moniker range=">= aspnetcore-3.0"

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    // ... other middleware ...

    // Make sure the CORS middleware is ahead of SignalR.
    app.UseCors(builder =>
    {
        builder.WithOrigins("https://example.com")
            .AllowAnyHeader()
            .WithMethods("GET", "POST")
            .AllowCredentials();
    });

    // ... other middleware ...
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chatHub");
    });

    // ... other middleware ...
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[Main](security/sample/Startup.cs?name=snippet1)]

::: moniker-end

> [!NOTE]
> SignalR<span data-ttu-id="3035f-122"> não é compatível com o recurso interno CORS no serviço Azure App.</span><span class="sxs-lookup"><span data-stu-id="3035f-122"> is not compatible with the built-in CORS feature in Azure App Service.</span></span>

## <a name="websocket-origin-restriction"></a><span data-ttu-id="3035f-123">Restrição de origem do WebSocket</span><span class="sxs-lookup"><span data-stu-id="3035f-123">WebSocket Origin Restriction</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="3035f-124">As proteções fornecidas pelo CORS não se aplicam ao WebSockets.</span><span class="sxs-lookup"><span data-stu-id="3035f-124">The protections provided by CORS don't apply to WebSockets.</span></span> <span data-ttu-id="3035f-125">Para a restrição de origem em WebSockets, leia a [restrição de origem de WebSockets](xref:fundamentals/websockets#websocket-origin-restriction).</span><span class="sxs-lookup"><span data-stu-id="3035f-125">For origin restriction on WebSockets, read [WebSockets origin restriction](xref:fundamentals/websockets#websocket-origin-restriction).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="3035f-126">As proteções fornecidas pelo CORS não se aplicam ao WebSockets.</span><span class="sxs-lookup"><span data-stu-id="3035f-126">The protections provided by CORS don't apply to WebSockets.</span></span> <span data-ttu-id="3035f-127">Navegadores **não**:</span><span class="sxs-lookup"><span data-stu-id="3035f-127">Browsers do **not**:</span></span>

* <span data-ttu-id="3035f-128">Executam solicitações de simulação de CORS.</span><span class="sxs-lookup"><span data-stu-id="3035f-128">Perform CORS pre-flight requests.</span></span>
* <span data-ttu-id="3035f-129">Respeitam as restrições especificadas em cabeçalhos `Access-Control` ao fazer solicitações de WebSocket.</span><span class="sxs-lookup"><span data-stu-id="3035f-129">Respect the restrictions specified in `Access-Control` headers when making WebSocket requests.</span></span>

<span data-ttu-id="3035f-130">No entanto, os navegadores enviam o cabeçalho `Origin` ao emitir solicitações de WebSocket.</span><span class="sxs-lookup"><span data-stu-id="3035f-130">However, browsers do send the `Origin` header when issuing WebSocket requests.</span></span> <span data-ttu-id="3035f-131">Os aplicativos devem ser configurados para validar esses cabeçalhos e garantir que apenas WebSockets provenientes de origens esperadas sejam permitidos.</span><span class="sxs-lookup"><span data-stu-id="3035f-131">Applications should be configured to validate these headers to ensure that only WebSockets coming from the expected origins are allowed.</span></span>

<span data-ttu-id="3035f-132">No ASP.NET Core 2,1 e posterior, a validação de cabeçalho pode ser obtida usando um middleware personalizado colocado **antes `UseSignalR`e middleware de autenticação** no `Configure`:</span><span class="sxs-lookup"><span data-stu-id="3035f-132">In ASP.NET Core 2.1 and later, header validation can be achieved using a custom middleware placed **before `UseSignalR`, and authentication middleware** in `Configure`:</span></span>

[!code-csharp[Main](security/sample/Startup.cs?name=snippet2)]

> [!NOTE]
> <span data-ttu-id="3035f-133">O cabeçalho `Origin` é controlado pelo cliente e, como o cabeçalho `Referer`, pode ser falsificado.</span><span class="sxs-lookup"><span data-stu-id="3035f-133">The `Origin` header is controlled by the client and, like the `Referer` header, can be faked.</span></span> <span data-ttu-id="3035f-134">Esses cabeçalhos **não** devem ser usados como um mecanismo de autenticação.</span><span class="sxs-lookup"><span data-stu-id="3035f-134">These headers should **not** be used as an authentication mechanism.</span></span>

::: moniker-end

## <a name="connectionid"></a><span data-ttu-id="3035f-135">ConnectionId</span><span class="sxs-lookup"><span data-stu-id="3035f-135">ConnectionId</span></span>

<span data-ttu-id="3035f-136">A exposição de `ConnectionId` pode levar a uma representação mal-intencionada se a versão do cliente ou do servidor SignalR for ASP.NET Core 2,2 ou anterior.</span><span class="sxs-lookup"><span data-stu-id="3035f-136">Exposing `ConnectionId` can lead to malicious impersonation if the SignalR server or client version is ASP.NET Core 2.2 or earlier.</span></span> <span data-ttu-id="3035f-137">Se o servidor SignalR e a versão do cliente forem ASP.NET Core 3,0 ou posterior, o `ConnectionToken` em vez do `ConnectionId` deverá ser mantido em segredo.</span><span class="sxs-lookup"><span data-stu-id="3035f-137">If the SignalR server and client version are ASP.NET Core 3.0 or later, the `ConnectionToken` rather than the `ConnectionId` must be kept secret.</span></span> <span data-ttu-id="3035f-138">A `ConnectionToken` é intencionalmente não exposta em nenhuma API.</span><span class="sxs-lookup"><span data-stu-id="3035f-138">The `ConnectionToken` is purposely not exposed in any API.</span></span>  <span data-ttu-id="3035f-139">Pode ser difícil garantir que os clientes SignalR mais antigos não se conectem ao servidor, portanto, mesmo que a versão do SignalR Server seja ASP.NET Core 3,0 ou posterior, a `ConnectionId` não deve ser exposta.</span><span class="sxs-lookup"><span data-stu-id="3035f-139">It can be difficult to ensure that older SignalR clients aren't connecting to the server, so even if your SignalR server version is ASP.NET Core 3.0 or later, the `ConnectionId` shouldn't be exposed.</span></span>

## <a name="access-token-logging"></a><span data-ttu-id="3035f-140">Log de token de acesso</span><span class="sxs-lookup"><span data-stu-id="3035f-140">Access token logging</span></span>

<span data-ttu-id="3035f-141">Ao usar Websockets ou eventos enviados pelo servidor, o cliente de navegador envia o token de acesso na cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="3035f-141">When using WebSockets or Server-Sent Events, the browser client sends the access token in the query string.</span></span> <span data-ttu-id="3035f-142">O recebimento do token de acesso por meio da cadeia de caracteres de consulta é geralmente seguro ao usar o cabeçalho de `Authorization` padrão.</span><span class="sxs-lookup"><span data-stu-id="3035f-142">Receiving the access token via query string is generally secure as using the standard `Authorization` header.</span></span> <span data-ttu-id="3035f-143">Sempre use HTTPS para garantir uma conexão segura de ponta a ponta entre o cliente e o servidor.</span><span class="sxs-lookup"><span data-stu-id="3035f-143">Always use HTTPS to ensure a secure end-to-end connection between the client and the server.</span></span> <span data-ttu-id="3035f-144">Muitos servidores Web registram a URL para cada solicitação, incluindo a cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="3035f-144">Many web servers log the URL for each request, including the query string.</span></span> <span data-ttu-id="3035f-145">O registro em log das URLs pode registrar o token de acesso.</span><span class="sxs-lookup"><span data-stu-id="3035f-145">Logging the URLs may log the access token.</span></span> <span data-ttu-id="3035f-146">O ASP.NET Core registra em log a URL para cada solicitação por padrão, o que incluirá a cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="3035f-146">ASP.NET Core logs the URL for each request by default, which will include the query string.</span></span> <span data-ttu-id="3035f-147">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="3035f-147">For example:</span></span>

```
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:5000/myhub?access_token=1234
```

<span data-ttu-id="3035f-148">Se você tiver dúvidas sobre como registrar esses dados nos logs do servidor, poderá desabilitar esse log totalmente Configurando o agente de `Microsoft.AspNetCore.Hosting` para o nível de `Warning` ou acima (essas mensagens são gravadas no nível `Info`).</span><span class="sxs-lookup"><span data-stu-id="3035f-148">If you have concerns about logging this data with your server logs, you can disable this logging entirely by configuring the `Microsoft.AspNetCore.Hosting` logger to the `Warning` level or above (these messages are written at `Info` level).</span></span> <span data-ttu-id="3035f-149">Para obter mais informações, consulte [filtragem de log](xref:fundamentals/logging/index#log-filtering) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="3035f-149">For more information, see [Log Filtering](xref:fundamentals/logging/index#log-filtering) for more information.</span></span> <span data-ttu-id="3035f-150">Se você ainda quiser registrar determinadas informações de solicitação, poderá [escrever um middleware](xref:fundamentals/middleware/write) para registrar os dados necessários e filtrar o `access_token` valor da cadeia de caracteres de consulta (se houver).</span><span class="sxs-lookup"><span data-stu-id="3035f-150">If you still want to log certain request information, you can [write a middleware](xref:fundamentals/middleware/write) to log the data you require and filter out the `access_token` query string value (if present).</span></span>

## <a name="exceptions"></a><span data-ttu-id="3035f-151">Exceções</span><span class="sxs-lookup"><span data-stu-id="3035f-151">Exceptions</span></span>

<span data-ttu-id="3035f-152">As mensagens de exceção geralmente são consideradas dados confidenciais que não devem ser revelados a um cliente.</span><span class="sxs-lookup"><span data-stu-id="3035f-152">Exception messages are generally considered sensitive data that shouldn't be revealed to a client.</span></span> <span data-ttu-id="3035f-153">Por padrão, o SignalR não envia os detalhes de uma exceção gerada por um método de Hub para o cliente.</span><span class="sxs-lookup"><span data-stu-id="3035f-153">By default, SignalR doesn't send the details of an exception thrown by a hub method to the client.</span></span> <span data-ttu-id="3035f-154">Em vez disso, o cliente recebe uma mensagem genérica indicando que ocorreu um erro.</span><span class="sxs-lookup"><span data-stu-id="3035f-154">Instead, the client receives a generic message indicating an error occurred.</span></span> <span data-ttu-id="3035f-155">A entrega de mensagem de exceção ao cliente pode ser substituída (por exemplo, em desenvolvimento ou teste) com [EnableDetailedErrors](xref:signalr/configuration#configure-server-options).</span><span class="sxs-lookup"><span data-stu-id="3035f-155">Exception message delivery to the client can be overridden (for example in development or test) with [EnableDetailedErrors](xref:signalr/configuration#configure-server-options).</span></span> <span data-ttu-id="3035f-156">As mensagens de exceção não devem ser expostas ao cliente em aplicativos de produção.</span><span class="sxs-lookup"><span data-stu-id="3035f-156">Exception messages should not be exposed to the client in production apps.</span></span>

## <a name="buffer-management"></a><span data-ttu-id="3035f-157">Gerenciamento de buffer</span><span class="sxs-lookup"><span data-stu-id="3035f-157">Buffer management</span></span>

<span data-ttu-id="3035f-158">o SignalR usa buffers por conexão para gerenciar mensagens de entrada e saída.</span><span class="sxs-lookup"><span data-stu-id="3035f-158">SignalR uses per-connection buffers to manage incoming and outgoing messages.</span></span> <span data-ttu-id="3035f-159">Por padrão, SignalR limita esses buffers a 32 KB.</span><span class="sxs-lookup"><span data-stu-id="3035f-159">By default, SignalR limits these buffers to 32 KB.</span></span> <span data-ttu-id="3035f-160">A maior mensagem que um cliente ou servidor pode enviar é 32 KB.</span><span class="sxs-lookup"><span data-stu-id="3035f-160">The largest message a client or server can send is 32 KB.</span></span> <span data-ttu-id="3035f-161">A memória máxima consumida por uma conexão para mensagens é de 32 KB.</span><span class="sxs-lookup"><span data-stu-id="3035f-161">The maximum memory consumed by a connection for messages is 32 KB.</span></span> <span data-ttu-id="3035f-162">Se suas mensagens forem sempre menores que 32 KB, você poderá reduzir o limite, que:</span><span class="sxs-lookup"><span data-stu-id="3035f-162">If your messages are always smaller than 32 KB, you can reduce the limit, which:</span></span>

* <span data-ttu-id="3035f-163">Impede que um cliente possa enviar uma mensagem maior.</span><span class="sxs-lookup"><span data-stu-id="3035f-163">Prevents a client from being able to send a larger message.</span></span>
* <span data-ttu-id="3035f-164">O servidor nunca precisará alocar buffers grandes para aceitar mensagens.</span><span class="sxs-lookup"><span data-stu-id="3035f-164">The server will never need to allocate large buffers to accept messages.</span></span>

<span data-ttu-id="3035f-165">Se suas mensagens forem maiores que 32 KB, você poderá aumentar o limite.</span><span class="sxs-lookup"><span data-stu-id="3035f-165">If your messages are larger than 32 KB, you can increase the limit.</span></span> <span data-ttu-id="3035f-166">Aumentar esse limite significa:</span><span class="sxs-lookup"><span data-stu-id="3035f-166">Increasing this limit means:</span></span>

* <span data-ttu-id="3035f-167">O cliente pode fazer com que o servidor aloque buffers de memória grandes.</span><span class="sxs-lookup"><span data-stu-id="3035f-167">The client can cause the server to allocate large memory buffers.</span></span>
* <span data-ttu-id="3035f-168">A alocação de servidor de buffers grandes pode reduzir o número de conexões simultâneas.</span><span class="sxs-lookup"><span data-stu-id="3035f-168">Server allocation of large buffers may reduce the number of concurrent connections.</span></span>

<span data-ttu-id="3035f-169">Há limites para mensagens de entrada e saída, ambas podem ser configuradas no objeto [HttpConnectionDispatcherOptions](xref:signalr/configuration#configure-server-options) configurado no `MapHub`:</span><span class="sxs-lookup"><span data-stu-id="3035f-169">There are limits for incoming and outgoing messages, both can be configured on the [HttpConnectionDispatcherOptions](xref:signalr/configuration#configure-server-options) object configured in `MapHub`:</span></span>

* <span data-ttu-id="3035f-170">`ApplicationMaxBufferSize` representa o número máximo de bytes do cliente que o servidor armazena em buffer.</span><span class="sxs-lookup"><span data-stu-id="3035f-170">`ApplicationMaxBufferSize` represents the maximum number of bytes from the client that the server buffers.</span></span> <span data-ttu-id="3035f-171">Se o cliente tentar enviar uma mensagem maior que esse limite, a conexão poderá ser fechada.</span><span class="sxs-lookup"><span data-stu-id="3035f-171">If the client attempts to send a message larger than this limit, the connection may be closed.</span></span>
* <span data-ttu-id="3035f-172">`TransportMaxBufferSize` representa o número máximo de bytes que o servidor pode enviar.</span><span class="sxs-lookup"><span data-stu-id="3035f-172">`TransportMaxBufferSize` represents the maximum number of bytes the server can send.</span></span> <span data-ttu-id="3035f-173">Se o servidor tentar enviar uma mensagem (incluindo valores de retorno dos métodos de Hub) maior que esse limite, uma exceção será lançada.</span><span class="sxs-lookup"><span data-stu-id="3035f-173">If the server attempts to send a message (including return values from hub methods) larger than this limit, an exception will be thrown.</span></span>

<span data-ttu-id="3035f-174">Definir o limite para `0` desabilita o limite.</span><span class="sxs-lookup"><span data-stu-id="3035f-174">Setting the limit to `0` disables the limit.</span></span> <span data-ttu-id="3035f-175">A remoção do limite permite que um cliente envie uma mensagem de qualquer tamanho.</span><span class="sxs-lookup"><span data-stu-id="3035f-175">Removing the limit allows a client to send a message of any size.</span></span> <span data-ttu-id="3035f-176">Clientes mal-intencionados que enviam mensagens grandes podem causar o excesso de memória a ser alocada.</span><span class="sxs-lookup"><span data-stu-id="3035f-176">Malicious clients sending large messages can cause excess memory to be allocated.</span></span> <span data-ttu-id="3035f-177">O uso excessivo de memória pode reduzir significativamente o número de conexões simultâneas.</span><span class="sxs-lookup"><span data-stu-id="3035f-177">Excess memory usage can significantly reduce the number of concurrent connections.</span></span>
