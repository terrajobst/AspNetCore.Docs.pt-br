---
title: Considerações de segurança no SignalR do ASP.NET Core
author: bradygaster
description: Saiba como usar a autenticação e autorização no SignalR do ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: mvc
ms.date: 11/06/2018
uid: signalr/security
ms.openlocfilehash: 52cfac6be8e61572acdf0b19dab574b607314d97
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836058"
---
# <a name="security-considerations-in-aspnet-core-signalr"></a><span data-ttu-id="797be-103">Considerações de segurança no SignalR do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="797be-103">Security considerations in ASP.NET Core SignalR</span></span>

<span data-ttu-id="797be-104">Por [Andrew Stanton-Nurse](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="797be-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="797be-105">Este artigo fornece informações sobre como proteger o SignalR.</span><span class="sxs-lookup"><span data-stu-id="797be-105">This article provides information on securing SignalR.</span></span>

## <a name="cross-origin-resource-sharing"></a><span data-ttu-id="797be-106">Compartilhamento de recursos entre origens</span><span class="sxs-lookup"><span data-stu-id="797be-106">Cross-origin resource sharing</span></span>

<span data-ttu-id="797be-107">[Recursos entre origens (CORS) compartilhamento](https://www.w3.org/TR/cors/) pode ser usado para permitir conexões do SignalR entre origens no navegador.</span><span class="sxs-lookup"><span data-stu-id="797be-107">[Cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/) can be used to allow cross-origin SignalR connections in the browser.</span></span> <span data-ttu-id="797be-108">Se o código JavaScript é hospedado em um domínio diferente do aplicativo do SignalR, [middleware CORS](xref:security/cors) deve estar habilitado para permitir que o JavaScript para se conectar ao aplicativo SignalR.</span><span class="sxs-lookup"><span data-stu-id="797be-108">If JavaScript code is hosted on a different domain from the SignalR app, [CORS middleware](xref:security/cors) must be enabled to allow the JavaScript to connect to the SignalR app.</span></span> <span data-ttu-id="797be-109">Permitir solicitações entre origens de domínios que confiáveis ou controle.</span><span class="sxs-lookup"><span data-stu-id="797be-109">Allow cross-origin requests only from domains you trust or control.</span></span> <span data-ttu-id="797be-110">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="797be-110">For example:</span></span>

* <span data-ttu-id="797be-111">O site é hospedado em `http://www.example.com`</span><span class="sxs-lookup"><span data-stu-id="797be-111">Your site is hosted on `http://www.example.com`</span></span>
* <span data-ttu-id="797be-112">Seu aplicativo SignalR é hospedado em `http://signalr.example.com`</span><span class="sxs-lookup"><span data-stu-id="797be-112">Your SignalR app is hosted on `http://signalr.example.com`</span></span>

<span data-ttu-id="797be-113">CORS devem ser configurado no aplicativo do SignalR para permitir somente a origem `www.example.com`.</span><span class="sxs-lookup"><span data-stu-id="797be-113">CORS should be configured in the SignalR app to only allow the origin `www.example.com`.</span></span>

<span data-ttu-id="797be-114">Para obter mais informações sobre como configurar o CORS, consulte [habilitar solicitações de entre origens (CORS)](xref:security/cors).</span><span class="sxs-lookup"><span data-stu-id="797be-114">For more information on configuring CORS, see [Enable Cross-Origin Requests (CORS)](xref:security/cors).</span></span> <span data-ttu-id="797be-115">O SignalR **requer** as seguintes políticas CORS:</span><span class="sxs-lookup"><span data-stu-id="797be-115">SignalR **requires** the following CORS policies:</span></span>

* <span data-ttu-id="797be-116">Permitir que as origens esperadas específicas.</span><span class="sxs-lookup"><span data-stu-id="797be-116">Allow the specific expected origins.</span></span> <span data-ttu-id="797be-117">Permitir qualquer origem é possível, mas está **não** segura ou recomendada.</span><span class="sxs-lookup"><span data-stu-id="797be-117">Allowing any origin is possible but is **not** secure or recommended.</span></span>
* <span data-ttu-id="797be-118">Métodos HTTP `GET` e `POST` devem ser permitidos.</span><span class="sxs-lookup"><span data-stu-id="797be-118">HTTP methods `GET` and `POST` must be allowed.</span></span>
* <span data-ttu-id="797be-119">Credenciais devem ser habilitadas, mesmo quando a autenticação não é usada.</span><span class="sxs-lookup"><span data-stu-id="797be-119">Credentials must be enabled, even when authentication is not used.</span></span>

<span data-ttu-id="797be-120">Por exemplo, a seguinte política CORS permite que um cliente de navegador do SignalR hospedado no `https://example.com` para acessar o aplicativo de SignalR hospedado em `https://signalr.example.com`:</span><span class="sxs-lookup"><span data-stu-id="797be-120">For example, the following CORS policy allows a SignalR browser client hosted on `https://example.com` to access the SignalR app hosted on `https://signalr.example.com`:</span></span>

[!code-csharp[Main](security/sample/Startup.cs?name=snippet1)]

> [!NOTE]
> <span data-ttu-id="797be-121">O SignalR não é compatível com o recurso interno de CORS no serviço de aplicativo do Azure.</span><span class="sxs-lookup"><span data-stu-id="797be-121">SignalR is not compatible with the built-in CORS feature in Azure App Service.</span></span>

## <a name="websocket-origin-restriction"></a><span data-ttu-id="797be-122">WebSocket Origin Restriction</span><span class="sxs-lookup"><span data-stu-id="797be-122">WebSocket Origin Restriction</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="797be-123">As proteções fornecidas pelo CORS não se aplicam ao WebSockets.</span><span class="sxs-lookup"><span data-stu-id="797be-123">The protections provided by CORS don't apply to WebSockets.</span></span> <span data-ttu-id="797be-124">Para a restrição de origem sobre WebSockets, leia [restrição de origem de WebSockets](xref:fundamentals/websockets#websocket-origin-restriction).</span><span class="sxs-lookup"><span data-stu-id="797be-124">For origin restriction on WebSockets, read [WebSockets origin restriction](xref:fundamentals/websockets#websocket-origin-restriction).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="797be-125">As proteções fornecidas pelo CORS não se aplicam ao WebSockets.</span><span class="sxs-lookup"><span data-stu-id="797be-125">The protections provided by CORS don't apply to WebSockets.</span></span> <span data-ttu-id="797be-126">Navegadores **não**:</span><span class="sxs-lookup"><span data-stu-id="797be-126">Browsers do **not**:</span></span>

* <span data-ttu-id="797be-127">Executam solicitações de simulação de CORS.</span><span class="sxs-lookup"><span data-stu-id="797be-127">Perform CORS pre-flight requests.</span></span>
* <span data-ttu-id="797be-128">Respeitam as restrições especificadas em cabeçalhos `Access-Control` ao fazer solicitações de WebSocket.</span><span class="sxs-lookup"><span data-stu-id="797be-128">Respect the restrictions specified in `Access-Control` headers when making WebSocket requests.</span></span>

<span data-ttu-id="797be-129">No entanto, os navegadores enviam o cabeçalho `Origin` ao emitir solicitações de WebSocket.</span><span class="sxs-lookup"><span data-stu-id="797be-129">However, browsers do send the `Origin` header when issuing WebSocket requests.</span></span> <span data-ttu-id="797be-130">Os aplicativos devem ser configurados para validar esses cabeçalhos e garantir que apenas WebSockets provenientes de origens esperadas sejam permitidos.</span><span class="sxs-lookup"><span data-stu-id="797be-130">Applications should be configured to validate these headers to ensure that only WebSockets coming from the expected origins are allowed.</span></span>

<span data-ttu-id="797be-131">No ASP.NET Core 2.1 e posterior, validação de cabeçalho pode ser obtida usando um middleware personalizado colocado **antes de `UseSignalR`e o middleware de autenticação** em `Configure`:</span><span class="sxs-lookup"><span data-stu-id="797be-131">In ASP.NET Core 2.1 and later, header validation can be achieved using a custom middleware placed **before `UseSignalR`, and authentication middleware** in `Configure`:</span></span>

[!code-csharp[Main](security/sample/Startup.cs?name=snippet2)]

> [!NOTE]
> <span data-ttu-id="797be-132">O cabeçalho `Origin` é controlado pelo cliente e, como o cabeçalho `Referer`, pode ser falsificado.</span><span class="sxs-lookup"><span data-stu-id="797be-132">The `Origin` header is controlled by the client and, like the `Referer` header, can be faked.</span></span> <span data-ttu-id="797be-133">Esses cabeçalhos devem **não** ser usado como um mecanismo de autenticação.</span><span class="sxs-lookup"><span data-stu-id="797be-133">These headers should **not** be used as an authentication mechanism.</span></span>

::: moniker-end

## <a name="access-token-logging"></a><span data-ttu-id="797be-134">Log de token de acesso</span><span class="sxs-lookup"><span data-stu-id="797be-134">Access token logging</span></span>

<span data-ttu-id="797be-135">Ao usar WebSockets ou Server-Sent eventos, o cliente de navegador envia o token de acesso na cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="797be-135">When using WebSockets or Server-Sent Events, the browser client sends the access token in the query string.</span></span> <span data-ttu-id="797be-136">Receber o token de acesso por meio da cadeia de caracteres de consulta é geralmente tão seguro quanto usar o padrão `Authorization` cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="797be-136">Receiving the access token via query string is generally as secure as using the standard `Authorization` header.</span></span> <span data-ttu-id="797be-137">Você sempre deve usar HTTPS para garantir uma conexão segura de ponta a ponta entre o cliente e o servidor.</span><span class="sxs-lookup"><span data-stu-id="797be-137">You should always use HTTPS to ensure a secure end-to-end connection between the client and the server.</span></span> <span data-ttu-id="797be-138">A URL para cada solicitação, incluindo a cadeia de caracteres de consulta de log de muitos servidores web.</span><span class="sxs-lookup"><span data-stu-id="797be-138">Many web servers log the URL for each request, including the query string.</span></span> <span data-ttu-id="797be-139">Registro em log as URLs pode registrar o token de acesso.</span><span class="sxs-lookup"><span data-stu-id="797be-139">Logging the URLs may log the access token.</span></span> <span data-ttu-id="797be-140">ASP.NET Core registra a URL para cada solicitação, por padrão, o que incluirá a cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="797be-140">ASP.NET Core logs the URL for each request by default, which will include the query string.</span></span> <span data-ttu-id="797be-141">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="797be-141">For example:</span></span>

```
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:5000/myhub?access_token=1234
```

<span data-ttu-id="797be-142">Se você tiver dúvidas sobre o log de dados com os logs do servidor, você pode desabilitar esse log inteiramente, configurando o `Microsoft.AspNetCore.Hosting` agente para o `Warning` nível ou superior (essas mensagens são gravadas em `Info` nível).</span><span class="sxs-lookup"><span data-stu-id="797be-142">If you have concerns about logging this data with your server logs, you can disable this logging entirely by configuring the `Microsoft.AspNetCore.Hosting` logger to the `Warning` level or above (these messages are written at `Info` level).</span></span> <span data-ttu-id="797be-143">Consulte a documentação sobre [filtragem de Log](xref:fundamentals/logging/index#log-filtering) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="797be-143">See the documentation on [Log Filtering](xref:fundamentals/logging/index#log-filtering) for more information.</span></span> <span data-ttu-id="797be-144">Se você ainda quiser registrar determinadas informações de solicitação, você poderá [escrever um middleware](xref:fundamentals/middleware/index#write-middleware) para registrar os dados necessários e filtrar o `access_token` valor de cadeia de caracteres de consulta (se presente).</span><span class="sxs-lookup"><span data-stu-id="797be-144">If you still want to log certain request information, you can [write a middleware](xref:fundamentals/middleware/index#write-middleware) to log the data you require and filter out the `access_token` query string value (if present).</span></span>

## <a name="exceptions"></a><span data-ttu-id="797be-145">Exceções</span><span class="sxs-lookup"><span data-stu-id="797be-145">Exceptions</span></span>

<span data-ttu-id="797be-146">Mensagens de exceção são geralmente consideradas dados confidenciais que não devem ser revelados para um cliente.</span><span class="sxs-lookup"><span data-stu-id="797be-146">Exception messages are generally considered sensitive data that shouldn't be revealed to a client.</span></span> <span data-ttu-id="797be-147">Por padrão, o SignalR não envia os detalhes de uma exceção gerada por um método de hub para o cliente.</span><span class="sxs-lookup"><span data-stu-id="797be-147">By default, SignalR doesn't send the details of an exception thrown by a hub method to the client.</span></span> <span data-ttu-id="797be-148">Em vez disso, o cliente recebe uma mensagem genérica indicando que ocorreu um erro.</span><span class="sxs-lookup"><span data-stu-id="797be-148">Instead, the client receives a generic message indicating an error occurred.</span></span> <span data-ttu-id="797be-149">Entrega de mensagem de exceção para o cliente pode ser substituída (por exemplo, no desenvolvimento ou teste) com [ `EnableDetailedErrors` ](xref:signalr/configuration#configure-server-options).</span><span class="sxs-lookup"><span data-stu-id="797be-149">Exception message delivery to the client can be overridden (for example in development or test) with [`EnableDetailedErrors`](xref:signalr/configuration#configure-server-options).</span></span> <span data-ttu-id="797be-150">Mensagens de exceção não devem ser expostas ao cliente em aplicativos de produção.</span><span class="sxs-lookup"><span data-stu-id="797be-150">Exception messages should not be exposed to the client in production apps.</span></span>

## <a name="buffer-management"></a><span data-ttu-id="797be-151">Gerenciamento de buffer</span><span class="sxs-lookup"><span data-stu-id="797be-151">Buffer management</span></span>

<span data-ttu-id="797be-152">O SignalR usa buffers por conexão para gerenciar mensagens de entrada e saídas.</span><span class="sxs-lookup"><span data-stu-id="797be-152">SignalR uses per-connection buffers to manage incoming and outgoing messages.</span></span> <span data-ttu-id="797be-153">Por padrão, o SignalR limita esses buffers para 32 KB.</span><span class="sxs-lookup"><span data-stu-id="797be-153">By default, SignalR limits these buffers to 32 KB.</span></span> <span data-ttu-id="797be-154">A mensagem maior que um cliente ou servidor pode enviar é 32 KB.</span><span class="sxs-lookup"><span data-stu-id="797be-154">The largest message a client or server can send is 32 KB.</span></span> <span data-ttu-id="797be-155">O máximo de memória consumido por uma conexão de mensagens é 32 KB.</span><span class="sxs-lookup"><span data-stu-id="797be-155">The maximum memory consumed by a connection for messages is 32 KB.</span></span> <span data-ttu-id="797be-156">Se as mensagens são sempre menores que 32 KB, você pode reduzir o limite, que:</span><span class="sxs-lookup"><span data-stu-id="797be-156">If your messages are always smaller than 32 KB, you can reduce the limit, which:</span></span>

* <span data-ttu-id="797be-157">Impede que um cliente que está sendo capaz de enviar uma mensagem maior.</span><span class="sxs-lookup"><span data-stu-id="797be-157">Prevents a client from being able to send a larger message.</span></span>
* <span data-ttu-id="797be-158">O servidor nunca será necessário alocar buffers grandes para aceitar mensagens.</span><span class="sxs-lookup"><span data-stu-id="797be-158">The server will never need to allocate large buffers to accept messages.</span></span>

<span data-ttu-id="797be-159">Se suas mensagens forem maiores que 32 KB, você pode aumentar o limite.</span><span class="sxs-lookup"><span data-stu-id="797be-159">If your messages are larger than 32 KB, you can increase the limit.</span></span> <span data-ttu-id="797be-160">Aumentar esse limite significa:</span><span class="sxs-lookup"><span data-stu-id="797be-160">Increasing this limit means:</span></span>

* <span data-ttu-id="797be-161">O cliente pode fazer com que o servidor para alocar buffers de memória grandes.</span><span class="sxs-lookup"><span data-stu-id="797be-161">The client can cause the server to allocate large memory buffers.</span></span>
* <span data-ttu-id="797be-162">Alocação de servidor de buffers grandes pode reduzir o número de conexões simultâneas.</span><span class="sxs-lookup"><span data-stu-id="797be-162">Server allocation of large buffers may reduce the number of concurrent connections.</span></span>

<span data-ttu-id="797be-163">Há limites para mensagens de entrada e saídas, ambos podem ser configuradas sobre o [ `HttpConnectionDispatcherOptions` ](xref:signalr/configuration#configure-server-options) objeto configurado no `MapHub`:</span><span class="sxs-lookup"><span data-stu-id="797be-163">There are limits for incoming and outgoing messages, both can be configured on the [`HttpConnectionDispatcherOptions`](xref:signalr/configuration#configure-server-options) object configured in `MapHub`:</span></span>

* <span data-ttu-id="797be-164">`ApplicationMaxBufferSize` representa o número máximo de bytes do cliente que os buffers de servidor.</span><span class="sxs-lookup"><span data-stu-id="797be-164">`ApplicationMaxBufferSize` represents the maximum number of bytes from the client that the server buffers.</span></span> <span data-ttu-id="797be-165">Se o cliente tenta enviar uma mensagem maior que esse limite, a conexão poderá ser fechada.</span><span class="sxs-lookup"><span data-stu-id="797be-165">If the client attempts to send a message larger than this limit, the connection may be closed.</span></span>
* <span data-ttu-id="797be-166">`TransportMaxBufferSize` representa o número máximo de bytes que o servidor pode enviar.</span><span class="sxs-lookup"><span data-stu-id="797be-166">`TransportMaxBufferSize` represents the maximum number of bytes the server can send.</span></span> <span data-ttu-id="797be-167">Se o servidor tenta enviar uma mensagem (incluindo valores de retorno de métodos de hub) maiores que esse limite, uma exceção será lançada.</span><span class="sxs-lookup"><span data-stu-id="797be-167">If the server attempts to send a message (including return values from hub methods) larger than this limit, an exception will be thrown.</span></span>

<span data-ttu-id="797be-168">Definir o limite como `0` desabilita o limite.</span><span class="sxs-lookup"><span data-stu-id="797be-168">Setting the limit to `0` disables the limit.</span></span> <span data-ttu-id="797be-169">Remover o limite permite que um cliente enviar uma mensagem de qualquer tamanho.</span><span class="sxs-lookup"><span data-stu-id="797be-169">Removing the limit allows a client to send a message of any size.</span></span> <span data-ttu-id="797be-170">Os clientes mal-intencionados enviando mensagens extensas podem provocar alocação de memória em excesso.</span><span class="sxs-lookup"><span data-stu-id="797be-170">Malicious clients sending large messages can cause excess memory to be allocated.</span></span> <span data-ttu-id="797be-171">Uso de memória em excesso pode reduzir significativamente o número de conexões simultâneas.</span><span class="sxs-lookup"><span data-stu-id="797be-171">Excess memory usage can significantly reduce the number of concurrent connections.</span></span>
