---
title: Link do navegador no ASP.NET Core
author: ncarandini
description: Explica como o link do navegador é um recurso do Visual Studio que vincula o ambiente de desenvolvimento com um ou mais navegadores da Web.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 01/09/2020
no-loc:
- SignalR
uid: client-side/using-browserlink
ms.openlocfilehash: 19cc3c2ed91bd9e05df3c036123c78ecbf81fcc0
ms.sourcegitcommit: 7dfe6cc8408ac6a4549c29ca57b0c67ec4baa8de
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75828264"
---
# <a name="browser-link-in-aspnet-core"></a><span data-ttu-id="5e6ed-103">Link do navegador no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5e6ed-103">Browser Link in ASP.NET Core</span></span>

<span data-ttu-id="5e6ed-104">Por [Nicolò Carandini](https://github.com/ncarandini), [Mike Wasson](https://github.com/MikeWasson)e [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="5e6ed-104">By [Nicolò Carandini](https://github.com/ncarandini), [Mike Wasson](https://github.com/MikeWasson), and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="5e6ed-105">O link do navegador é um recurso do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="5e6ed-105">Browser Link is a Visual Studio feature.</span></span> <span data-ttu-id="5e6ed-106">Ele cria um canal de comunicação entre o ambiente de desenvolvimento e um ou mais navegadores da Web.</span><span class="sxs-lookup"><span data-stu-id="5e6ed-106">It creates a communication channel between the development environment and one or more web browsers.</span></span> <span data-ttu-id="5e6ed-107">Você pode usar o link do navegador para atualizar seu aplicativo Web em vários navegadores de uma vez, o que é útil para testes entre navegadores.</span><span class="sxs-lookup"><span data-stu-id="5e6ed-107">You can use Browser Link to refresh your web app in several browsers at once, which is useful for cross-browser testing.</span></span>

## <a name="browser-link-setup"></a><span data-ttu-id="5e6ed-108">Instalação do link do navegador</span><span class="sxs-lookup"><span data-stu-id="5e6ed-108">Browser Link setup</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5e6ed-109">Adicione o pacote [Microsoft. VisualStudio. Web. BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) ao seu projeto.</span><span class="sxs-lookup"><span data-stu-id="5e6ed-109">Add the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package to your project.</span></span> <span data-ttu-id="5e6ed-110">Para projetos ASP.NET Core Razor Pages ou MVC, habilite também a compilação em tempo de execução de arquivos Razor ( *. cshtml*), conforme descrito em <xref:mvc/views/view-compilation>.</span><span class="sxs-lookup"><span data-stu-id="5e6ed-110">For ASP.NET Core Razor Pages or MVC projects, also enable runtime compilation of Razor (*.cshtml*) files as described in <xref:mvc/views/view-compilation>.</span></span> <span data-ttu-id="5e6ed-111">Sintaxe Razor alterações são aplicadas somente quando a compilação em tempo de execução tiver sido habilitada.</span><span class="sxs-lookup"><span data-stu-id="5e6ed-111">Razor syntax changes are applied only when runtime compilation has been enabled.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

<span data-ttu-id="5e6ed-112">Ao converter um projeto ASP.NET Core 2,0 para ASP.NET Core 2,1 e fazer a transição para o [metapacote Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), instale o pacote [Microsoft. VisualStudio. Web. BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) para a funcionalidade de link do navegador.</span><span class="sxs-lookup"><span data-stu-id="5e6ed-112">When converting an ASP.NET Core 2.0 project to ASP.NET Core 2.1 and transitioning to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), install the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package for Browser Link functionality.</span></span> <span data-ttu-id="5e6ed-113">Os modelos de projeto ASP.NET Core 2,1 usam o metapacote do `Microsoft.AspNetCore.App` por padrão.</span><span class="sxs-lookup"><span data-stu-id="5e6ed-113">The ASP.NET Core 2.1 project templates use the `Microsoft.AspNetCore.App` metapackage by default.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="5e6ed-114">Os modelos de projeto de **aplicativo web**ASP.NET Core 2,0, **vazio**e de **API Web** usam o [metapacote Microsoft. AspNetCore. All](xref:fundamentals/metapackage), que contém uma referência de pacote para [Microsoft. VisualStudio. Web. BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/).</span><span class="sxs-lookup"><span data-stu-id="5e6ed-114">The ASP.NET Core 2.0 **Web Application**, **Empty**, and **Web API** project templates use the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage), which contains a package reference for [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/).</span></span> <span data-ttu-id="5e6ed-115">Portanto, o uso do metapacote `Microsoft.AspNetCore.All` não requer nenhuma ação adicional para disponibilizar o link do navegador para uso.</span><span class="sxs-lookup"><span data-stu-id="5e6ed-115">Therefore, using the `Microsoft.AspNetCore.All` metapackage requires no further action to make Browser Link available for use.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="5e6ed-116">O modelo de projeto de **aplicativo Web** ASP.NET Core 1. x tem uma referência de pacote para o pacote [Microsoft. VisualStudio. Web. BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) .</span><span class="sxs-lookup"><span data-stu-id="5e6ed-116">The ASP.NET Core 1.x **Web Application** project template has a package reference for the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package.</span></span> <span data-ttu-id="5e6ed-117">Outros tipos de projeto exigem que você adicione uma referência de pacote a `Microsoft.VisualStudio.Web.BrowserLink`.</span><span class="sxs-lookup"><span data-stu-id="5e6ed-117">Other project types require you to add a package reference to `Microsoft.VisualStudio.Web.BrowserLink`.</span></span>

