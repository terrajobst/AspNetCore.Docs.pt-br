---
title: Estado de sessão e aplicativo no ASP.NET Core
author: rick-anderson
description: Descubra abordagens para preservar o estado da sessão e do aplicativo entre as solicitações.
ms.author: riande
ms.custom: mvc
ms.date: 03/04/2019
uid: fundamentals/app-state
ms.openlocfilehash: 2e3591ac1d6b1670b27b1ed9e42f59ba2b956b37
ms.sourcegitcommit: 6ddd8a7675c1c1d997c8ab2d4498538e44954cac
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2019
ms.locfileid: "57400704"
---
# <a name="session-and-app-state-in-aspnet-core"></a><span data-ttu-id="ce9ba-103">Estado de sessão e aplicativo no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ce9ba-103">Session and app state in ASP.NET Core</span></span>

<span data-ttu-id="ce9ba-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Steve Smith](https://ardalis.com/), [Diana LaRose](https://github.com/DianaLaRose) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="ce9ba-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Steve Smith](https://ardalis.com/), [Diana LaRose](https://github.com/DianaLaRose), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="ce9ba-105">O HTTP é um protocolo sem estado.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-105">HTTP is a stateless protocol.</span></span> <span data-ttu-id="ce9ba-106">Sem realizar etapas adicionais, as solicitações HTTP são mensagens independentes que não mantêm os valores de usuário nem o estado do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-106">Without taking additional steps, HTTP requests are independent messages that don't retain user values or app state.</span></span> <span data-ttu-id="ce9ba-107">Este artigo descreve várias abordagens para preservar dados do usuário e estado de aplicativo entre solicitações.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-107">This article describes several approaches to preserve user data and app state between requests.</span></span>

<span data-ttu-id="ce9ba-108">[Exibir ou baixar código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/app-state/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ce9ba-108">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/app-state/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="state-management"></a><span data-ttu-id="ce9ba-109">Gerenciamento de estado</span><span class="sxs-lookup"><span data-stu-id="ce9ba-109">State management</span></span>

<span data-ttu-id="ce9ba-110">O estado pode ser armazenado usando várias abordagens.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-110">State can be stored using several approaches.</span></span> <span data-ttu-id="ce9ba-111">Cada abordagem é descrita posteriormente neste tópico.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-111">Each approach is described later in this topic.</span></span>

| <span data-ttu-id="ce9ba-112">Abordagem de armazenamento</span><span class="sxs-lookup"><span data-stu-id="ce9ba-112">Storage approach</span></span> | <span data-ttu-id="ce9ba-113">Mecanismo de armazenamento</span><span class="sxs-lookup"><span data-stu-id="ce9ba-113">Storage mechanism</span></span> |
| ---------------- | ----------------- |
| [<span data-ttu-id="ce9ba-114">Cookies</span><span class="sxs-lookup"><span data-stu-id="ce9ba-114">Cookies</span></span>](#cookies) | <span data-ttu-id="ce9ba-115">Cookies HTTP (podem incluir dados armazenados usando o código de aplicativo do lado do servidor)</span><span class="sxs-lookup"><span data-stu-id="ce9ba-115">HTTP cookies (may include data stored using server-side app code)</span></span> |
| [<span data-ttu-id="ce9ba-116">Estado de sessão</span><span class="sxs-lookup"><span data-stu-id="ce9ba-116">Session state</span></span>](#session-state) | <span data-ttu-id="ce9ba-117">Cookies HTTP e o código de aplicativo do lado do servidor</span><span class="sxs-lookup"><span data-stu-id="ce9ba-117">HTTP cookies and server-side app code</span></span> |
| [<span data-ttu-id="ce9ba-118">TempData</span><span class="sxs-lookup"><span data-stu-id="ce9ba-118">TempData</span></span>](#tempdata) | <span data-ttu-id="ce9ba-119">Estado de sessão ou cookies HTTP</span><span class="sxs-lookup"><span data-stu-id="ce9ba-119">HTTP cookies or session state</span></span> |
| [<span data-ttu-id="ce9ba-120">Cadeias de consulta</span><span class="sxs-lookup"><span data-stu-id="ce9ba-120">Query strings</span></span>](#query-strings) | <span data-ttu-id="ce9ba-121">Cadeias de caracteres de consulta HTTP</span><span class="sxs-lookup"><span data-stu-id="ce9ba-121">HTTP query strings</span></span> |
| [<span data-ttu-id="ce9ba-122">Campos ocultos</span><span class="sxs-lookup"><span data-stu-id="ce9ba-122">Hidden fields</span></span>](#hidden-fields) | <span data-ttu-id="ce9ba-123">Campos de formulário HTTP</span><span class="sxs-lookup"><span data-stu-id="ce9ba-123">HTTP form fields</span></span> |
| [<span data-ttu-id="ce9ba-124">HttpContext.Items</span><span class="sxs-lookup"><span data-stu-id="ce9ba-124">HttpContext.Items</span></span>](#httpcontextitems) | <span data-ttu-id="ce9ba-125">Código do aplicativo do lado do servidor</span><span class="sxs-lookup"><span data-stu-id="ce9ba-125">Server-side app code</span></span> |
| [<span data-ttu-id="ce9ba-126">Cache</span><span class="sxs-lookup"><span data-stu-id="ce9ba-126">Cache</span></span>](#cache) | <span data-ttu-id="ce9ba-127">Código do aplicativo do lado do servidor</span><span class="sxs-lookup"><span data-stu-id="ce9ba-127">Server-side app code</span></span> |
| [<span data-ttu-id="ce9ba-128">Injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="ce9ba-128">Dependency Injection</span></span>](#dependency-injection) | <span data-ttu-id="ce9ba-129">Código do aplicativo do lado do servidor</span><span class="sxs-lookup"><span data-stu-id="ce9ba-129">Server-side app code</span></span> |

## <a name="cookies"></a><span data-ttu-id="ce9ba-130">Cookies</span><span class="sxs-lookup"><span data-stu-id="ce9ba-130">Cookies</span></span>

<span data-ttu-id="ce9ba-131">Cookies armazenam dados entre solicitações.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-131">Cookies store data across requests.</span></span> <span data-ttu-id="ce9ba-132">Como os cookies são enviados com cada solicitação, seu tamanho deve ser reduzido ao mínimo.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-132">Because cookies are sent with every request, their size should be kept to a minimum.</span></span> <span data-ttu-id="ce9ba-133">Idealmente, somente um identificador deve ser armazenado em um cookie com os dados armazenados pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-133">Ideally, only an identifier should be stored in a cookie with the data stored by the app.</span></span> <span data-ttu-id="ce9ba-134">A maioria dos navegadores restringe o tamanho do cookie a 4.096 bytes.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-134">Most browsers restrict cookie size to 4096 bytes.</span></span> <span data-ttu-id="ce9ba-135">Somente um número limitado de cookies está disponível para cada domínio.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-135">Only a limited number of cookies are available for each domain.</span></span>

<span data-ttu-id="ce9ba-136">Uma vez que os cookies estão sujeitos à adulteração, eles devem ser validados pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-136">Because cookies are subject to tampering, they must be validated by the app.</span></span> <span data-ttu-id="ce9ba-137">Os cookies podem ser excluídos por usuários e expirarem em clientes.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-137">Cookies can be deleted by users and expire on clients.</span></span> <span data-ttu-id="ce9ba-138">No entanto, os cookies geralmente são a forma mais durável de persistência de dados no cliente.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-138">However, cookies are generally the most durable form of data persistence on the client.</span></span>

<span data-ttu-id="ce9ba-139">Frequentemente, cookies são usados para personalização quando o conteúdo é personalizado para um usuário conhecido.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-139">Cookies are often used for personalization, where content is customized for a known user.</span></span> <span data-ttu-id="ce9ba-140">O usuário é apenas identificado, e não autenticado, na maioria dos casos.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-140">The user is only identified and not authenticated in most cases.</span></span> <span data-ttu-id="ce9ba-141">O cookie pode armazenar o nome do usuário, o nome da conta ou a ID de usuário único (como um GUID).</span><span class="sxs-lookup"><span data-stu-id="ce9ba-141">The cookie can store the user's name, account name, or unique user ID (such as a GUID).</span></span> <span data-ttu-id="ce9ba-142">Você pode usar o cookie para acessar as configurações personalizadas do usuário, como cor preferida da tela de fundo do site.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-142">You can then use the cookie to access the user's personalized settings, such as their preferred website background color.</span></span>

<span data-ttu-id="ce9ba-143">Esteja ciente do [RGPD (Regulamento Geral sobre a Proteção de Dados) da União Europeia](https://ec.europa.eu/info/law/law-topic/data-protection) ao emitir cookies e lidar com questões de privacidade.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-143">Be mindful of the [European Union General Data Protection Regulations (GDPR)](https://ec.europa.eu/info/law/law-topic/data-protection) when issuing cookies and dealing with privacy concerns.</span></span> <span data-ttu-id="ce9ba-144">Para obter mais informações, veja [Suporte ao RGPD (Regulamento Geral sobre a Proteção de Dados) no ASP.NET Core](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="ce9ba-144">For more information, see [General Data Protection Regulation (GDPR) support in ASP.NET Core](xref:security/gdpr).</span></span>

## <a name="session-state"></a><span data-ttu-id="ce9ba-145">Estado de sessão</span><span class="sxs-lookup"><span data-stu-id="ce9ba-145">Session state</span></span>

<span data-ttu-id="ce9ba-146">Estado de sessão é um cenário do ASP.NET Core para o armazenamento de dados de usuário enquanto o usuário procura um aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-146">Session state is an ASP.NET Core scenario for storage of user data while the user browses a web app.</span></span> <span data-ttu-id="ce9ba-147">Estado de sessão usa um armazenamento mantido pelo aplicativo para que os dados persistam entre solicitações de um cliente.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-147">Session state uses a store maintained by the app to persist data across requests from a client.</span></span> <span data-ttu-id="ce9ba-148">Os dados da sessão são apoiados por um cache e considerados dados efêmeros&mdash;o site deve continuar funcionando sem os dados da sessão.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-148">The session data is backed by a cache and considered ephemeral data&mdash;the site should continue to function without the session data.</span></span> <span data-ttu-id="ce9ba-149">Os dados críticos do aplicativo devem ser armazenados no banco de dados do usuário e armazenados em cache na sessão apenas como uma otimização de desempenho.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-149">Critical application data should be stored in the user database and cached in session only as a performance optimization.</span></span>

> [!NOTE]
> <span data-ttu-id="ce9ba-150">A sessão não é compatível com os aplicativos [SignalR](xref:signalr/index) porque um [Hub SignalR](xref:signalr/hubs) pode ser executado independente de um contexto HTTP.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-150">Session isn't supported in [SignalR](xref:signalr/index) apps because a [SignalR Hub](xref:signalr/hubs) may execute independent of an HTTP context.</span></span> <span data-ttu-id="ce9ba-151">Por exemplo, isso pode ocorrer quando uma solicitação de sondagem longa é mantida aberta por um hub além do tempo de vida do contexto HTTP da solicitação.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-151">For example, this can occur when a long polling request is held open by a hub beyond the lifetime of the request's HTTP context.</span></span>

<span data-ttu-id="ce9ba-152">O ASP.NET Core mantém o estado de sessão fornecendo um cookie para o cliente que contém uma ID de sessão, que é enviada para o aplicativo com cada solicitação.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-152">ASP.NET Core maintains session state by providing a cookie to the client that contains a session ID, which is sent to the app with each request.</span></span> <span data-ttu-id="ce9ba-153">O aplicativo usa a ID da sessão para buscar os dados da sessão.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-153">The app uses the session ID to fetch the session data.</span></span>

<span data-ttu-id="ce9ba-154">Estado de sessão exibe os seguintes comportamentos:</span><span class="sxs-lookup"><span data-stu-id="ce9ba-154">Session state exhibits the following behaviors:</span></span>

* <span data-ttu-id="ce9ba-155">Uma vez que o cookie da sessão é específico ao navegador, não é possível compartilhar sessões entre navegadores.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-155">Because the session cookie is specific to the browser, sessions aren't shared across browsers.</span></span>
* <span data-ttu-id="ce9ba-156">Cookies da sessão são excluídos quando a sessão do navegador termina.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-156">Session cookies are deleted when the browser session ends.</span></span>
* <span data-ttu-id="ce9ba-157">Se um cookie for recebido de uma sessão expirada, será criada uma nova sessão que usa o mesmo cookie de sessão.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-157">If a cookie is received for an expired session, a new session is created that uses the same session cookie.</span></span>
* <span data-ttu-id="ce9ba-158">Sessões vazias não são mantidas&mdash;a sessão deve ter pelo menos um valor definido nela para que mantenha a sessão entre solicitações.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-158">Empty sessions aren't retained&mdash;the session must have at least one value set into it to persist the session across requests.</span></span> <span data-ttu-id="ce9ba-159">Quando uma sessão não é mantida, uma nova ID de sessão é gerada para cada nova solicitação.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-159">When a session isn't retained, a new session ID is generated for each new request.</span></span>
* <span data-ttu-id="ce9ba-160">O aplicativo mantém uma sessão por um tempo limitado após a última solicitação.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-160">The app retains a session for a limited time after the last request.</span></span> <span data-ttu-id="ce9ba-161">O aplicativo define o tempo limite da sessão ou usa o valor padrão de 20 minutos.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-161">The app either sets the session timeout or uses the default value of 20 minutes.</span></span> <span data-ttu-id="ce9ba-162">Estado de sessão é ideal para armazenar dados de usuário específicos para uma sessão em particular, mas em que os dados não requerem armazenamento permanente entre sessões.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-162">Session state is ideal for storing user data that's specific to a particular session but where the data doesn't require permanent storage across sessions.</span></span>
* <span data-ttu-id="ce9ba-163">Dados da sessão são excluídos quando a implementação [ISession.Clear](/dotnet/api/microsoft.aspnetcore.http.isession.clear) é chamada ou quando a sessão expira.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-163">Session data is deleted either when the [ISession.Clear](/dotnet/api/microsoft.aspnetcore.http.isession.clear) implementation is called or when the session expires.</span></span>
* <span data-ttu-id="ce9ba-164">Não há nenhum mecanismo padrão para informar o código do aplicativo de que um navegador cliente foi fechado ou quando o cookie de sessão foi excluído ou expirou no cliente.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-164">There's no default mechanism to inform app code that a client browser has been closed or when the session cookie is deleted or expired on the client.</span></span>
<span data-ttu-id="ce9ba-165">Os modelos de páginas do ASP.NET Core MVC e Razor incluem suporte ao RGPD (Regulamento Geral sobre a Proteção de Dados).</span><span class="sxs-lookup"><span data-stu-id="ce9ba-165">The ASP.NET Core MVC and Razor pages templates include support for General Data Protection Regulation (GDPR).</span></span> <span data-ttu-id="ce9ba-166">Os cookies do estado de sessão não são marcados como essenciais por padrão, portanto, o estado de sessão não é funcional, a menos que o rastreamento seja permitido pelo visitante do site.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-166">Session state cookies aren't marked essential by default, so session state isn't functional unless tracking is permitted by the site visitor.</span></span> <span data-ttu-id="ce9ba-167">Para obter mais informações, consulte <xref:security/gdpr#tempdata-provider-and-session-state-cookies-are-not-essential>.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-167">For more information, see <xref:security/gdpr#tempdata-provider-and-session-state-cookies-are-not-essential>.</span></span>

> [!WARNING]
> <span data-ttu-id="ce9ba-168">Não armazene dados confidenciais no estado de sessão.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-168">Don't store sensitive data in session state.</span></span> <span data-ttu-id="ce9ba-169">O usuário não pode fechar o navegador e limpar o cookie de sessão.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-169">The user might not close the browser and clear the session cookie.</span></span> <span data-ttu-id="ce9ba-170">Alguns navegadores mantêm cookies de sessão válidos entre as janelas do navegador.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-170">Some browsers maintain valid session cookies across browser windows.</span></span> <span data-ttu-id="ce9ba-171">Uma sessão não pode ser restringida a um único usuário&mdash;o próximo usuário pode continuar a procurar o aplicativo com o mesmo cookie de sessão.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-171">A session might not be restricted to a single user&mdash;the next user might continue to browse the app with the same session cookie.</span></span>

<span data-ttu-id="ce9ba-172">O provedor de cache na memória armazena dados de sessão na memória do servidor em que o aplicativo reside.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-172">The in-memory cache provider stores session data in the memory of the server where the app resides.</span></span> <span data-ttu-id="ce9ba-173">Em um cenário de farm de servidores:</span><span class="sxs-lookup"><span data-stu-id="ce9ba-173">In a server farm scenario:</span></span>

* <span data-ttu-id="ce9ba-174">Use *sessões persistentes* para vincular cada sessão a uma instância de aplicativo específico em um servidor individual.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-174">Use *sticky sessions* to tie each session to a specific app instance on an individual server.</span></span> <span data-ttu-id="ce9ba-175">O [Serviço de Aplicativo do Azure](https://azure.microsoft.com/services/app-service/) usa o [ARR (Application Request Routing)](/iis/extensions/planning-for-arr/using-the-application-request-routing-module) para impor as sessões persistentes por padrão.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-175">[Azure App Service](https://azure.microsoft.com/services/app-service/) uses [Application Request Routing (ARR)](/iis/extensions/planning-for-arr/using-the-application-request-routing-module) to enforce sticky sessions by default.</span></span> <span data-ttu-id="ce9ba-176">Entretanto, sessões autoadesivas podem afetar a escalabilidade e complicar atualizações de aplicativos Web.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-176">However, sticky sessions can affect scalability and complicate web app updates.</span></span> <span data-ttu-id="ce9ba-177">Uma abordagem melhor é usar um Redis ou cache distribuído do SQL Server, que não requer sessões persistentes.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-177">A better approach is to use a Redis or SQL Server distributed cache, which doesn't require sticky sessions.</span></span> <span data-ttu-id="ce9ba-178">Para obter mais informações, consulte <xref:performance/caching/distributed>.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-178">For more information, see <xref:performance/caching/distributed>.</span></span>
* <span data-ttu-id="ce9ba-179">O cookie de sessão é criptografado por meio de [IDataProtector](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotector).</span><span class="sxs-lookup"><span data-stu-id="ce9ba-179">The session cookie is encrypted via [IDataProtector](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotector).</span></span> <span data-ttu-id="ce9ba-180">A Proteção de Dados deve ser configurada corretamente para ler os cookies de sessão em cada computador.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-180">Data Protection must be properly configured to read session cookies on each machine.</span></span> <span data-ttu-id="ce9ba-181">Para obter mais informações, veja <xref:security/data-protection/introduction> e [Provedores de armazenamento de chaves](xref:security/data-protection/implementation/key-storage-providers).</span><span class="sxs-lookup"><span data-stu-id="ce9ba-181">For more information, see <xref:security/data-protection/introduction> and [Key storage providers](xref:security/data-protection/implementation/key-storage-providers).</span></span>

### <a name="configure-session-state"></a><span data-ttu-id="ce9ba-182">Configurar o estado de sessão</span><span class="sxs-lookup"><span data-stu-id="ce9ba-182">Configure session state</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="ce9ba-183">O pacote [Microsoft.AspNetCore.Session](https://www.nuget.org/packages/Microsoft.AspNetCore.Session/), incluído no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), fornece middleware para gerenciar o estado de sessão.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-183">The [Microsoft.AspNetCore.Session](https://www.nuget.org/packages/Microsoft.AspNetCore.Session/) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), provides middleware for managing session state.</span></span> <span data-ttu-id="ce9ba-184">Para habilitar o middleware da sessão, `Startup` deve conter:</span><span class="sxs-lookup"><span data-stu-id="ce9ba-184">To enable the session middleware, `Startup` must contain:</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="ce9ba-185">O pacote [Microsoft.AspNetCore.Session](https://www.nuget.org/packages/Microsoft.AspNetCore.Session/) fornece middleware para gerenciar o estado de sessão.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-185">The [Microsoft.AspNetCore.Session](https://www.nuget.org/packages/Microsoft.AspNetCore.Session/) package provides middleware for managing session state.</span></span> <span data-ttu-id="ce9ba-186">Para habilitar o middleware da sessão, `Startup` deve conter:</span><span class="sxs-lookup"><span data-stu-id="ce9ba-186">To enable the session middleware, `Startup` must contain:</span></span>

::: moniker-end

* <span data-ttu-id="ce9ba-187">Qualquer um dos caches de memória [IDistributedCache](/dotnet/api/microsoft.extensions.caching.distributed.idistributedcache).</span><span class="sxs-lookup"><span data-stu-id="ce9ba-187">Any of the [IDistributedCache](/dotnet/api/microsoft.extensions.caching.distributed.idistributedcache) memory caches.</span></span> <span data-ttu-id="ce9ba-188">A implementação `IDistributedCache` é usada como um repositório de backup para a sessão.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-188">The `IDistributedCache` implementation is used as a backing store for session.</span></span> <span data-ttu-id="ce9ba-189">Para obter mais informações, consulte <xref:performance/caching/distributed>.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-189">For more information, see <xref:performance/caching/distributed>.</span></span>
* <span data-ttu-id="ce9ba-190">Uma chamada para [AddSession](/dotnet/api/microsoft.extensions.dependencyinjection.sessionservicecollectionextensions.addsession) em `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-190">A call to [AddSession](/dotnet/api/microsoft.extensions.dependencyinjection.sessionservicecollectionextensions.addsession) in `ConfigureServices`.</span></span>
* <span data-ttu-id="ce9ba-191">Uma chamada para [UseSession](/dotnet/api/microsoft.aspnetcore.builder.sessionmiddlewareextensions#methods_) em `Configure`.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-191">A call to [UseSession](/dotnet/api/microsoft.aspnetcore.builder.sessionmiddlewareextensions#methods_) in `Configure`.</span></span>

<span data-ttu-id="ce9ba-192">O código a seguir mostra como configurar o provedor de sessão na memória com uma implementação padrão na memória de `IDistributedCache`:</span><span class="sxs-lookup"><span data-stu-id="ce9ba-192">The following code shows how to set up the in-memory session provider with a default in-memory implementation of `IDistributedCache`:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](app-state/samples/2.x/SessionSample/Startup.cs?name=snippet1&highlight=11,13-18,39)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](app-state/samples/1.x/SessionSample/Startup.cs?name=snippet1&highlight=5,7-12,19)]

::: moniker-end

<span data-ttu-id="ce9ba-193">A ordem do middleware é importante.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-193">The order of middleware is important.</span></span> <span data-ttu-id="ce9ba-194">No exemplo anterior, uma exceção `InvalidOperationException` ocorre quando `UseSession` é invocado após `UseMvc`.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-194">In the preceding example, an `InvalidOperationException` exception occurs when `UseSession` is invoked after `UseMvc`.</span></span> <span data-ttu-id="ce9ba-195">Para obter mais informações, veja [Ordenação de Middleware](xref:fundamentals/middleware/index#order).</span><span class="sxs-lookup"><span data-stu-id="ce9ba-195">For more information, see [Middleware Ordering](xref:fundamentals/middleware/index#order).</span></span>

<span data-ttu-id="ce9ba-196">[HttpContext.Session](/dotnet/api/microsoft.aspnetcore.http.httpcontext.session) estará disponível depois que o estado de sessão for configurado.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-196">[HttpContext.Session](/dotnet/api/microsoft.aspnetcore.http.httpcontext.session) is available after session state is configured.</span></span>

<span data-ttu-id="ce9ba-197">`HttpContext.Session` não pode ser acessado antes que `UseSession` tenha sido chamado.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-197">`HttpContext.Session` can't be accessed before `UseSession` has been called.</span></span>

<span data-ttu-id="ce9ba-198">Não é possível criar uma nova sessão com um novo cookie de sessão depois que o aplicativo começou a gravar no fluxo de resposta.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-198">A new session with a new session cookie can't be created after the app has begun writing to the response stream.</span></span> <span data-ttu-id="ce9ba-199">A exceção é registrada no log do servidor Web e não é exibida no navegador.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-199">The exception is recorded in the web server log and not displayed in the browser.</span></span>

### <a name="load-session-state-asynchronously"></a><span data-ttu-id="ce9ba-200">Carregue o estado de sessão de maneira assíncrona</span><span class="sxs-lookup"><span data-stu-id="ce9ba-200">Load session state asynchronously</span></span>

<span data-ttu-id="ce9ba-201">O provedor de sessão padrão no ASP.NET Core carregará os registros da sessão do repositório de backup [IDistributedCache](/dotnet/api/microsoft.extensions.caching.distributed.idistributedcache) subjacente de maneira assíncrona somente se o método [ISession.LoadAsync](/dotnet/api/microsoft.aspnetcore.http.isession.loadasync) for chamado explicitamente antes dos métodos [TryGetValue](/dotnet/api/microsoft.aspnetcore.http.isession.trygetvalue), [Set](/dotnet/api/microsoft.aspnetcore.http.isession.set) ou [Remove](/dotnet/api/microsoft.aspnetcore.http.isession.remove).</span><span class="sxs-lookup"><span data-stu-id="ce9ba-201">The default session provider in ASP.NET Core loads session records from the underlying [IDistributedCache](/dotnet/api/microsoft.extensions.caching.distributed.idistributedcache) backing store asynchronously only if the [ISession.LoadAsync](/dotnet/api/microsoft.aspnetcore.http.isession.loadasync) method is explicitly called before the [TryGetValue](/dotnet/api/microsoft.aspnetcore.http.isession.trygetvalue), [Set](/dotnet/api/microsoft.aspnetcore.http.isession.set), or [Remove](/dotnet/api/microsoft.aspnetcore.http.isession.remove) methods.</span></span> <span data-ttu-id="ce9ba-202">Se `LoadAsync` não for chamado primeiro, o registro de sessão subjacente será carregado de maneira síncrona, o que pode incorrer em uma penalidade de desempenho em escala.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-202">If `LoadAsync` isn't called first, the underlying session record is loaded synchronously, which can incur a performance penalty at scale.</span></span>

<span data-ttu-id="ce9ba-203">Para que aplicativos imponham esse padrão, encapsule as implementações [DistributedSessionStore](/dotnet/api/microsoft.aspnetcore.session.distributedsessionstore) e [DistributedSession](/dotnet/api/microsoft.aspnetcore.session.distributedsession) com versões que geram uma exceção se o método `LoadAsync` não for chamado antes de `TryGetValue`, `Set` ou `Remove`.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-203">To have apps enforce this pattern, wrap the [DistributedSessionStore](/dotnet/api/microsoft.aspnetcore.session.distributedsessionstore) and [DistributedSession](/dotnet/api/microsoft.aspnetcore.session.distributedsession) implementations with versions that throw an exception if the `LoadAsync` method isn't called before `TryGetValue`, `Set`, or `Remove`.</span></span> <span data-ttu-id="ce9ba-204">Registre as versões encapsuladas no contêiner de serviços.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-204">Register the wrapped versions in the services container.</span></span>

### <a name="session-options"></a><span data-ttu-id="ce9ba-205">Opções da sessão</span><span class="sxs-lookup"><span data-stu-id="ce9ba-205">Session options</span></span>

<span data-ttu-id="ce9ba-206">Para substituir os padrões da sessão, use [SessionOptions](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions).</span><span class="sxs-lookup"><span data-stu-id="ce9ba-206">To override session defaults, use [SessionOptions](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions).</span></span>

::: moniker range=">= aspnetcore-2.0"

| <span data-ttu-id="ce9ba-207">Opção</span><span class="sxs-lookup"><span data-stu-id="ce9ba-207">Option</span></span> | <span data-ttu-id="ce9ba-208">Descrição</span><span class="sxs-lookup"><span data-stu-id="ce9ba-208">Description</span></span> |
| ------ | ----------- |
| [<span data-ttu-id="ce9ba-209">Cookie</span><span class="sxs-lookup"><span data-stu-id="ce9ba-209">Cookie</span></span>](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.cookie) | <span data-ttu-id="ce9ba-210">Determina as configurações usadas para criar o cookie.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-210">Determines the settings used to create the cookie.</span></span> <span data-ttu-id="ce9ba-211">[Name](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.name) assume como padrão [SessionDefaults.CookieName](/dotnet/api/microsoft.aspnetcore.session.sessiondefaults.cookiename) (`.AspNetCore.Session`).</span><span class="sxs-lookup"><span data-stu-id="ce9ba-211">[Name](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.name) defaults to [SessionDefaults.CookieName](/dotnet/api/microsoft.aspnetcore.session.sessiondefaults.cookiename) (`.AspNetCore.Session`).</span></span> <span data-ttu-id="ce9ba-212">[Path](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.path) assume como padrão [SessionDefaults.CookiePath](/dotnet/api/microsoft.aspnetcore.session.sessiondefaults.cookiepath) (`/`).</span><span class="sxs-lookup"><span data-stu-id="ce9ba-212">[Path](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.path) defaults to [SessionDefaults.CookiePath](/dotnet/api/microsoft.aspnetcore.session.sessiondefaults.cookiepath) (`/`).</span></span> <span data-ttu-id="ce9ba-213">[SameSite](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.samesite) assume como padrão [SameSiteMode.Lax](/dotnet/api/microsoft.aspnetcore.http.samesitemode) (`1`).</span><span class="sxs-lookup"><span data-stu-id="ce9ba-213">[SameSite](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.samesite) defaults to [SameSiteMode.Lax](/dotnet/api/microsoft.aspnetcore.http.samesitemode) (`1`).</span></span> <span data-ttu-id="ce9ba-214">[HttpOnly](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.httponly) assume `true` como padrão .</span><span class="sxs-lookup"><span data-stu-id="ce9ba-214">[HttpOnly](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.httponly) defaults to `true`.</span></span> <span data-ttu-id="ce9ba-215">[IsEssential](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.isessential) assume `false` como padrão.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-215">[IsEssential](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.isessential) defaults to `false`.</span></span> |
| [<span data-ttu-id="ce9ba-216">IdleTimeout</span><span class="sxs-lookup"><span data-stu-id="ce9ba-216">IdleTimeout</span></span>](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.idletimeout) | <span data-ttu-id="ce9ba-217">O `IdleTimeout` indica por quanto tempo a sessão pode ficar ociosa antes de seu conteúdo ser abandonado.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-217">The `IdleTimeout` indicates how long the session can be idle before its contents are abandoned.</span></span> <span data-ttu-id="ce9ba-218">Cada acesso à sessão redefine o tempo limite.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-218">Each session access resets the timeout.</span></span> <span data-ttu-id="ce9ba-219">Essa configuração aplica-se somente ao conteúdo da sessão, não ao cookie.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-219">This setting only applies to the content of the session, not the cookie.</span></span> <span data-ttu-id="ce9ba-220">O padrão é de 20 minutos.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-220">The default is 20 minutes.</span></span> |
| [<span data-ttu-id="ce9ba-221">IOTimeout</span><span class="sxs-lookup"><span data-stu-id="ce9ba-221">IOTimeout</span></span>](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.iotimeout) | <span data-ttu-id="ce9ba-222">O tempo máximo permitido para carregar uma sessão do repositório ou para confirmá-la de volta para o repositório.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-222">The maximim amount of time allowed to load a session from the store or to commit it back to the store.</span></span> <span data-ttu-id="ce9ba-223">Essa configuração pode se aplicar somente a operações assíncronas.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-223">This setting may only apply to asynchronous operations.</span></span> <span data-ttu-id="ce9ba-224">Esse tempo limite pode ser desabilitado usando [InfiniteTimeSpan](/dotnet/api/system.threading.timeout.infinitetimespan).</span><span class="sxs-lookup"><span data-stu-id="ce9ba-224">This timeout can be disabled using [InfiniteTimeSpan](/dotnet/api/system.threading.timeout.infinitetimespan).</span></span> <span data-ttu-id="ce9ba-225">O padrão é 1 minuto.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-225">The default is 1 minute.</span></span> |

<span data-ttu-id="ce9ba-226">A sessão usa um cookie para rastrear e identificar solicitações de um único navegador.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-226">Session uses a cookie to track and identify requests from a single browser.</span></span> <span data-ttu-id="ce9ba-227">Por padrão, esse cookie se chama `.AspNetCore.Session`, e usa um caminho de `/`.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-227">By default, this cookie is named `.AspNetCore.Session`, and it uses a path of `/`.</span></span> <span data-ttu-id="ce9ba-228">Uma vez que o padrão do cookie não especifica um domínio, ele não fica disponível para o script do lado do cliente na página (porque [HttpOnly](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.httponly) usa `true` como padrão).</span><span class="sxs-lookup"><span data-stu-id="ce9ba-228">Because the cookie default doesn't specify a domain, it isn't made available to the client-side script on the page (because [HttpOnly](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.httponly) defaults to `true`).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

| <span data-ttu-id="ce9ba-229">Opção</span><span class="sxs-lookup"><span data-stu-id="ce9ba-229">Option</span></span> | <span data-ttu-id="ce9ba-230">Descrição</span><span class="sxs-lookup"><span data-stu-id="ce9ba-230">Description</span></span> |
| ------ | ----------- |
| [<span data-ttu-id="ce9ba-231">CookieDomain</span><span class="sxs-lookup"><span data-stu-id="ce9ba-231">CookieDomain</span></span>](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.cookiedomain) | <span data-ttu-id="ce9ba-232">Determina o domínio usado para criar o cookie.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-232">Determines the domain used to create the cookie.</span></span> <span data-ttu-id="ce9ba-233">`CookieDomain` não é definido por padrão.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-233">`CookieDomain` isn't set by default.</span></span> |
| [<span data-ttu-id="ce9ba-234">CookieHttpOnly</span><span class="sxs-lookup"><span data-stu-id="ce9ba-234">CookieHttpOnly</span></span>](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.cookiehttponly) | <span data-ttu-id="ce9ba-235">Determina se o navegador deve permitir que o cookie seja acessado pelo JavaScript do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-235">Determines if the browser should allow the cookie to be accessed by client-side JavaScript.</span></span> <span data-ttu-id="ce9ba-236">O padrão é `true`, o que significa que o cookie é transmitido apenas às solicitações HTTP e não é disponibilizado para o script na página.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-236">The default is `true`, which means the cookie is only passed to HTTP requests and isn't made available to script on the page.</span></span> |
| [<span data-ttu-id="ce9ba-237">CookieName</span><span class="sxs-lookup"><span data-stu-id="ce9ba-237">CookieName</span></span>](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.cookiename) | <span data-ttu-id="ce9ba-238">Determina o nome do cookie usado para manter a ID da sessão.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-238">Determines the cookie name used to persist the session ID.</span></span> <span data-ttu-id="ce9ba-239">O padrão é [SessionDefaults.CookieName](/dotnet/api/microsoft.aspnetcore.session.sessiondefaults.cookiename) (`.AspNetCore.Session`).</span><span class="sxs-lookup"><span data-stu-id="ce9ba-239">The default is [SessionDefaults.CookieName](/dotnet/api/microsoft.aspnetcore.session.sessiondefaults.cookiename) (`.AspNetCore.Session`).</span></span> |
| [<span data-ttu-id="ce9ba-240">CookiePath</span><span class="sxs-lookup"><span data-stu-id="ce9ba-240">CookiePath</span></span>](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.cookiepath) | <span data-ttu-id="ce9ba-241">Determina o caminho usado para criar o cookie.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-241">Determines the path used to create the cookie.</span></span> <span data-ttu-id="ce9ba-242">Usa como padrão [SessionDefaults.CookiePath](/dotnet/api/microsoft.aspnetcore.session.sessiondefaults.cookiepath) (`/`).</span><span class="sxs-lookup"><span data-stu-id="ce9ba-242">Defaults to [SessionDefaults.CookiePath](/dotnet/api/microsoft.aspnetcore.session.sessiondefaults.cookiepath) (`/`).</span></span> |
| [<span data-ttu-id="ce9ba-243">CookieSecure</span><span class="sxs-lookup"><span data-stu-id="ce9ba-243">CookieSecure</span></span>](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.cookiesecure) | <span data-ttu-id="ce9ba-244">Determina se o cookie deve ser transmitido apenas em solicitações HTTPS.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-244">Determines if the cookie should only be transmitted on HTTPS requests.</span></span> <span data-ttu-id="ce9ba-245">O padrão é [CookieSecurePolicy.None](/dotnet/api/microsoft.aspnetcore.http.cookiesecurepolicy) (`2`).</span><span class="sxs-lookup"><span data-stu-id="ce9ba-245">The default is [CookieSecurePolicy.None](/dotnet/api/microsoft.aspnetcore.http.cookiesecurepolicy) (`2`).</span></span> |
| [<span data-ttu-id="ce9ba-246">IdleTimeout</span><span class="sxs-lookup"><span data-stu-id="ce9ba-246">IdleTimeout</span></span>](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.idletimeout) | <span data-ttu-id="ce9ba-247">O `IdleTimeout` indica por quanto tempo a sessão pode ficar ociosa antes de seu conteúdo ser abandonado.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-247">The `IdleTimeout` indicates how long the session can be idle before its contents are abandoned.</span></span> <span data-ttu-id="ce9ba-248">Cada acesso à sessão redefine o tempo limite.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-248">Each session access resets the timeout.</span></span> <span data-ttu-id="ce9ba-249">Observe que isso só se aplica ao conteúdo da sessão, não o cookie.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-249">Note this only applies to the content of the session, not the cookie.</span></span> <span data-ttu-id="ce9ba-250">O padrão é de 20 minutos.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-250">The default is 20 minutes.</span></span> |

<span data-ttu-id="ce9ba-251">A sessão usa um cookie para rastrear e identificar solicitações de um único navegador.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-251">Session uses a cookie to track and identify requests from a single browser.</span></span> <span data-ttu-id="ce9ba-252">Por padrão, esse cookie se chama `.AspNet.Session`, e usa um caminho de `/`.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-252">By default, this cookie is named `.AspNet.Session`, and it uses a path of `/`.</span></span>

::: moniker-end

<span data-ttu-id="ce9ba-253">Para substituir os padrões de sessão do cookie, use `SessionOptions`:</span><span class="sxs-lookup"><span data-stu-id="ce9ba-253">To override cookie session defaults, use `SessionOptions`:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](app-state/samples_snapshot/2.x/SessionSample/Startup.cs?name=snippet1&highlight=13-18)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](app-state/samples_snapshot/1.x/SessionSample/Startup.cs?name=snippet1&highlight=5-9)]

::: moniker-end

<span data-ttu-id="ce9ba-254">O aplicativo usa a propriedade [IdleTimeout](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.idletimeout) para determinar por quanto tempo uma sessão pode ficar ociosa antes de seu conteúdo no cache do servidor ser abandonado.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-254">The app uses the [IdleTimeout](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.idletimeout) property to determine how long a session can be idle before its contents in the server's cache are abandoned.</span></span> <span data-ttu-id="ce9ba-255">Essa propriedade é independente da expiração do cookie.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-255">This property is independent of the cookie expiration.</span></span> <span data-ttu-id="ce9ba-256">Cada solicitação que passa pelo [Middleware da Sessão](/dotnet/api/microsoft.aspnetcore.session.sessionmiddleware) redefine o tempo limite.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-256">Each request that passes through the [Session Middleware](/dotnet/api/microsoft.aspnetcore.session.sessionmiddleware) resets the timeout.</span></span>

<span data-ttu-id="ce9ba-257">Estado de sessão é *sem bloqueio*.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-257">Session state is *non-locking*.</span></span> <span data-ttu-id="ce9ba-258">Se duas solicitações tentarem simultaneamente modificar o conteúdo de uma sessão, a última solicitação substituirá a primeira.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-258">If two requests simultaneously attempt to modify the contents of a session, the last request overrides the first.</span></span> <span data-ttu-id="ce9ba-259">`Session` é implementado como uma *sessão coerente*, o que significa que todo o conteúdo é armazenado junto.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-259">`Session` is implemented as a *coherent session*, which means that all the contents are stored together.</span></span> <span data-ttu-id="ce9ba-260">Quando duas solicitações buscam modificar valores de sessão diferentes, a última solicitação pode substituir as alterações de sessão feitas pelo primeira.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-260">When two requests seek to modify different session values, the last request may override session changes made by the first.</span></span>

### <a name="set-and-get-session-values"></a><span data-ttu-id="ce9ba-261">Definir e obter valores de Session</span><span class="sxs-lookup"><span data-stu-id="ce9ba-261">Set and get Session values</span></span>

<span data-ttu-id="ce9ba-262">Estado de sessão é acessado de uma classe [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel) Razor Pages ou classe [Controlador](/dotnet/api/microsoft.aspnetcore.mvc.controller) MVC com [HttpContext.Session](/dotnet/api/microsoft.aspnetcore.http.httpcontext.session).</span><span class="sxs-lookup"><span data-stu-id="ce9ba-262">Session state is accessed from a Razor Pages [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel) class or MVC [Controller](/dotnet/api/microsoft.aspnetcore.mvc.controller) class with [HttpContext.Session](/dotnet/api/microsoft.aspnetcore.http.httpcontext.session).</span></span> <span data-ttu-id="ce9ba-263">Esta propriedade é uma implementação de [ISession](/dotnet/api/microsoft.aspnetcore.http.isession).</span><span class="sxs-lookup"><span data-stu-id="ce9ba-263">This property is an [ISession](/dotnet/api/microsoft.aspnetcore.http.isession) implementation.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="ce9ba-264">A implementação de `ISession` fornece vários métodos de extensão para definir e recuperar valores de inteiro e cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-264">The `ISession` implementation provides several extension methods to set and retrieve integer and string values.</span></span> <span data-ttu-id="ce9ba-265">Os métodos de extensão estão no namespace [Microsoft.AspNetCore.Http](/dotnet/api/microsoft.aspnetcore.http) (adicione uma instrução `using Microsoft.AspNetCore.Http;` para acessar os métodos de extensão) quando o pacote [Microsoft.AspNetCore.Http.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.Http.Extensions/) for referenciado pelo projeto.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-265">The extension methods are in the [Microsoft.AspNetCore.Http](/dotnet/api/microsoft.aspnetcore.http) namespace (add a `using Microsoft.AspNetCore.Http;` statement to gain access to the extension methods) when the [Microsoft.AspNetCore.Http.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.Http.Extensions/) package is referenced by the project.</span></span> <span data-ttu-id="ce9ba-266">Ambos os pacotes são incluídos no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="ce9ba-266">Both packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="ce9ba-267">A implementação de `ISession` fornece vários métodos de extensão para definir e recuperar valores de inteiro e cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-267">The `ISession` implementation provides several extension methods to set and retreive integer and string values.</span></span> <span data-ttu-id="ce9ba-268">Os métodos de extensão estão no namespace [Microsoft.AspNetCore.Http](/dotnet/api/microsoft.aspnetcore.http) (adicione uma instrução `using Microsoft.AspNetCore.Http;` para acessar os métodos de extensão) quando o pacote [Microsoft.AspNetCore.Http.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.Http.Extensions/) for referenciado pelo projeto.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-268">The extension methods are in the [Microsoft.AspNetCore.Http](/dotnet/api/microsoft.aspnetcore.http) namespace (add a `using Microsoft.AspNetCore.Http;` statement to gain access to the extension methods) when the [Microsoft.AspNetCore.Http.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.Http.Extensions/) package is referenced by the project.</span></span>

::: moniker-end

<span data-ttu-id="ce9ba-269">Métodos de extensão `ISession`:</span><span class="sxs-lookup"><span data-stu-id="ce9ba-269">`ISession` extension methods:</span></span>

* [<span data-ttu-id="ce9ba-270">Get(ISession, String)</span><span class="sxs-lookup"><span data-stu-id="ce9ba-270">Get(ISession, String)</span></span>](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.get)
* [<span data-ttu-id="ce9ba-271">GetInt32(ISession, String)</span><span class="sxs-lookup"><span data-stu-id="ce9ba-271">GetInt32(ISession, String)</span></span>](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.getint32)
* [<span data-ttu-id="ce9ba-272">GetString(ISession, String)</span><span class="sxs-lookup"><span data-stu-id="ce9ba-272">GetString(ISession, String)</span></span>](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.getstring)
* [<span data-ttu-id="ce9ba-273">SetInt32(ISession, String, Int32)</span><span class="sxs-lookup"><span data-stu-id="ce9ba-273">SetInt32(ISession, String, Int32)</span></span>](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.setint32)
* [<span data-ttu-id="ce9ba-274">SetString(ISession, String, String)</span><span class="sxs-lookup"><span data-stu-id="ce9ba-274">SetString(ISession, String, String)</span></span>](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.setstring)

<span data-ttu-id="ce9ba-275">O exemplo a seguir recupera o valor da sessão para a chave `IndexModel.SessionKeyName` (`_Name` no aplicativo de exemplo) em uma página do Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="ce9ba-275">The following example retrieves the session value for the `IndexModel.SessionKeyName` key (`_Name` in the sample app) in a Razor Pages page:</span></span>

```csharp
@page
@using Microsoft.AspNetCore.Http
@model IndexModel

...

Name: @HttpContext.Session.GetString(IndexModel.SessionKeyName)
```

<span data-ttu-id="ce9ba-276">O exemplo a seguir mostra como definir e obter um número inteiro e uma cadeia de caracteres:</span><span class="sxs-lookup"><span data-stu-id="ce9ba-276">The following example shows how to set and get an integer and a string:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](app-state/samples/2.x/SessionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=18-19,22-23)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](app-state/samples/1.x/SessionSample/Controllers/HomeController.cs?name=snippet1&highlight=10-11,18-19)]

::: moniker-end

<span data-ttu-id="ce9ba-277">Todos os dados de sessão devem ser serializados para habilitar um cenário de cache distribuído, mesmo ao usar o cache na memória.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-277">All session data must be serialized to enable a distributed cache scenario, even when using the in-memory cache.</span></span> <span data-ttu-id="ce9ba-278">Cadeia de caracteres mínima e serializadores número são fornecidos (confira os métodos e os métodos de extensão de [ISession](/dotnet/api/microsoft.aspnetcore.http.isession)).</span><span class="sxs-lookup"><span data-stu-id="ce9ba-278">Minimal string and number serializers are provided (see the methods and extension methods of [ISession](/dotnet/api/microsoft.aspnetcore.http.isession)).</span></span> <span data-ttu-id="ce9ba-279">Tipos complexos devem ser serializados pelo usuário por meio de outro mecanismo, como JSON.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-279">Complex types must be serialized by the user using another mechanism, such as JSON.</span></span>

<span data-ttu-id="ce9ba-280">Adicione os seguintes métodos de extensão para definir e obter objetos serializáveis:</span><span class="sxs-lookup"><span data-stu-id="ce9ba-280">Add the following extension methods to set and get serializable objects:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](app-state/samples/2.x/SessionSample/Extensions/SessionExtensions.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](app-state/samples/1.x/SessionSample/Extensions/SessionExtensions.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="ce9ba-281">O exemplo a seguir mostra como definir e obter um objeto serializável com os métodos de extensão:</span><span class="sxs-lookup"><span data-stu-id="ce9ba-281">The following example shows how to set and get a serializable object with the extension methods:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](app-state/samples/2.x/SessionSample/Pages/Index.cshtml.cs?name=snippet2)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](app-state/samples/1.x/SessionSample/Controllers/HomeController.cs?name=snippet2&highlight=4,12)]

::: moniker-end

## <a name="tempdata"></a><span data-ttu-id="ce9ba-282">TempData</span><span class="sxs-lookup"><span data-stu-id="ce9ba-282">TempData</span></span>

<span data-ttu-id="ce9ba-283">O ASP.NET Core expõe a [propriedade TempData de um modelo de página do Razor Pages](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.tempdata) ou [TempData de um controlador MVC](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata).</span><span class="sxs-lookup"><span data-stu-id="ce9ba-283">ASP.NET Core exposes the [TempData property of a Razor Pages page model](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.tempdata) or [TempData of an MVC controller](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata).</span></span> <span data-ttu-id="ce9ba-284">Essa propriedade armazena dados até eles serem lidos.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-284">This property stores data until it's read.</span></span> <span data-ttu-id="ce9ba-285">Os métodos [Keep](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.itempdatadictionary.keep) e [Peek](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.itempdatadictionary.peek) podem ser usados para examinar os dados sem exclusão.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-285">The [Keep](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.itempdatadictionary.keep) and [Peek](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.itempdatadictionary.peek) methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="ce9ba-286">TempData é particularmente útil para redirecionamento em casos em que são necessários dados para mais de uma única solicitação.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-286">TempData is particularly useful for redirection when data is required for more than a single request.</span></span> <span data-ttu-id="ce9ba-287">TempData é implementado por provedores de TempData usando cookies ou estado de sessão.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-287">TempData is implemented by TempData providers using either cookies or session state.</span></span>

### <a name="tempdata-providers"></a><span data-ttu-id="ce9ba-288">Provedores de TempData</span><span class="sxs-lookup"><span data-stu-id="ce9ba-288">TempData providers</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="ce9ba-289">No ASP.NET Core 2.0 ou posterior, o provedor TempData baseado em cookies é usado por padrão para armazenar TempData em cookies.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-289">In ASP.NET Core 2.0 or later, the cookie-based TempData provider is used by default to store TempData in cookies.</span></span>

<span data-ttu-id="ce9ba-290">Os dados do cookie são criptografados usando [IDataProtector](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotector), codificado com [Base64UrlTextEncoder](/dotnet/api/microsoft.aspnetcore.webutilities.base64urltextencoder), em seguida, em partes.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-290">The cookie data is encrypted using [IDataProtector](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotector), encoded with [Base64UrlTextEncoder](/dotnet/api/microsoft.aspnetcore.webutilities.base64urltextencoder), then chunked.</span></span> <span data-ttu-id="ce9ba-291">Como o cookie é dividido em partes, o limite de tamanho de cookie único encontrado no ASP.NET Core 1.x não se aplica.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-291">Because the cookie is chunked, the single cookie size limit found in ASP.NET Core 1.x doesn't apply.</span></span> <span data-ttu-id="ce9ba-292">Os dados do cookie não são compactados porque a compactação de dados criptografados pode levar a problemas de segurança, como os ataques [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit)) e [BREACH](https://wikipedia.org/wiki/BREACH_(security_exploit)).</span><span class="sxs-lookup"><span data-stu-id="ce9ba-292">The cookie data isn't compressed because compressing encrypted data can lead to security problems such as the [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit)) and [BREACH](https://wikipedia.org/wiki/BREACH_(security_exploit)) attacks.</span></span> <span data-ttu-id="ce9ba-293">Para obter mais informações sobre o provedor de TempData baseado em cookie, consulte [CookieTempDataProvider](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.cookietempdataprovider).</span><span class="sxs-lookup"><span data-stu-id="ce9ba-293">For more information on the cookie-based TempData provider, see [CookieTempDataProvider](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.cookietempdataprovider).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="ce9ba-294">No ASP.NET Core 1.0 e 1.1, o provedor de TempData do estado de sessão é o provedor padrão.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-294">In ASP.NET Core 1.0 and 1.1, the session state TempData provider is the default provider.</span></span>

::: moniker-end

### <a name="choose-a-tempdata-provider"></a><span data-ttu-id="ce9ba-295">Escolha um provedor de TempData</span><span class="sxs-lookup"><span data-stu-id="ce9ba-295">Choose a TempData provider</span></span>

<span data-ttu-id="ce9ba-296">Escolher um provedor de TempData envolve várias considerações, como:</span><span class="sxs-lookup"><span data-stu-id="ce9ba-296">Choosing a TempData provider involves several considerations, such as:</span></span>

1. <span data-ttu-id="ce9ba-297">O aplicativo já usa estado de sessão?</span><span class="sxs-lookup"><span data-stu-id="ce9ba-297">Does the app already use session state?</span></span> <span data-ttu-id="ce9ba-298">Se for o caso, usar o provedor de TempData do estado de sessão não terá custos adicionais para o aplicativo (além do tamanho dos dados).</span><span class="sxs-lookup"><span data-stu-id="ce9ba-298">If so, using the session state TempData provider has no additional cost to the app (aside from the size of the data).</span></span>
2. <span data-ttu-id="ce9ba-299">O aplicativo usa TempData apenas raramente para quantidades relativamente pequenas de dados (até 500 bytes)?</span><span class="sxs-lookup"><span data-stu-id="ce9ba-299">Does the app use TempData only sparingly for relatively small amounts of data (up to 500 bytes)?</span></span> <span data-ttu-id="ce9ba-300">Em caso afirmativo, o provedor de TempData do cookie adiciona um pequeno custo a cada solicitação que transportar TempData.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-300">If so, the cookie TempData provider adds a small cost to each request that carries TempData.</span></span> <span data-ttu-id="ce9ba-301">Caso contrário, o provedor de TempData do estado de sessão pode ser útil para evitar fazer viagens de ida e volta para uma grande quantidade de dados a cada solicitação até que TempData seja consumido.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-301">If not, the session state TempData provider can be beneficial to avoid round-tripping a large amount of data in each request until the TempData is consumed.</span></span>
3. <span data-ttu-id="ce9ba-302">O aplicativo é executado em um farm de servidores em vários servidores?</span><span class="sxs-lookup"><span data-stu-id="ce9ba-302">Does the app run in a server farm on multiple servers?</span></span> <span data-ttu-id="ce9ba-303">Se for o caso, não será necessária nenhuma configuração adicional para usar o provedor TempData do cookie fora da Proteção de Dados (confira <xref:security/data-protection/introduction> e [Provedores de armazenamento de chaves](xref:security/data-protection/implementation/key-storage-providers)).</span><span class="sxs-lookup"><span data-stu-id="ce9ba-303">If so, there's no additional configuration required to use the cookie TempData provider outside of Data Protection (see <xref:security/data-protection/introduction> and [Key storage providers](xref:security/data-protection/implementation/key-storage-providers)).</span></span>

> [!NOTE]
> <span data-ttu-id="ce9ba-304">A maioria dos clientes da Web (como navegadores da Web) impõem limites quanto ao tamanho máximo de cada cookie, o número total de cookies ou ambos.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-304">Most web clients (such as web browsers) enforce limits on the maximum size of each cookie, the total number of cookies, or both.</span></span> <span data-ttu-id="ce9ba-305">Ao usar o provedor TempData do cookie, verifique se o aplicativo não ultrapassará esses limites.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-305">When using the cookie TempData provider, verify the app won't exceed these limits.</span></span> <span data-ttu-id="ce9ba-306">Considere o tamanho total dos dados.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-306">Consider the total size of the data.</span></span> <span data-ttu-id="ce9ba-307">Conta para aumento no tamanho de cookie devido à criptografia e ao agrupamento.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-307">Account for increases in cookie size due to encryption and chunking.</span></span>

### <a name="configure-the-tempdata-provider"></a><span data-ttu-id="ce9ba-308">Configurar o provedor de TempData</span><span class="sxs-lookup"><span data-stu-id="ce9ba-308">Configure the TempData provider</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="ce9ba-309">O provedor de TempData baseado em cookie é habilitado por padrão.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-309">The cookie-based TempData provider is enabled by default.</span></span>

<span data-ttu-id="ce9ba-310">Para habilitar o provedor TempData baseado em sessão, use o método de extensão [AddSessionStateTempDataProvider](/dotnet/api/microsoft.extensions.dependencyinjection.mvcviewfeaturesmvcbuilderextensions.addsessionstatetempdataprovider):</span><span class="sxs-lookup"><span data-stu-id="ce9ba-310">To enable the session-based TempData provider, use the [AddSessionStateTempDataProvider](/dotnet/api/microsoft.extensions.dependencyinjection.mvcviewfeaturesmvcbuilderextensions.addsessionstatetempdataprovider) extension method:</span></span>

[!code-csharp[](app-state/samples_snapshot_2/2.x/SessionSample/Startup.cs?name=snippet1&highlight=11,13,32)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="ce9ba-311">O código da classe `Startup` a seguir configura o provedor de TempData baseado em sessão:</span><span class="sxs-lookup"><span data-stu-id="ce9ba-311">The following `Startup` class code configures the session-based TempData provider:</span></span>

[!code-csharp[](app-state/samples_snapshot_2/1.x/SessionSample/Startup.cs?name=snippet1&highlight=4,9)]

::: moniker-end

<span data-ttu-id="ce9ba-312">A ordem do middleware é importante.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-312">The order of middleware is important.</span></span> <span data-ttu-id="ce9ba-313">No exemplo anterior, uma exceção `InvalidOperationException` ocorre quando `UseSession` é invocado após `UseMvc`.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-313">In the preceding example, an `InvalidOperationException` exception occurs when `UseSession` is invoked after `UseMvc`.</span></span> <span data-ttu-id="ce9ba-314">Para obter mais informações, veja [Ordenação de Middleware](xref:fundamentals/middleware/index#order).</span><span class="sxs-lookup"><span data-stu-id="ce9ba-314">For more information, see [Middleware Ordering](xref:fundamentals/middleware/index#order).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="ce9ba-315">Se o alvo for o .NET Framework e o provedor TempData baseado em sessão for usado, adicione o pacote [Microsoft.AspNetCore.Session](https://www.nuget.org/packages/Microsoft.AspNetCore.Session/) ao projeto.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-315">If targeting .NET Framework and using the session-based TempData provider, add the [Microsoft.AspNetCore.Session](https://www.nuget.org/packages/Microsoft.AspNetCore.Session/) package to the project.</span></span>

## <a name="query-strings"></a><span data-ttu-id="ce9ba-316">Cadeias de consulta</span><span class="sxs-lookup"><span data-stu-id="ce9ba-316">Query strings</span></span>

<span data-ttu-id="ce9ba-317">É possível passar uma quantidade limitada de dados de uma solicitação para outra adicionando-a à cadeia de caracteres de consulta da nova solicitação.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-317">A limited amount of data can be passed from one request to another by adding it to the new request's query string.</span></span> <span data-ttu-id="ce9ba-318">Isso é útil para capturar o estado de uma maneira persistente que permita que links com estado inserido sejam compartilhados por email ou por redes sociais.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-318">This is useful for capturing state in a persistent manner that allows links with embedded state to be shared through email or social networks.</span></span> <span data-ttu-id="ce9ba-319">Uma vez que cadeias de consulta de URL são públicas, nunca use cadeias de consulta para dados confidenciais.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-319">Because URL query strings are public, never use query strings for sensitive data.</span></span>

<span data-ttu-id="ce9ba-320">Além da possibilidade de ocorrência de compartilhamento não intencional, incluir dados em cadeias de consulta pode criar oportunidades para ataques de [CSRF (solicitação intersite forjada)](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)), que podem enganar os usuários para que eles visitem sites mal-intencionados enquanto estão autenticados.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-320">In addition to unintended sharing, including data in query strings can create opportunities for [Cross-Site Request Forgery (CSRF)](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) attacks, which can trick users into visiting malicious sites while authenticated.</span></span> <span data-ttu-id="ce9ba-321">Invasores então podem roubar dados do usuário do aplicativo ou executar ações mal-intencionadas em nome do usuário.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-321">Attackers can then steal user data from the app or take malicious actions on behalf of the user.</span></span> <span data-ttu-id="ce9ba-322">Qualquer estado de sessão ou aplicativo preservado deve se proteger contra ataques CSRF.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-322">Any preserved app or session state must protect against CSRF attacks.</span></span> <span data-ttu-id="ce9ba-323">Para obter mais informações, confira [Impedir ataques de XSRF/CSRF (solicitação intersite forjada)](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="ce9ba-323">For more information, see [Prevent Cross-Site Request Forgery (XSRF/CSRF) attacks](xref:security/anti-request-forgery).</span></span>

## <a name="hidden-fields"></a><span data-ttu-id="ce9ba-324">Campos ocultos</span><span class="sxs-lookup"><span data-stu-id="ce9ba-324">Hidden fields</span></span>

<span data-ttu-id="ce9ba-325">Dados podem ser salvos em campos de formulário ocultos e postados novamente na solicitação seguinte.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-325">Data can be saved in hidden form fields and posted back on the next request.</span></span> <span data-ttu-id="ce9ba-326">Isso é comum em formulários com várias páginas.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-326">This is common in multi-page forms.</span></span> <span data-ttu-id="ce9ba-327">Uma vez que o cliente potencialmente pode adulterar os dados, o aplicativo deve sempre revalidar os dados armazenados nos campos ocultos.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-327">Because the client can potentially tamper with the data, the app must always revalidate the data stored in hidden fields.</span></span>

## <a name="httpcontextitems"></a><span data-ttu-id="ce9ba-328">HttpContext.Items</span><span class="sxs-lookup"><span data-stu-id="ce9ba-328">HttpContext.Items</span></span>

<span data-ttu-id="ce9ba-329">A coleção [HttpContext.Items](/dotnet/api/microsoft.aspnetcore.http.httpcontext.items) é usada para armazenar dados durante o processamento de uma única solicitação.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-329">The [HttpContext.Items](/dotnet/api/microsoft.aspnetcore.http.httpcontext.items) collection is used to store data while processing a single request.</span></span> <span data-ttu-id="ce9ba-330">O conteúdo da coleção é descartado após uma solicitação ser processada.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-330">The collection's contents are discarded after a request is processed.</span></span> <span data-ttu-id="ce9ba-331">A coleção `Items` costuma ser usada para permitir que componentes ou middleware se comuniquem quando operam em diferentes momentos durante uma solicitação e não têm nenhuma maneira direta de passar parâmetros.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-331">The `Items` collection is often used to allow components or middleware to communicate when they operate at different points in time during a request and have no direct way to pass parameters.</span></span>

<span data-ttu-id="ce9ba-332">No exemplo a seguir, [middleware](xref:fundamentals/middleware/index) adiciona `isVerified` à coleção `Items`.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-332">In the following example, [middleware](xref:fundamentals/middleware/index) adds `isVerified` to the `Items` collection.</span></span>

```csharp
app.Use(async (context, next) =>
{
    // perform some verification
    context.Items["isVerified"] = true;
    await next.Invoke();
});
```

<span data-ttu-id="ce9ba-333">Mais tarde no pipeline, outro middleware poderá acessar o valor de `isVerified`:</span><span class="sxs-lookup"><span data-stu-id="ce9ba-333">Later in the pipeline, another middleware can access the value of `isVerified`:</span></span>

```csharp
app.Run(async (context) =>
{
    await context.Response.WriteAsync($"Verified: {context.Items["isVerified"]}");
});
```

<span data-ttu-id="ce9ba-334">Para middleware usado apenas por um único aplicativo, chaves `string` são aceitáveis.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-334">For middleware that's only used by a single app, `string` keys are acceptable.</span></span> <span data-ttu-id="ce9ba-335">Middleware compartilhado entre instâncias do aplicativo deve usar chaves de objeto únicas para evitar colisões de chaves.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-335">Middleware shared between app instances should use unique object keys to avoid key collisions.</span></span> <span data-ttu-id="ce9ba-336">O exemplo a seguir mostra como usar uma chave de objeto exclusiva definida em uma classe de middleware:</span><span class="sxs-lookup"><span data-stu-id="ce9ba-336">The following example shows how to use a unique object key defined in a middleware class:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](app-state/samples/2.x/SessionSample/Middleware/HttpContextItemsMiddleware.cs?name=snippet1&highlight=4,13)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](app-state/samples/1.x/SessionSample/Middleware/HttpContextItemsMiddleware.cs?name=snippet1&highlight=5,14)]

::: moniker-end

<span data-ttu-id="ce9ba-337">Outros códigos podem acessar o valor armazenado em `HttpContext.Items` usando a chave exposta pela classe do middleware:</span><span class="sxs-lookup"><span data-stu-id="ce9ba-337">Other code can access the value stored in `HttpContext.Items` using the key exposed by the middleware class:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](app-state/samples/2.x/SessionSample/Pages/Index.cshtml.cs?name=snippet3)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](app-state/samples/1.x/SessionSample/Controllers/HomeController.cs?name=snippet3)]

::: moniker-end

<span data-ttu-id="ce9ba-338">Essa abordagem também tem a vantagem de eliminar o uso de cadeias de caracteres de chave no código.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-338">This approach also has the advantage of eliminating the use of key strings in the code.</span></span>

## <a name="cache"></a><span data-ttu-id="ce9ba-339">Cache</span><span class="sxs-lookup"><span data-stu-id="ce9ba-339">Cache</span></span>

<span data-ttu-id="ce9ba-340">O cache é uma maneira eficiente de armazenar e recuperar dados.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-340">Caching is an efficient way to store and retrieve data.</span></span> <span data-ttu-id="ce9ba-341">O aplicativo pode controlar o tempo de vida de itens em cache.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-341">The app can control the lifetime of cached items.</span></span>

<span data-ttu-id="ce9ba-342">Dados armazenados em cache não são associados uma solicitação, usuário ou sessão específico.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-342">Cached data isn't associated with a specific request, user, or session.</span></span> <span data-ttu-id="ce9ba-343">**Tenha cuidado para não armazenar em cache dados específicos do usuário que podem ser recuperados pelas solicitações de outros usuários.**</span><span class="sxs-lookup"><span data-stu-id="ce9ba-343">**Be careful not to cache user-specific data that may be retrieved by other users' requests.**</span></span>

<span data-ttu-id="ce9ba-344">Para obter mais informações, consulte <xref:performance/caching/response>.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-344">For more information, see <xref:performance/caching/response>.</span></span>

## <a name="dependency-injection"></a><span data-ttu-id="ce9ba-345">Injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="ce9ba-345">Dependency Injection</span></span>

<span data-ttu-id="ce9ba-346">Use a [Injeção de dependência](xref:fundamentals/dependency-injection) para disponibilizar dados para todos os usuários:</span><span class="sxs-lookup"><span data-stu-id="ce9ba-346">Use [Dependency Injection](xref:fundamentals/dependency-injection) to make data available to all users:</span></span>

1. <span data-ttu-id="ce9ba-347">Defina um serviço que contenha os dados.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-347">Define a service containing the data.</span></span> <span data-ttu-id="ce9ba-348">Por exemplo, uma classe denominada `MyAppData` está definida:</span><span class="sxs-lookup"><span data-stu-id="ce9ba-348">For example, a class named `MyAppData` is defined:</span></span>

    ```csharp
    public class MyAppData
    {
        // Declare properties and methods
    }
    ```

2. <span data-ttu-id="ce9ba-349">Adicione a classe de serviço a `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="ce9ba-349">Add the service class to `Startup.ConfigureServices`:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<MyAppData>();
    }
    ```

3. <span data-ttu-id="ce9ba-350">Consuma a classe de serviço de dados:</span><span class="sxs-lookup"><span data-stu-id="ce9ba-350">Consume the data service class:</span></span>

    ::: moniker range=">= aspnetcore-2.0"

    ```csharp
    public class IndexModel : PageModel
    {
        public IndexModel(MyAppData myService)
        {
            // Do something with the service
            //    Examples: Read data, store in a field or property
        }
    }
    ```

    ::: moniker-end

    ::: moniker range="< aspnetcore-2.0"

    ```csharp
    public class HomeController : Controller
    {
        public HomeController(MyAppData myService)
        {
            // Do something with the service
            //    Examples: Read data, store in a field or property
        }
    }
    ```

    ::: moniker-end

## <a name="common-errors"></a><span data-ttu-id="ce9ba-351">Erros comuns</span><span class="sxs-lookup"><span data-stu-id="ce9ba-351">Common errors</span></span>

* <span data-ttu-id="ce9ba-352">"Não é possível resolver o serviço para o tipo 'Microsoft.Extensions.Caching.Distributed.IDistributedCache' ao tentar ativar 'Microsoft.AspNetCore.Session.DistributedSessionStore'."</span><span class="sxs-lookup"><span data-stu-id="ce9ba-352">"Unable to resolve service for type 'Microsoft.Extensions.Caching.Distributed.IDistributedCache' while attempting to activate 'Microsoft.AspNetCore.Session.DistributedSessionStore'."</span></span>

  <span data-ttu-id="ce9ba-353">Geralmente, isso é causado quando não é configurada pelo menos uma implementação de `IDistributedCache`.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-353">This is usually caused by failing to configure at least one `IDistributedCache` implementation.</span></span> <span data-ttu-id="ce9ba-354">Para obter mais informações, consulte <xref:performance/caching/distributed> e <xref:performance/caching/memory>.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-354">For more information, see <xref:performance/caching/distributed> and <xref:performance/caching/memory>.</span></span>

* <span data-ttu-id="ce9ba-355">Caso a sessão de middleware não consiga manter uma sessão (por exemplo, se o repositório de backup não estiver disponível), o middleware registrará a exceção e a solicitação continuará normalmente.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-355">In the event that the session middleware fails to persist a session (for example, if the backing store isn't available), the middleware logs the exception and the request continues normally.</span></span> <span data-ttu-id="ce9ba-356">Isso leva a um comportamento imprevisível.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-356">This leads to unpredictable behavior.</span></span>

  <span data-ttu-id="ce9ba-357">Por exemplo, um usuário armazena um carrinho de compras na sessão.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-357">For example, a user stores a shopping cart in session.</span></span> <span data-ttu-id="ce9ba-358">O usuário adiciona um item ao carrinho, mas a confirmação falha.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-358">The user adds an item to the cart but the commit fails.</span></span> <span data-ttu-id="ce9ba-359">O aplicativo não sabe sobre a falha, assim, relata ao usuário que o item foi adicionado ao seu carrinho, o que não é verdade.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-359">The app doesn't know about the failure so it reports to the user that the item was added to their cart, which isn't true.</span></span>

  <span data-ttu-id="ce9ba-360">A abordagem recomendada para verificar se há erros é chamar `await feature.Session.CommitAsync();` do código de aplicativo quando o aplicativo tiver terminado de gravar na sessão.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-360">The recommended approach to check for errors is to call `await feature.Session.CommitAsync();` from app code when the app is done writing to the session.</span></span> <span data-ttu-id="ce9ba-361">`CommitAsync` gerará uma exceção se o repositório de backup não estiver disponível.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-361">`CommitAsync` throws an exception if the backing store is unavailable.</span></span> <span data-ttu-id="ce9ba-362">Se `CommitAsync` falhar, o aplicativo poderá processar a exceção.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-362">If `CommitAsync` fails, the app can process the exception.</span></span> <span data-ttu-id="ce9ba-363">`LoadAsync` gera sob as mesmas condições em que o armazenamento de dados não está disponível.</span><span class="sxs-lookup"><span data-stu-id="ce9ba-363">`LoadAsync` throws under the same conditions where the data store is unavailable.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ce9ba-364">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="ce9ba-364">Additional resources</span></span>

<xref:host-and-deploy/web-farm>
