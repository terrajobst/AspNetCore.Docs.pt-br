---
title: Gerenciamento de estado de Blazor ASP.NET Core
author: guardrex
description: Saiba como persistir o estado em aplicativos do Blazor Server.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- Blazor
- SignalR
uid: blazor/state-management
ms.openlocfilehash: e8a1959a8fc05ea59362bb5824181a9d2e418811
ms.sourcegitcommit: 91dc1dd3d055b4c7d7298420927b3fd161067c64
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2020
ms.locfileid: "80218863"
---
# <a name="aspnet-core-opno-locblazor-state-management"></a><span data-ttu-id="fd757-103">Gerenciamento de estado de Blazor ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fd757-103">ASP.NET Core Blazor state management</span></span>

<span data-ttu-id="fd757-104">Por [Steve Sanderson](https://github.com/SteveSandersonMS)</span><span class="sxs-lookup"><span data-stu-id="fd757-104">By [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="fd757-105">o Blazor Server é uma estrutura de aplicativo com estado.</span><span class="sxs-lookup"><span data-stu-id="fd757-105">Blazor Server is a stateful app framework.</span></span> <span data-ttu-id="fd757-106">Na maioria das vezes, o aplicativo mantém uma conexão contínua com o servidor.</span><span class="sxs-lookup"><span data-stu-id="fd757-106">Most of the time, the app maintains an ongoing connection to the server.</span></span> <span data-ttu-id="fd757-107">O estado do usuário é mantido na memória do servidor em um *circuito*.</span><span class="sxs-lookup"><span data-stu-id="fd757-107">The user's state is held in the server's memory in a *circuit*.</span></span> 

<span data-ttu-id="fd757-108">Exemplos de estado mantido para o circuito de um usuário incluem:</span><span class="sxs-lookup"><span data-stu-id="fd757-108">Examples of state held for a user's circuit include:</span></span>

* <span data-ttu-id="fd757-109">A interface do usuário renderizada&mdash;hierarquia de instâncias de componente e sua saída de renderização mais recente.</span><span class="sxs-lookup"><span data-stu-id="fd757-109">The rendered UI&mdash;the hierarchy of component instances and their most recent render output.</span></span>
* <span data-ttu-id="fd757-110">Os valores de quaisquer campos e propriedades em instâncias de componente.</span><span class="sxs-lookup"><span data-stu-id="fd757-110">The values of any fields and properties in component instances.</span></span>
* <span data-ttu-id="fd757-111">Dados mantidos em instâncias de serviço de [injeção de dependência (di)](xref:fundamentals/dependency-injection) que estão no escopo do circuito.</span><span class="sxs-lookup"><span data-stu-id="fd757-111">Data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span>

> [!NOTE]
> <span data-ttu-id="fd757-112">Este artigo aborda a persistência de estado em aplicativos do Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="fd757-112">This article addresses state persistence in Blazor Server apps.</span></span> Blazor<span data-ttu-id="fd757-113"> aplicativos Webassembly podem aproveitar [a persistência de estado do lado do cliente no navegador](#client-side-in-the-browser) , mas exigem soluções personalizadas ou pacotes de terceiros além do escopo deste artigo.</span><span class="sxs-lookup"><span data-stu-id="fd757-113"> WebAssembly apps can take advantage of [client-side state persistence in the browser](#client-side-in-the-browser) but require custom solutions or 3rd party packages beyond the scope of this article.</span></span>

## <a name="opno-locblazor-circuits"></a><span data-ttu-id="fd757-114">circuitos de Blazor</span><span class="sxs-lookup"><span data-stu-id="fd757-114">Blazor circuits</span></span>

<span data-ttu-id="fd757-115">Se um usuário tiver uma perda de conexão de rede temporária, Blazor tentar reconectar o usuário ao seu circuito original para que ele possa continuar a usar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="fd757-115">If a user experiences a temporary network connection loss, Blazor attempts to reconnect the user to their original circuit so they can continue to use the app.</span></span> <span data-ttu-id="fd757-116">No entanto, a reconexão de um usuário ao seu circuito original na memória do servidor nem sempre é possível:</span><span class="sxs-lookup"><span data-stu-id="fd757-116">However, reconnecting a user to their original circuit in the server's memory isn't always possible:</span></span>

* <span data-ttu-id="fd757-117">O servidor não pode manter um circuito desconectado para sempre.</span><span class="sxs-lookup"><span data-stu-id="fd757-117">The server can't retain a disconnected circuit forever.</span></span> <span data-ttu-id="fd757-118">O servidor deve liberar um circuito desconectado após um tempo limite ou quando o servidor está sob pressão de memória.</span><span class="sxs-lookup"><span data-stu-id="fd757-118">The server must release a disconnected circuit after a timeout or when the server is under memory pressure.</span></span>
* <span data-ttu-id="fd757-119">No multisservidor, ambientes de implantação com balanceamento de carga, qualquer solicitação de processamento de servidor pode se tornar indisponível em um determinado momento.</span><span class="sxs-lookup"><span data-stu-id="fd757-119">In multiserver, load-balanced deployment environments, any server processing requests may become unavailable at any given time.</span></span> <span data-ttu-id="fd757-120">Os servidores individuais podem falhar ou ser removidos automaticamente quando não forem mais necessários para lidar com o volume geral de solicitações.</span><span class="sxs-lookup"><span data-stu-id="fd757-120">Individual servers may fail or be automatically removed when no longer required to handle the overall volume of requests.</span></span> <span data-ttu-id="fd757-121">O servidor original pode não estar disponível quando o usuário tenta se reconectar.</span><span class="sxs-lookup"><span data-stu-id="fd757-121">The original server may not be available when the user attempts to reconnect.</span></span>
* <span data-ttu-id="fd757-122">O usuário pode fechar e reabrir o navegador ou recarregar a página, o que remove qualquer estado mantido na memória do navegador.</span><span class="sxs-lookup"><span data-stu-id="fd757-122">The user might close and re-open their browser or reload the page, which removes any state held in the browser's memory.</span></span> <span data-ttu-id="fd757-123">Por exemplo, os valores definidos por meio de chamadas de interoperabilidade do JavaScript são perdidos.</span><span class="sxs-lookup"><span data-stu-id="fd757-123">For example, values set through JavaScript interop calls are lost.</span></span>

<span data-ttu-id="fd757-124">Quando um usuário não pode ser reconectado ao seu circuito original, o usuário recebe um novo circuito com um estado vazio.</span><span class="sxs-lookup"><span data-stu-id="fd757-124">When a user can't be reconnected to their original circuit, the user receives a new circuit with an empty state.</span></span> <span data-ttu-id="fd757-125">Isso é equivalente a fechar e reabrir um aplicativo de área de trabalho.</span><span class="sxs-lookup"><span data-stu-id="fd757-125">This is equivalent to closing and re-opening a desktop app.</span></span>

## <a name="preserve-state-across-circuits"></a><span data-ttu-id="fd757-126">Preservar estado entre circuitos</span><span class="sxs-lookup"><span data-stu-id="fd757-126">Preserve state across circuits</span></span>

<span data-ttu-id="fd757-127">Em alguns cenários, a preservação do estado entre circuitos é desejável.</span><span class="sxs-lookup"><span data-stu-id="fd757-127">In some scenarios, preserving state across circuits is desirable.</span></span> <span data-ttu-id="fd757-128">Um aplicativo pode reter dados importantes para um usuário se:</span><span class="sxs-lookup"><span data-stu-id="fd757-128">An app can retain important data for a user if:</span></span>

* <span data-ttu-id="fd757-129">O servidor Web fica indisponível.</span><span class="sxs-lookup"><span data-stu-id="fd757-129">The web server becomes unavailable.</span></span>
* <span data-ttu-id="fd757-130">O navegador do usuário é forçado a iniciar um novo circuito com um novo servidor Web.</span><span class="sxs-lookup"><span data-stu-id="fd757-130">The user's browser is forced to start a new circuit with a new web server.</span></span>

<span data-ttu-id="fd757-131">Em geral, a manutenção de estado entre circuitos se aplica a cenários em que os usuários estão criando dados ativamente, não simplesmente lendo dados já existentes.</span><span class="sxs-lookup"><span data-stu-id="fd757-131">In general, maintaining state across circuits applies to scenarios where users are actively creating data, not simply reading data that already exists.</span></span>

<span data-ttu-id="fd757-132">Para preservar o estado além de um único circuito, *não armazene apenas os dados na memória do servidor*.</span><span class="sxs-lookup"><span data-stu-id="fd757-132">To preserve state beyond a single circuit, *don't merely store the data in the server's memory*.</span></span> <span data-ttu-id="fd757-133">O aplicativo deve manter os dados em algum outro local de armazenamento.</span><span class="sxs-lookup"><span data-stu-id="fd757-133">The app must persist the data to some other storage location.</span></span> <span data-ttu-id="fd757-134">A persistência de estado não é automática&mdash;você deve executar etapas ao desenvolver o aplicativo para implementar a persistência de dados com estado.</span><span class="sxs-lookup"><span data-stu-id="fd757-134">State persistence isn't automatic&mdash;you must take steps when developing the app to implement stateful data persistence.</span></span>

<span data-ttu-id="fd757-135">A persistência de dados normalmente é necessária apenas para o estado de alto valor que os usuários gastaram esforços para criar.</span><span class="sxs-lookup"><span data-stu-id="fd757-135">Data persistence is typically only required for high-value state that users have expended effort to create.</span></span> <span data-ttu-id="fd757-136">Nos exemplos a seguir, o estado de persistência economiza tempo ou ajuda em atividades comerciais:</span><span class="sxs-lookup"><span data-stu-id="fd757-136">In the following examples, persisting state either saves time or aids in commercial activities:</span></span>

* <span data-ttu-id="fd757-137">Formulários da webstep &ndash; é demorado para um usuário reinserir dados para várias etapas concluídas de um processo com vários passos se seu estado for perdido.</span><span class="sxs-lookup"><span data-stu-id="fd757-137">Multistep webform &ndash; It's time-consuming for a user to re-enter data for several completed steps of a multistep process if their state is lost.</span></span> <span data-ttu-id="fd757-138">Um usuário perde o estado nesse cenário se ele navega para fora do formulário com várias etapas e retorna para o formulário mais tarde.</span><span class="sxs-lookup"><span data-stu-id="fd757-138">A user loses state in this scenario if they navigate away from the multistep form and return to the form later.</span></span>
* <span data-ttu-id="fd757-139">O carrinho de compras &ndash; qualquer componente comercialmente importante de um aplicativo que representa a possível receita pode ser mantido.</span><span class="sxs-lookup"><span data-stu-id="fd757-139">Shopping cart &ndash; Any commercially important component of an app that represents potential revenue can be maintained.</span></span> <span data-ttu-id="fd757-140">Um usuário que perde seu estado e, portanto, seu carrinho de compras, pode comprar menos produtos ou serviços quando eles retornam para o site mais tarde.</span><span class="sxs-lookup"><span data-stu-id="fd757-140">A user who loses their state, and thus their shopping cart, may purchase fewer products or services when they return to the site later.</span></span>

<span data-ttu-id="fd757-141">Normalmente, não é necessário preservar o estado de recriação fácil, como o nome de usuário inserido em uma caixa de diálogo de entrada que não foi enviada.</span><span class="sxs-lookup"><span data-stu-id="fd757-141">It's usually not necessary to preserve easily-recreated state, such as the username entered into a sign-in dialog that hasn't been submitted.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="fd757-142">Um aplicativo só pode persistir o *estado do aplicativo*.</span><span class="sxs-lookup"><span data-stu-id="fd757-142">An app can only persist *app state*.</span></span> <span data-ttu-id="fd757-143">As UIs não podem ser persistentes, como instâncias de componente e suas árvores de renderização.</span><span class="sxs-lookup"><span data-stu-id="fd757-143">UIs can't be persisted, such as component instances and their render trees.</span></span> <span data-ttu-id="fd757-144">Os componentes e as árvores de renderização geralmente não são serializáveis.</span><span class="sxs-lookup"><span data-stu-id="fd757-144">Components and render trees aren't generally serializable.</span></span> <span data-ttu-id="fd757-145">Para persistir algo semelhante ao estado da interface do usuário, como os nós expandidos de um TreeView, o aplicativo deve ter um código personalizado para modelar o comportamento como um estado do aplicativo serializável.</span><span class="sxs-lookup"><span data-stu-id="fd757-145">To persist something similar to UI state, such as the expanded nodes of a TreeView, the app must have custom code to model the behavior as serializable app state.</span></span>

## <a name="where-to-persist-state"></a><span data-ttu-id="fd757-146">Onde persistir o estado</span><span class="sxs-lookup"><span data-stu-id="fd757-146">Where to persist state</span></span>

<span data-ttu-id="fd757-147">Existem três localizações comuns para o estado persistente em um aplicativo do Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="fd757-147">Three common locations exist for persisting state in a Blazor Server app.</span></span> <span data-ttu-id="fd757-148">Cada abordagem é mais adequada para cenários diferentes e tem diferentes limitações:</span><span class="sxs-lookup"><span data-stu-id="fd757-148">Each approach is best suited to different scenarios and has different caveats:</span></span>

* [<span data-ttu-id="fd757-149">Lado do servidor em um banco de dados</span><span class="sxs-lookup"><span data-stu-id="fd757-149">Server-side in a database</span></span>](#server-side-in-a-database)
* [<span data-ttu-id="fd757-150">URL</span><span class="sxs-lookup"><span data-stu-id="fd757-150">URL</span></span>](#url)
* [<span data-ttu-id="fd757-151">Lado do cliente no navegador</span><span class="sxs-lookup"><span data-stu-id="fd757-151">Client-side in the browser</span></span>](#client-side-in-the-browser)

### <a name="server-side-in-a-database"></a><span data-ttu-id="fd757-152">Lado do servidor em um banco de dados</span><span class="sxs-lookup"><span data-stu-id="fd757-152">Server-side in a database</span></span>

<span data-ttu-id="fd757-153">Para persistência de dados permanentes ou para quaisquer dados que devam abranger vários usuários ou dispositivos, um banco de dado independente do servidor é quase certamente a melhor opção.</span><span class="sxs-lookup"><span data-stu-id="fd757-153">For permanent data persistence or for any data that must span multiple users or devices, an independent server-side database is almost certainly the best choice.</span></span> <span data-ttu-id="fd757-154">As opções incluem:</span><span class="sxs-lookup"><span data-stu-id="fd757-154">Options include:</span></span>

* <span data-ttu-id="fd757-155">Banco de dados SQL relacional</span><span class="sxs-lookup"><span data-stu-id="fd757-155">Relational SQL database</span></span>
* <span data-ttu-id="fd757-156">Repositório de valor-chave</span><span class="sxs-lookup"><span data-stu-id="fd757-156">Key-value store</span></span>
* <span data-ttu-id="fd757-157">Repositório de BLOB</span><span class="sxs-lookup"><span data-stu-id="fd757-157">Blob store</span></span>
* <span data-ttu-id="fd757-158">Repositório de tabelas</span><span class="sxs-lookup"><span data-stu-id="fd757-158">Table store</span></span>

<span data-ttu-id="fd757-159">Depois que os dados são salvos no banco de dado, um novo circuito pode ser iniciado por um usuário a qualquer momento.</span><span class="sxs-lookup"><span data-stu-id="fd757-159">After data is saved in the database, a new circuit can be started by a user at any time.</span></span> <span data-ttu-id="fd757-160">Os dados do usuário são mantidos e estão disponíveis em qualquer circuito novo.</span><span class="sxs-lookup"><span data-stu-id="fd757-160">The user's data is retained and available in any new circuit.</span></span>

<span data-ttu-id="fd757-161">Para obter mais informações sobre as opções de armazenamento de dados do Azure, consulte a [documentação do armazenamento do Azure](/azure/storage/) e [os bancos de dados do Azure](https://azure.microsoft.com/product-categories/databases/).</span><span class="sxs-lookup"><span data-stu-id="fd757-161">For more information on Azure data storage options, see the [Azure Storage Documentation](/azure/storage/) and [Azure Databases](https://azure.microsoft.com/product-categories/databases/).</span></span>

### <a name="url"></a><span data-ttu-id="fd757-162">{1&gt;URL&lt;1}</span><span class="sxs-lookup"><span data-stu-id="fd757-162">URL</span></span>

<span data-ttu-id="fd757-163">Para dados transitórios que representam o estado de navegação, modele os dados como parte da URL.</span><span class="sxs-lookup"><span data-stu-id="fd757-163">For transient data representing navigation state, model the data as a part of the URL.</span></span> <span data-ttu-id="fd757-164">Exemplos de estado modelado na URL incluem:</span><span class="sxs-lookup"><span data-stu-id="fd757-164">Examples of state modeled in the URL include:</span></span>

* <span data-ttu-id="fd757-165">A ID de uma entidade exibida.</span><span class="sxs-lookup"><span data-stu-id="fd757-165">The ID of a viewed entity.</span></span>
* <span data-ttu-id="fd757-166">O número da página atual em uma grade paginável.</span><span class="sxs-lookup"><span data-stu-id="fd757-166">The current page number in a paged grid.</span></span>

<span data-ttu-id="fd757-167">O conteúdo da barra de endereços do navegador é mantido:</span><span class="sxs-lookup"><span data-stu-id="fd757-167">The contents of the browser's address bar are retained:</span></span>

* <span data-ttu-id="fd757-168">Se o usuário recarregar a página manualmente.</span><span class="sxs-lookup"><span data-stu-id="fd757-168">If the user manually reloads the page.</span></span>
* <span data-ttu-id="fd757-169">Se o servidor Web ficar indisponível&mdash;o usuário será forçado a recarregar a página para se conectar a um servidor diferente.</span><span class="sxs-lookup"><span data-stu-id="fd757-169">If the web server becomes unavailable&mdash;the user is forced to reload the page in order to connect to a different server.</span></span>

<span data-ttu-id="fd757-170">Para obter informações sobre como definir padrões de URL com a diretiva `@page`, consulte <xref:blazor/routing>.</span><span class="sxs-lookup"><span data-stu-id="fd757-170">For information on defining URL patterns with the `@page` directive, see <xref:blazor/routing>.</span></span>

### <a name="client-side-in-the-browser"></a><span data-ttu-id="fd757-171">Lado do cliente no navegador</span><span class="sxs-lookup"><span data-stu-id="fd757-171">Client-side in the browser</span></span>

<span data-ttu-id="fd757-172">Para dados transitórios que o usuário está criando ativamente, um repositório de backup comum é o `localStorage` do navegador e as coleções de `sessionStorage`.</span><span class="sxs-lookup"><span data-stu-id="fd757-172">For transient data that the user is actively creating, a common backing store is the browser's `localStorage` and `sessionStorage` collections.</span></span> <span data-ttu-id="fd757-173">O aplicativo não é necessário para gerenciar ou limpar o estado armazenado se o circuito for abandonado, o que é uma vantagem sobre o armazenamento do lado do servidor.</span><span class="sxs-lookup"><span data-stu-id="fd757-173">The app isn't required to manage or clear the stored state if the circuit is abandoned, which is an advantage over server-side storage.</span></span>

> [!NOTE]
> <span data-ttu-id="fd757-174">"Lado do cliente" nesta seção refere-se aos cenários do lado do cliente no navegador, não ao [modelo de hospedagem do WebassemblyBlazor](xref:blazor/hosting-models#blazor-webassembly).</span><span class="sxs-lookup"><span data-stu-id="fd757-174">"Client-side" in this section refers to client-side scenarios in the browser, not the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly).</span></span> <span data-ttu-id="fd757-175">`localStorage` e `sessionStorage` podem ser usados em Blazor aplicativos Webassembly, mas apenas escrevendo código personalizado ou usando um pacote de terceiros.</span><span class="sxs-lookup"><span data-stu-id="fd757-175">`localStorage` and `sessionStorage` can be used in Blazor WebAssembly apps but only by writing custom code or using a 3rd party package.</span></span>

<span data-ttu-id="fd757-176">`localStorage` e `sessionStorage` diferem da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="fd757-176">`localStorage` and `sessionStorage` differ as follows:</span></span>

* <span data-ttu-id="fd757-177">`localStorage` está no escopo do navegador do usuário.</span><span class="sxs-lookup"><span data-stu-id="fd757-177">`localStorage` is scoped to the user's browser.</span></span> <span data-ttu-id="fd757-178">Se o usuário recarregar a página ou fechar e abrir novamente o navegador, o estado persistirá.</span><span class="sxs-lookup"><span data-stu-id="fd757-178">If the user reloads the page or closes and re-opens the browser, the state persists.</span></span> <span data-ttu-id="fd757-179">Se o usuário abrir várias guias do navegador, o estado será compartilhado entre as guias.</span><span class="sxs-lookup"><span data-stu-id="fd757-179">If the user opens multiple browser tabs, the state is shared across the tabs.</span></span> <span data-ttu-id="fd757-180">Os dados persistem em `localStorage` até serem explicitamente desmarcados.</span><span class="sxs-lookup"><span data-stu-id="fd757-180">Data persists in `localStorage` until explicitly cleared.</span></span>
* <span data-ttu-id="fd757-181">`sessionStorage` está no escopo da guia do navegador do usuário. Se o usuário recarregar a guia, o estado persiste.</span><span class="sxs-lookup"><span data-stu-id="fd757-181">`sessionStorage` is scoped to the user's browser tab. If the user reloads the tab, the state persists.</span></span> <span data-ttu-id="fd757-182">Se o usuário fechar a guia ou o navegador, o estado será perdido.</span><span class="sxs-lookup"><span data-stu-id="fd757-182">If the user closes the tab or the browser, the state is lost.</span></span> <span data-ttu-id="fd757-183">Se o usuário abrir várias guias do navegador, cada guia terá sua própria versão independente dos dados.</span><span class="sxs-lookup"><span data-stu-id="fd757-183">If the user opens multiple browser tabs, each tab has its own independent version of the data.</span></span>

<span data-ttu-id="fd757-184">Em geral, `sessionStorage` é mais seguro de usar.</span><span class="sxs-lookup"><span data-stu-id="fd757-184">Generally, `sessionStorage` is safer to use.</span></span> <span data-ttu-id="fd757-185">`sessionStorage` evita o risco de um usuário abrir várias guias e encontrar o seguinte:</span><span class="sxs-lookup"><span data-stu-id="fd757-185">`sessionStorage` avoids the risk that a user opens multiple tabs and encounters the following:</span></span>

* <span data-ttu-id="fd757-186">Bugs no armazenamento de estado entre guias.</span><span class="sxs-lookup"><span data-stu-id="fd757-186">Bugs in state storage across tabs.</span></span>
* <span data-ttu-id="fd757-187">Comportamento confuso quando uma guia substitui o estado de outras guias.</span><span class="sxs-lookup"><span data-stu-id="fd757-187">Confusing behavior when a tab overwrites the state of other tabs.</span></span>

<span data-ttu-id="fd757-188">`localStorage` é a melhor opção se o aplicativo precisar manter o estado entre fechar e reabrir o navegador.</span><span class="sxs-lookup"><span data-stu-id="fd757-188">`localStorage` is the better choice if the app must persist state across closing and re-opening the browser.</span></span>

<span data-ttu-id="fd757-189">Advertências para usar o armazenamento de navegador:</span><span class="sxs-lookup"><span data-stu-id="fd757-189">Caveats for using browser storage:</span></span>

* <span data-ttu-id="fd757-190">Assim como o uso de um banco de dados do lado do servidor, o carregamento e o salvamento do data são assíncronos.</span><span class="sxs-lookup"><span data-stu-id="fd757-190">Similar to the use of a server-side database, loading and saving data are asynchronous.</span></span>
* <span data-ttu-id="fd757-191">Ao contrário de um banco de dados do lado do servidor, o armazenamento não está disponível durante o pré-processamento porque a página solicitada não existe no navegador durante o estágio de pré-processamento.</span><span class="sxs-lookup"><span data-stu-id="fd757-191">Unlike a server-side database, storage isn't available during prerendering because the requested page doesn't exist in the browser during the prerendering stage.</span></span>
* <span data-ttu-id="fd757-192">O armazenamento de alguns kilobytes de dados é razoável para persistir para os aplicativos do Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="fd757-192">Storage of a few kilobytes of data is reasonable to persist for Blazor Server apps.</span></span> <span data-ttu-id="fd757-193">Além de alguns quilobytes, você deve considerar as implicações de desempenho porque os dados são carregados e salvos na rede.</span><span class="sxs-lookup"><span data-stu-id="fd757-193">Beyond a few kilobytes, you must consider the performance implications because the data is loaded and saved across the network.</span></span>
* <span data-ttu-id="fd757-194">Os usuários podem exibir ou adulterar os dados.</span><span class="sxs-lookup"><span data-stu-id="fd757-194">Users may view or tamper with the data.</span></span> <span data-ttu-id="fd757-195">ASP.NET Core [proteção de dados](xref:security/data-protection/introduction) pode reduzir o risco.</span><span class="sxs-lookup"><span data-stu-id="fd757-195">ASP.NET Core [Data Protection](xref:security/data-protection/introduction) can mitigate the risk.</span></span>

## <a name="third-party-browser-storage-solutions"></a><span data-ttu-id="fd757-196">Soluções de armazenamento de navegadores de terceiros</span><span class="sxs-lookup"><span data-stu-id="fd757-196">Third-party browser storage solutions</span></span>

<span data-ttu-id="fd757-197">Os pacotes NuGet de terceiros fornecem APIs para trabalhar com `localStorage` e `sessionStorage`.</span><span class="sxs-lookup"><span data-stu-id="fd757-197">Third-party NuGet packages provide APIs for working with `localStorage` and `sessionStorage`.</span></span>

<span data-ttu-id="fd757-198">Vale a pena considerar a escolha de um pacote que usa de forma transparente a [proteção de dados](xref:security/data-protection/introduction)de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fd757-198">It's worth considering choosing a package that transparently uses ASP.NET Core's [Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="fd757-199">ASP.NET Core proteção de dados criptografa os dados armazenados e reduz o risco potencial de adulterar os dados armazenados.</span><span class="sxs-lookup"><span data-stu-id="fd757-199">ASP.NET Core Data Protection encrypts stored data and reduces the potential risk of tampering with stored data.</span></span> <span data-ttu-id="fd757-200">Se os dados serializados em JSON forem armazenados em texto não criptografado, os usuários poderão ver os dados usando as ferramentas de desenvolvedor do navegador e também modificar os dados armazenados.</span><span class="sxs-lookup"><span data-stu-id="fd757-200">If JSON-serialized data is stored in plaintext, users can see the data using browser developer tools and also modify the stored data.</span></span> <span data-ttu-id="fd757-201">Proteger os dados nem sempre é um problema porque os dados podem ser triviais por natureza.</span><span class="sxs-lookup"><span data-stu-id="fd757-201">Securing data isn't always a problem because the data might be trivial in nature.</span></span> <span data-ttu-id="fd757-202">Por exemplo, ler ou modificar a cor armazenada de um elemento de interface do usuário não é um risco de segurança significativo para o usuário ou a organização.</span><span class="sxs-lookup"><span data-stu-id="fd757-202">For example, reading or modifying the stored color of a UI element isn't a significant security risk to the user or the organization.</span></span> <span data-ttu-id="fd757-203">Evite permitir que os usuários inspecionem ou violem *dados confidenciais*.</span><span class="sxs-lookup"><span data-stu-id="fd757-203">Avoid allowing users to inspect or tamper with *sensitive data*.</span></span>

## <a name="protected-browser-storage-experimental-package"></a><span data-ttu-id="fd757-204">Pacote experimental de armazenamento de navegador protegido</span><span class="sxs-lookup"><span data-stu-id="fd757-204">Protected Browser Storage experimental package</span></span>

<span data-ttu-id="fd757-205">Um exemplo de um pacote NuGet que fornece [proteção de dados](xref:security/data-protection/introduction) para `localStorage` e `sessionStorage` é [Microsoft. AspNetCore. ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span><span class="sxs-lookup"><span data-stu-id="fd757-205">An example of a NuGet package that provides [Data Protection](xref:security/data-protection/introduction) for `localStorage` and `sessionStorage` is [Microsoft.AspNetCore.ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span></span>

> [!WARNING]
> <span data-ttu-id="fd757-206">o `Microsoft.AspNetCore.ProtectedBrowserStorage` é um pacote experimental sem suporte não adequado para uso em produção no momento.</span><span class="sxs-lookup"><span data-stu-id="fd757-206">`Microsoft.AspNetCore.ProtectedBrowserStorage` is an unsupported experimental package unsuitable for production use at this time.</span></span>

### <a name="installation"></a><span data-ttu-id="fd757-207">Instalação</span><span class="sxs-lookup"><span data-stu-id="fd757-207">Installation</span></span>

<span data-ttu-id="fd757-208">Para instalar o pacote de `Microsoft.AspNetCore.ProtectedBrowserStorage`:</span><span class="sxs-lookup"><span data-stu-id="fd757-208">To install the `Microsoft.AspNetCore.ProtectedBrowserStorage` package:</span></span>

1. <span data-ttu-id="fd757-209">No projeto de aplicativo Blazor Server, adicione uma referência de pacote a [Microsoft. AspNetCore. ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span><span class="sxs-lookup"><span data-stu-id="fd757-209">In the Blazor Server app project, add a package reference to [Microsoft.AspNetCore.ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span></span>
1. <span data-ttu-id="fd757-210">No HTML de nível superior (por exemplo, no arquivo *pages/_Host. cshtml* no modelo de projeto padrão), adicione a seguinte marca de `<script>`:</span><span class="sxs-lookup"><span data-stu-id="fd757-210">In the top-level HTML (for example, in the *Pages/_Host.cshtml* file in the default project template), add the following `<script>` tag:</span></span>

   ```html
   <script src="_content/Microsoft.AspNetCore.ProtectedBrowserStorage/protectedBrowserStorage.js"></script>
   ```

1. <span data-ttu-id="fd757-211">No método `Startup.ConfigureServices`, chame `AddProtectedBrowserStorage` para adicionar os serviços `localStorage` e `sessionStorage` à coleção de serviços:</span><span class="sxs-lookup"><span data-stu-id="fd757-211">In the `Startup.ConfigureServices` method, call `AddProtectedBrowserStorage` to add `localStorage` and `sessionStorage` services to the service collection:</span></span>

   ```csharp
   services.AddProtectedBrowserStorage();
   ```

### <a name="save-and-load-data-within-a-component"></a><span data-ttu-id="fd757-212">Salvar e carregar dados dentro de um componente</span><span class="sxs-lookup"><span data-stu-id="fd757-212">Save and load data within a component</span></span>

<span data-ttu-id="fd757-213">Em qualquer componente que exija carregar ou salvar dados no armazenamento de navegador, use [`@inject`](xref:blazor/dependency-injection#request-a-service-in-a-component) para injetar uma instância de um dos seguintes:</span><span class="sxs-lookup"><span data-stu-id="fd757-213">In any component that requires loading or saving data to browser storage, use [`@inject`](xref:blazor/dependency-injection#request-a-service-in-a-component) to inject an instance of either of the following:</span></span>

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

<span data-ttu-id="fd757-214">A escolha depende de qual repositório de backup você deseja usar.</span><span class="sxs-lookup"><span data-stu-id="fd757-214">The choice depends on which backing store you wish to use.</span></span> <span data-ttu-id="fd757-215">No exemplo a seguir, `sessionStorage` é usado:</span><span class="sxs-lookup"><span data-stu-id="fd757-215">In the following example, `sessionStorage` is used:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

<span data-ttu-id="fd757-216">A instrução `@using` pode ser colocada em um arquivo *_Imports. Razor* em vez de no componente.</span><span class="sxs-lookup"><span data-stu-id="fd757-216">The `@using` statement can be placed into an *_Imports.razor* file instead of in the component.</span></span> <span data-ttu-id="fd757-217">O uso do arquivo *_Imports. Razor* torna o namespace disponível para segmentos maiores do aplicativo ou todo o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="fd757-217">Use of the *_Imports.razor* file makes the namespace available to larger segments of the app or the whole app.</span></span>

<span data-ttu-id="fd757-218">Para manter o valor `_currentCount` no componente `Counter` do modelo de projeto, modifique o método `IncrementCount` para usar `ProtectedSessionStore.SetAsync`:</span><span class="sxs-lookup"><span data-stu-id="fd757-218">To persist the `_currentCount` value in the `Counter` component of the project template, modify the `IncrementCount` method to use `ProtectedSessionStore.SetAsync`:</span></span>

```csharp
private async Task IncrementCount()
{
    _currentCount++;
    await ProtectedSessionStore.SetAsync("count", _currentCount);
}
```

<span data-ttu-id="fd757-219">Em aplicativos maiores e mais realistas, o armazenamento de campos individuais é um cenário improvável.</span><span class="sxs-lookup"><span data-stu-id="fd757-219">In larger, more realistic apps, storage of individual fields is an unlikely scenario.</span></span> <span data-ttu-id="fd757-220">Os aplicativos têm maior probabilidade de armazenar objetos de modelo inteiros que incluem estado complexo.</span><span class="sxs-lookup"><span data-stu-id="fd757-220">Apps are more likely to store entire model objects that include complex state.</span></span> <span data-ttu-id="fd757-221">`ProtectedSessionStore` serializa e desserializa automaticamente os dados JSON.</span><span class="sxs-lookup"><span data-stu-id="fd757-221">`ProtectedSessionStore` automatically serializes and deserializes JSON data.</span></span>

<span data-ttu-id="fd757-222">No exemplo de código anterior, os dados de `_currentCount` são armazenados como `sessionStorage['count']` no navegador do usuário.</span><span class="sxs-lookup"><span data-stu-id="fd757-222">In the preceding code example, the `_currentCount` data is stored as `sessionStorage['count']` in the user's browser.</span></span> <span data-ttu-id="fd757-223">Os dados não são armazenados em texto sem formatação, mas, em vez disso, são protegidos usando a [proteção de dados](xref:security/data-protection/introduction)do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fd757-223">The data isn't stored in plaintext but rather is protected using ASP.NET Core's [Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="fd757-224">Os dados criptografados podem ser vistos se `sessionStorage['count']` for avaliado no console do desenvolvedor do navegador.</span><span class="sxs-lookup"><span data-stu-id="fd757-224">The encrypted data can be seen if `sessionStorage['count']` is evaluated in the browser's developer console.</span></span>

<span data-ttu-id="fd757-225">Para recuperar os dados de `_currentCount` se o usuário retornar ao componente `Counter` posteriormente (incluindo se eles estiverem em um circuito totalmente novo), use `ProtectedSessionStore.GetAsync`:</span><span class="sxs-lookup"><span data-stu-id="fd757-225">To recover the `_currentCount` data if the user returns to the `Counter` component later (including if they're on an entirely new circuit), use `ProtectedSessionStore.GetAsync`:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    _currentCount = await ProtectedSessionStore.GetAsync<int>("count");
}
```

<span data-ttu-id="fd757-226">Se os parâmetros do componente incluírem o estado de navegação, chame `ProtectedSessionStore.GetAsync` e atribua o resultado em `OnParametersSetAsync`, não `OnInitializedAsync`.</span><span class="sxs-lookup"><span data-stu-id="fd757-226">If the component's parameters include navigation state, call `ProtectedSessionStore.GetAsync` and assign the result in `OnParametersSetAsync`, not `OnInitializedAsync`.</span></span> <span data-ttu-id="fd757-227">`OnInitializedAsync` é chamado apenas uma vez quando o componente é instanciado primeiro.</span><span class="sxs-lookup"><span data-stu-id="fd757-227">`OnInitializedAsync` is only called one time when the component is first instantiated.</span></span> <span data-ttu-id="fd757-228">`OnInitializedAsync` não será chamado novamente mais tarde se o usuário navegar para uma URL diferente enquanto permanece na mesma página.</span><span class="sxs-lookup"><span data-stu-id="fd757-228">`OnInitializedAsync` isn't called again later if the user navigates to a different URL while remaining on the same page.</span></span> <span data-ttu-id="fd757-229">Para obter mais informações, consulte <xref:blazor/lifecycle>.</span><span class="sxs-lookup"><span data-stu-id="fd757-229">For more information, see <xref:blazor/lifecycle>.</span></span>

> [!WARNING]
> <span data-ttu-id="fd757-230">Os exemplos nesta seção só funcionarão se o servidor não tiver o pré-processamento habilitado.</span><span class="sxs-lookup"><span data-stu-id="fd757-230">The examples in this section only work if the server doesn't have prerendering enabled.</span></span> <span data-ttu-id="fd757-231">Com o pré-processamento habilitado, um erro é gerado semelhante a:</span><span class="sxs-lookup"><span data-stu-id="fd757-231">With prerendering enabled, an error is generated similar to:</span></span>
>
> > <span data-ttu-id="fd757-232">As chamadas de interoperabilidade do JavaScript não podem ser emitidas neste momento.</span><span class="sxs-lookup"><span data-stu-id="fd757-232">JavaScript interop calls cannot be issued at this time.</span></span> <span data-ttu-id="fd757-233">Isso ocorre porque o componente está sendo renderizado.</span><span class="sxs-lookup"><span data-stu-id="fd757-233">This is because the component is being prerendered.</span></span>
>
> <span data-ttu-id="fd757-234">Desabilite o pré-processamento ou adicione código adicional para trabalhar com o pré-processamento.</span><span class="sxs-lookup"><span data-stu-id="fd757-234">Either disable prerendering or add additional code to work with prerendering.</span></span> <span data-ttu-id="fd757-235">Para saber mais sobre como escrever código que funciona com o pré-processamento, consulte a seção [manipular pré-processamento](#handle-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="fd757-235">To learn more about writing code that works with prerendering, see the [Handle prerendering](#handle-prerendering) section.</span></span>

### <a name="handle-the-loading-state"></a><span data-ttu-id="fd757-236">Manipular o estado de carregamento</span><span class="sxs-lookup"><span data-stu-id="fd757-236">Handle the loading state</span></span>

<span data-ttu-id="fd757-237">Como o armazenamento de navegador é assíncrono (acessado através de uma conexão de rede), sempre há um período de tempo antes que os dados sejam carregados e estejam disponíveis para uso por um componente.</span><span class="sxs-lookup"><span data-stu-id="fd757-237">Since browser storage is asynchronous (accessed over a network connection), there's always a period of time before the data is loaded and available for use by a component.</span></span> <span data-ttu-id="fd757-238">Para obter os melhores resultados, processe uma mensagem de estado de carregamento durante o carregamento em andamento, em vez de exibir dados em branco ou padrão.</span><span class="sxs-lookup"><span data-stu-id="fd757-238">For the best results, render a loading-state message while loading is in progress instead of displaying blank or default data.</span></span>

<span data-ttu-id="fd757-239">Uma abordagem é controlar se os dados estão `null` (ainda carregando) ou não.</span><span class="sxs-lookup"><span data-stu-id="fd757-239">One approach is to track whether the data is `null` (still loading) or not.</span></span> <span data-ttu-id="fd757-240">No componente de `Counter` padrão, a contagem é mantida em um `int`.</span><span class="sxs-lookup"><span data-stu-id="fd757-240">In the default `Counter` component, the count is held in an `int`.</span></span> <span data-ttu-id="fd757-241">Torne `_currentCount` anulável adicionando um ponto de interrogação (`?`) ao tipo (`int`):</span><span class="sxs-lookup"><span data-stu-id="fd757-241">Make `_currentCount` nullable by adding a question mark (`?`) to the type (`int`):</span></span>

```csharp
private int? _currentCount;
```

<span data-ttu-id="fd757-242">Em vez de exibir incondicionalmente o botão contagem e **incremento** , escolha exibir esses elementos somente se os dados forem carregados:</span><span class="sxs-lookup"><span data-stu-id="fd757-242">Instead of unconditionally displaying the count and **Increment** button, choose to display these elements only if the data is loaded:</span></span>

```razor
@if (_currentCount.HasValue)
{
    <p>Current count: <strong>@_currentCount</strong></p>

    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}
```

### <a name="handle-prerendering"></a><span data-ttu-id="fd757-243">Processar pré-processamento</span><span class="sxs-lookup"><span data-stu-id="fd757-243">Handle prerendering</span></span>

<span data-ttu-id="fd757-244">Durante o pré-processamento:</span><span class="sxs-lookup"><span data-stu-id="fd757-244">During prerendering:</span></span>

* <span data-ttu-id="fd757-245">Não existe uma conexão interativa com o navegador do usuário.</span><span class="sxs-lookup"><span data-stu-id="fd757-245">An interactive connection to the user's browser doesn't exist.</span></span>
* <span data-ttu-id="fd757-246">O navegador ainda não tem uma página na qual possa executar código JavaScript.</span><span class="sxs-lookup"><span data-stu-id="fd757-246">The browser doesn't yet have a page in which it can run JavaScript code.</span></span>

<span data-ttu-id="fd757-247">`localStorage` ou `sessionStorage` não estão disponíveis durante o pré-processamento.</span><span class="sxs-lookup"><span data-stu-id="fd757-247">`localStorage` or `sessionStorage` aren't available during prerendering.</span></span> <span data-ttu-id="fd757-248">Se o componente tentar interagir com o armazenamento, um erro será gerado semelhante a:</span><span class="sxs-lookup"><span data-stu-id="fd757-248">If the component attempts to interact with storage, an error is generated similar to:</span></span>

> <span data-ttu-id="fd757-249">As chamadas de interoperabilidade do JavaScript não podem ser emitidas neste momento.</span><span class="sxs-lookup"><span data-stu-id="fd757-249">JavaScript interop calls cannot be issued at this time.</span></span> <span data-ttu-id="fd757-250">Isso ocorre porque o componente está sendo renderizado.</span><span class="sxs-lookup"><span data-stu-id="fd757-250">This is because the component is being prerendered.</span></span>

<span data-ttu-id="fd757-251">Uma maneira de resolver o erro é desabilitar o pré-processamento.</span><span class="sxs-lookup"><span data-stu-id="fd757-251">One way to resolve the error is to disable prerendering.</span></span> <span data-ttu-id="fd757-252">Normalmente, essa é a melhor opção se o aplicativo fizer uso intensivo de armazenamento baseado em navegador.</span><span class="sxs-lookup"><span data-stu-id="fd757-252">This is usually the best choice if the app makes heavy use of browser-based storage.</span></span> <span data-ttu-id="fd757-253">O pré-processamento adiciona complexidade e não beneficia o aplicativo porque o aplicativo não pode colocar nenhum conteúdo útil antes que `localStorage` ou `sessionStorage` estejam disponíveis.</span><span class="sxs-lookup"><span data-stu-id="fd757-253">Prerendering adds complexity and doesn't benefit the app because the app can't prerender any useful content until `localStorage` or `sessionStorage` are available.</span></span>

<span data-ttu-id="fd757-254">Para desabilitar o pré-processamento, abra o arquivo *pages/_Host. cshtml* e altere o `render-mode` do auxiliar de [marca de componente](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) para <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>.</span><span class="sxs-lookup"><span data-stu-id="fd757-254">To disable prerendering, open the *Pages/_Host.cshtml* file and change the `render-mode` of the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) to <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>.</span></span>

<span data-ttu-id="fd757-255">O pré-processamento pode ser útil para outras páginas que não usam `localStorage` ou `sessionStorage`.</span><span class="sxs-lookup"><span data-stu-id="fd757-255">Prerendering might be useful for other pages that don't use `localStorage` or `sessionStorage`.</span></span> <span data-ttu-id="fd757-256">Para manter o pré-processamento habilitado, adie a operação de carregamento até que o navegador esteja conectado ao circuito.</span><span class="sxs-lookup"><span data-stu-id="fd757-256">To keep prerendering enabled, defer the loading operation until the browser is connected to the circuit.</span></span> <span data-ttu-id="fd757-257">Veja a seguir um exemplo de como armazenar um valor de contador:</span><span class="sxs-lookup"><span data-stu-id="fd757-257">The following is an example for storing a counter value:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedLocalStorage ProtectedLocalStore

... rendering code goes here ...

@code {
    private int? _currentCount;
    private bool _isConnected = false;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            // When execution reaches this point, the first *interactive* render
            // is complete. The component has an active connection to the browser.
            _isConnected = true;
            await LoadStateAsync();
            StateHasChanged();
        }
    }

    private async Task LoadStateAsync()
    {
        _currentCount = await ProtectedLocalStore.GetAsync<int>("prerenderedCount");
    }

    private async Task IncrementCount()
    {
        _currentCount++;
        await ProtectedSessionStore.SetAsync("count", _currentCount);
    }
}
```

### <a name="factor-out-the-state-preservation-to-a-common-location"></a><span data-ttu-id="fd757-258">Fatorar a preservação do estado para um local comum</span><span class="sxs-lookup"><span data-stu-id="fd757-258">Factor out the state preservation to a common location</span></span>

<span data-ttu-id="fd757-259">Se muitos componentes dependem do armazenamento baseado em navegador, a nova implementação do código do provedor de estado muitas vezes cria a duplicação de código.</span><span class="sxs-lookup"><span data-stu-id="fd757-259">If many components rely on browser-based storage, re-implementing state provider code many times creates code duplication.</span></span> <span data-ttu-id="fd757-260">Uma opção para evitar a duplicação de código é criar um *componente pai do provedor de estado* que encapsula a lógica do provedor de estado.</span><span class="sxs-lookup"><span data-stu-id="fd757-260">One option for avoiding code duplication is to create a *state provider parent component* that encapsulates the state provider logic.</span></span> <span data-ttu-id="fd757-261">Os componentes filho podem trabalhar com dados persistentes sem considerar o mecanismo de persistência de estado.</span><span class="sxs-lookup"><span data-stu-id="fd757-261">Child components can work with persisted data without regard to the state persistence mechanism.</span></span>

<span data-ttu-id="fd757-262">No exemplo a seguir de um componente `CounterStateProvider`, os dados do contador são persistidos:</span><span class="sxs-lookup"><span data-stu-id="fd757-262">In the following example of a `CounterStateProvider` component, counter data is persisted:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore

@if (_hasLoaded)
{
    <CascadingValue Value="@this">
        @ChildContent
    </CascadingValue>
}
else
{
    <p>Loading...</p>
}

@code {
    private bool _hasLoaded;

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public int CurrentCount { get; set; }

    protected override async Task OnInitializedAsync()
    {
        CurrentCount = await ProtectedSessionStore.GetAsync<int>("count");
        _hasLoaded = true;
    }

    public async Task SaveChangesAsync()
    {
        await ProtectedSessionStore.SetAsync("count", CurrentCount);
    }
}
```

<span data-ttu-id="fd757-263">O componente `CounterStateProvider` manipula a fase de carregamento não processando seu conteúdo filho até que o carregamento seja concluído.</span><span class="sxs-lookup"><span data-stu-id="fd757-263">The `CounterStateProvider` component handles the loading phase by not rendering its child content until loading is complete.</span></span>

<span data-ttu-id="fd757-264">Para usar o componente `CounterStateProvider`, empacote uma instância do componente em qualquer outro componente que exija acesso ao estado do contador.</span><span class="sxs-lookup"><span data-stu-id="fd757-264">To use the `CounterStateProvider` component, wrap an instance of the component around any other component that requires access to the counter state.</span></span> <span data-ttu-id="fd757-265">Para tornar o estado acessível a todos os componentes em um aplicativo, empacote o componente `CounterStateProvider` em volta do `Router` no componente `App` (*app. Razor*):</span><span class="sxs-lookup"><span data-stu-id="fd757-265">To make the state accessible to all components in an app, wrap the `CounterStateProvider` component around the `Router` in the `App` component (*App.razor*):</span></span>

```razor
<CounterStateProvider>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

<span data-ttu-id="fd757-266">Os componentes encapsulados recebem e podem modificar o estado do contador persistente.</span><span class="sxs-lookup"><span data-stu-id="fd757-266">Wrapped components receive and can modify the persisted counter state.</span></span> <span data-ttu-id="fd757-267">O componente `Counter` a seguir implementa o padrão:</span><span class="sxs-lookup"><span data-stu-id="fd757-267">The following `Counter` component implements the pattern:</span></span>

```razor
@page "/counter"

<p>Current count: <strong>@CounterStateProvider.CurrentCount</strong></p>

<button @onclick="IncrementCount">Increment</button>

@code {
    [CascadingParameter]
    private CounterStateProvider CounterStateProvider { get; set; }

    private async Task IncrementCount()
    {
        CounterStateProvider.CurrentCount++;
        await CounterStateProvider.SaveChangesAsync();
    }
}
```

<span data-ttu-id="fd757-268">O componente anterior não é necessário para interagir com `ProtectedBrowserStorage`, nem lidar com uma fase de "carregamento".</span><span class="sxs-lookup"><span data-stu-id="fd757-268">The preceding component isn't required to interact with `ProtectedBrowserStorage`, nor does it deal with a "loading" phase.</span></span>

<span data-ttu-id="fd757-269">Para lidar com o pré-processamento conforme descrito anteriormente, `CounterStateProvider` pode ser alterado para que todos os componentes que consumam os dados do contador funcionem automaticamente com o pré-processamento.</span><span class="sxs-lookup"><span data-stu-id="fd757-269">To deal with prerendering as described earlier, `CounterStateProvider` can be amended so that all of the components that consume the counter data automatically work with prerendering.</span></span> <span data-ttu-id="fd757-270">Consulte a seção [manipular pré-processamento](#handle-prerendering) para obter detalhes.</span><span class="sxs-lookup"><span data-stu-id="fd757-270">See the [Handle prerendering](#handle-prerendering) section for details.</span></span>

<span data-ttu-id="fd757-271">Em geral, o padrão de *componente pai do provedor de estado* é recomendado:</span><span class="sxs-lookup"><span data-stu-id="fd757-271">In general, *state provider parent component* pattern is recommended:</span></span>

* <span data-ttu-id="fd757-272">Para consumir o estado em muitos outros componentes.</span><span class="sxs-lookup"><span data-stu-id="fd757-272">To consume state in many other components.</span></span>
* <span data-ttu-id="fd757-273">Se houver apenas um objeto de estado de nível superior para persistir.</span><span class="sxs-lookup"><span data-stu-id="fd757-273">If there's just one top-level state object to persist.</span></span>

<span data-ttu-id="fd757-274">Para persistir muitos objetos de estado diferentes e consumir diferentes subconjuntos de objetos em locais diferentes, é melhor evitar manipular o carregamento e salvar o estado globalmente.</span><span class="sxs-lookup"><span data-stu-id="fd757-274">To persist many different state objects and consume different subsets of objects in different places, it's better to avoid handling the loading and saving of state globally.</span></span>