::: moniker-end

### <a name="configuration"></a><span data-ttu-id="5e6ed-118">Configuração do</span><span class="sxs-lookup"><span data-stu-id="5e6ed-118">Configuration</span></span>

<span data-ttu-id="5e6ed-119">Chame `UseBrowserLink` no método `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="5e6ed-119">Call `UseBrowserLink` in the `Startup.Configure` method:</span></span>

```csharp
app.UseBrowserLink();
```

<span data-ttu-id="5e6ed-120">A chamada de `UseBrowserLink` normalmente é colocada dentro de um bloco de `if` que habilita apenas o link do navegador no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="5e6ed-120">The `UseBrowserLink` call is typically placed inside an `if` block that only enables Browser Link in the Development environment.</span></span> <span data-ttu-id="5e6ed-121">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="5e6ed-121">For example:</span></span>

```csharp
if (env.IsDevelopment())
{
    app.UseDeveloperExceptionPage();
    app.UseBrowserLink();
}
```

<span data-ttu-id="5e6ed-122">Para obter mais informações, consulte <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="5e6ed-122">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="how-to-use-browser-link"></a><span data-ttu-id="5e6ed-123">Como usar o link do navegador</span><span class="sxs-lookup"><span data-stu-id="5e6ed-123">How to use Browser Link</span></span>

<span data-ttu-id="5e6ed-124">Quando um projeto do ASP.NET Core está aberto, o Visual Studio mostra o controle de barra de ferramentas do Link do navegador ao lado do controle de barra de ferramentas do **Destino de Depuração**:</span><span class="sxs-lookup"><span data-stu-id="5e6ed-124">When you have an ASP.NET Core project open, Visual Studio shows the Browser Link toolbar control next to the **Debug Target** toolbar control:</span></span>

![Menu suspenso de link do navegador](using-browserlink/_static/browserLink-dropdown-menu.png)

<span data-ttu-id="5e6ed-126">No controle da barra de ferramentas do link do navegador, você pode:</span><span class="sxs-lookup"><span data-stu-id="5e6ed-126">From the Browser Link toolbar control, you can:</span></span>

* <span data-ttu-id="5e6ed-127">Atualize o aplicativo Web em vários navegadores de uma vez.</span><span class="sxs-lookup"><span data-stu-id="5e6ed-127">Refresh the web app in several browsers at once.</span></span>
* <span data-ttu-id="5e6ed-128">Abrir o **Painel de link do navegador**.</span><span class="sxs-lookup"><span data-stu-id="5e6ed-128">Open the **Browser Link Dashboard**.</span></span>
* <span data-ttu-id="5e6ed-129">Habilitar ou desabilitar o **link do navegador**.</span><span class="sxs-lookup"><span data-stu-id="5e6ed-129">Enable or disable **Browser Link**.</span></span> <span data-ttu-id="5e6ed-130">Observação: o link do navegador está desabilitado por padrão no Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="5e6ed-130">Note: Browser Link is disabled by default in Visual Studio.</span></span>
* <span data-ttu-id="5e6ed-131">Habilitar ou desabilitar [a sincronização automática de CSS](#enable-or-disable-css-auto-sync).</span><span class="sxs-lookup"><span data-stu-id="5e6ed-131">Enable or disable [CSS Auto-Sync](#enable-or-disable-css-auto-sync).</span></span>

## <a name="refresh-the-web-app-in-several-browsers-at-once"></a><span data-ttu-id="5e6ed-132">Atualizar o aplicativo Web em vários navegadores de uma vez</span><span class="sxs-lookup"><span data-stu-id="5e6ed-132">Refresh the web app in several browsers at once</span></span>

<span data-ttu-id="5e6ed-133">Para escolher um único navegador Web para abrir ao iniciar o projeto, use o menu suspenso do controle de barra de ferramentas do **Destino de depuração**:</span><span class="sxs-lookup"><span data-stu-id="5e6ed-133">To choose a single web browser to launch when starting the project, use the drop-down menu in the **Debug Target** toolbar control:</span></span>

![F5 menu suspenso](using-browserlink/_static/debug-target-dropdown-menu.png)

<span data-ttu-id="5e6ed-135">Para abrir vários navegadores de uma vez, escolha **procurar com...** na mesma lista suspensa.</span><span class="sxs-lookup"><span data-stu-id="5e6ed-135">To open multiple browsers at once, choose **Browse with...** from the same drop-down.</span></span> <span data-ttu-id="5e6ed-136">Mantenha pressionada a tecla <kbd>Ctrl</kbd> para selecionar os navegadores desejados e clique em **procurar**:</span><span class="sxs-lookup"><span data-stu-id="5e6ed-136">Hold down the <kbd>Ctrl</kbd> key to select the browsers you want, and then click **Browse**:</span></span>

![Abrir vários navegadores de uma vez](using-browserlink/_static/open-many-browsers-at-once.png)

<span data-ttu-id="5e6ed-138">A captura de tela a seguir mostra o Visual Studio com a exibição de índice aberta e dois navegadores abertos:</span><span class="sxs-lookup"><span data-stu-id="5e6ed-138">The following screenshot shows Visual Studio with the Index view open and two open browsers:</span></span>

![Exemplo de sincronização com dois navegadores](using-browserlink/_static/sync-with-two-browsers-example.png)

<span data-ttu-id="5e6ed-140">Passe o mouse sobre o controle da barra de ferramentas link do navegador para ver os navegadores que estão conectados ao projeto:</span><span class="sxs-lookup"><span data-stu-id="5e6ed-140">Hover over the Browser Link toolbar control to see the browsers that are connected to the project:</span></span>

![Dica de foco](using-browserlink/_static/hoover-tip.png)

<span data-ttu-id="5e6ed-142">Altere a exibição do índice e todos os navegadores conectados serão atualizados quando você clicar no botão atualizar do link do navegador:</span><span class="sxs-lookup"><span data-stu-id="5e6ed-142">Change the Index view, and all connected browsers are updated when you click the Browser Link refresh button:</span></span>

![navegadores-sincronização-para-alterações](using-browserlink/_static/browsers-sync-to-changes.png)

<span data-ttu-id="5e6ed-144">O link do navegador também funciona com navegadores que você inicia de fora do Visual Studio e navega até a URL do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5e6ed-144">Browser Link also works with browsers that you launch from outside Visual Studio and navigate to the app URL.</span></span>

### <a name="the-browser-link-dashboard"></a><span data-ttu-id="5e6ed-145">O painel do link do navegador</span><span class="sxs-lookup"><span data-stu-id="5e6ed-145">The Browser Link Dashboard</span></span>

<span data-ttu-id="5e6ed-146">Abra a janela do **Painel link do navegador** no menu suspenso link do navegador para gerenciar a conexão com os navegadores abertos:</span><span class="sxs-lookup"><span data-stu-id="5e6ed-146">Open the **Browser Link Dashboard** window from the Browser Link drop down menu to manage the connection with open browsers:</span></span>

![open-browserslink-dashboard](using-browserlink/_static/open-browserlink-dashboard.png)

<span data-ttu-id="5e6ed-148">Se nenhum navegador estiver conectado, você pode iniciar uma sessão de depuração não, selecionando Se nenhum navegador estiver conectado, você poderá iniciar uma sessão de não depuração selecionando o link **exibir no navegador**:</span><span class="sxs-lookup"><span data-stu-id="5e6ed-148">If no browser is connected, you can start a non-debugging session by selecting the **View in Browser** link:</span></span>

![browserlink-dashboard-no-connections](using-browserlink/_static/browserlink-dashboard-no-connections.png)

<span data-ttu-id="5e6ed-150">Caso contrário, os navegadores conectados são mostrados com o caminho para a página que cada navegador está mostrando:</span><span class="sxs-lookup"><span data-stu-id="5e6ed-150">Otherwise, the connected browsers are shown with the path to the page that each browser is showing:</span></span>

![browserlink-dashboard-two-connections](using-browserlink/_static/browserlink-dashboard-two-connections.png)

<span data-ttu-id="5e6ed-152">Você também pode clicar em um nome de navegador individual para atualizar somente esse navegador.</span><span class="sxs-lookup"><span data-stu-id="5e6ed-152">You can also click on an individual browser name to refresh only that browser.</span></span>

### <a name="enable-or-disable-browser-link"></a><span data-ttu-id="5e6ed-153">Habilitar ou desabilitar o link do navegador</span><span class="sxs-lookup"><span data-stu-id="5e6ed-153">Enable or disable Browser Link</span></span>

<span data-ttu-id="5e6ed-154">Ao reabilitar o link do navegador depois de desabilitá-lo, você deve atualizar os navegadores para reconectá-los.</span><span class="sxs-lookup"><span data-stu-id="5e6ed-154">When you re-enable Browser Link after disabling it, you must refresh the browsers to reconnect them.</span></span>

### <a name="enable-or-disable-css-auto-sync"></a><span data-ttu-id="5e6ed-155">Habilitar ou desabilitar a sincronização automática de CSS</span><span class="sxs-lookup"><span data-stu-id="5e6ed-155">Enable or disable CSS Auto-Sync</span></span>

<span data-ttu-id="5e6ed-156">Quando a sincronização automática de CSS está habilitada, os navegadores conectados são atualizados automaticamente quando você faz qualquer alteração em arquivos CSS.</span><span class="sxs-lookup"><span data-stu-id="5e6ed-156">When CSS Auto-Sync is enabled, connected browsers are automatically refreshed when you make any change to CSS files.</span></span>

## <a name="how-it-works"></a><span data-ttu-id="5e6ed-157">Como funciona</span><span class="sxs-lookup"><span data-stu-id="5e6ed-157">How it works</span></span>

<span data-ttu-id="5e6ed-158">O link do navegador usa [SignalR](xref:signalr/introduction) para criar um canal de comunicação entre o Visual Studio e o navegador.</span><span class="sxs-lookup"><span data-stu-id="5e6ed-158">Browser Link uses [SignalR](xref:signalr/introduction) to create a communication channel between Visual Studio and the browser.</span></span> <span data-ttu-id="5e6ed-159">Quando o link do navegador está habilitado, o Visual Studio atua como um SignalR servidor ao qual vários clientes (navegadores) podem se conectar.</span><span class="sxs-lookup"><span data-stu-id="5e6ed-159">When Browser Link is enabled, Visual Studio acts as a SignalR server that multiple clients (browsers) can connect to.</span></span> <span data-ttu-id="5e6ed-160">O link do navegador também registra um componente de middleware no pipeline de solicitação de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5e6ed-160">Browser Link also registers a middleware component in the ASP.NET Core request pipeline.</span></span> <span data-ttu-id="5e6ed-161">Esse componente injeta referências especiais de `<script>` em cada solicitação de página do servidor.</span><span class="sxs-lookup"><span data-stu-id="5e6ed-161">This component injects special `<script>` references into every page request from the server.</span></span> <span data-ttu-id="5e6ed-162">Você pode ver as referências de script selecionando **Exibir origem** no navegador e rolando até o final do `<body>` conteúdo da marca:</span><span class="sxs-lookup"><span data-stu-id="5e6ed-162">You can see the script references by selecting **View source** in the browser and scrolling to the end of the `<body>` tag content:</span></span>

```html
    <!-- Visual Studio Browser Link -->
    <script type="application/json" id="__browserLink_initializationData">
        {"requestId":"a717d5a07c1741949a7cefd6fa2bad08","requestMappingFromServer":false}
    </script>
    <script type="text/javascript" src="http://localhost:54139/b6e36e429d034f578ebccd6a79bf19bf/browserLink" async="async"></script>
    <!-- End Browser Link -->
</body>
```

<span data-ttu-id="5e6ed-163">Os arquivos de origem não são modificados.</span><span class="sxs-lookup"><span data-stu-id="5e6ed-163">Your source files aren't modified.</span></span> <span data-ttu-id="5e6ed-164">O componente de middleware injeta as referências de script dinamicamente.</span><span class="sxs-lookup"><span data-stu-id="5e6ed-164">The middleware component injects the script references dynamically.</span></span>

<span data-ttu-id="5e6ed-165">Como o código do lado do navegador é todo o JavaScript, ele funciona em todos os navegadores que SignalR oferece suporte sem a necessidade de um plug-in de navegador.</span><span class="sxs-lookup"><span data-stu-id="5e6ed-165">Because the browser-side code is all JavaScript, it works on all browsers that SignalR supports without requiring a browser plug-in.</span></span>
