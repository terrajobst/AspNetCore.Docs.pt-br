---
title: Link do navegador no ASP.NET Core
author: ncarandini
description: Explica como o link do navegador é um recurso do Visual Studio que vincula o ambiente de desenvolvimento com um ou mais navegadores da Web.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 11/12/2019
no-loc:
- SignalR
uid: client-side/using-browserlink
ms.openlocfilehash: b21b698d49e72b559cd9cd3753c48a38c99db24d
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73962791"
---
# <a name="browser-link-in-aspnet-core"></a><span data-ttu-id="b12e5-103">Link do navegador no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b12e5-103">Browser Link in ASP.NET Core</span></span>

<span data-ttu-id="b12e5-104">Por [Nicolò Carandini](https://github.com/ncarandini), [Mike Wasson](https://github.com/MikeWasson)e [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="b12e5-104">By [Nicolò Carandini](https://github.com/ncarandini), [Mike Wasson](https://github.com/MikeWasson), and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="b12e5-105">O link do navegador é um recurso do Visual Studio que cria um canal de comunicação entre o ambiente de desenvolvimento e um ou mais navegadores da Web.</span><span class="sxs-lookup"><span data-stu-id="b12e5-105">Browser Link is a feature in Visual Studio that creates a communication channel between the development environment and one or more web browsers.</span></span> <span data-ttu-id="b12e5-106">Você pode usar o link do navegador para atualizar seu aplicativo Web em vários navegadores de uma vez, o que é útil para testes entre navegadores.</span><span class="sxs-lookup"><span data-stu-id="b12e5-106">You can use Browser Link to refresh your web application in several browsers at once, which is useful for cross-browser testing.</span></span>

## <a name="browser-link-setup"></a><span data-ttu-id="b12e5-107">Instalação do link do navegador</span><span class="sxs-lookup"><span data-stu-id="b12e5-107">Browser Link setup</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="b12e5-108">Ao converter um projeto ASP.NET Core 2,0 para ASP.NET Core 2,1 e fazer a transição para o [metapacote Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), instale o pacote [Microsoft. VisualStudio. Web. BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) para a funcionalidade BrowserLink.</span><span class="sxs-lookup"><span data-stu-id="b12e5-108">When converting an ASP.NET Core 2.0 project to ASP.NET Core 2.1 and transitioning to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), install the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package for BrowserLink functionality.</span></span> <span data-ttu-id="b12e5-109">Os modelos de projeto ASP.NET Core 2,1 usam o metapacote do `Microsoft.AspNetCore.App` por padrão.</span><span class="sxs-lookup"><span data-stu-id="b12e5-109">The ASP.NET Core 2.1 project templates use the `Microsoft.AspNetCore.App` metapackage by default.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="b12e5-110">Os modelos de projeto de **aplicativo web**ASP.NET Core 2,0, **vazio**e de **API Web** usam o [metapacote Microsoft. AspNetCore. All](xref:fundamentals/metapackage), que contém uma referência de pacote para [Microsoft. VisualStudio. Web. BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/).</span><span class="sxs-lookup"><span data-stu-id="b12e5-110">The ASP.NET Core 2.0 **Web Application**, **Empty**, and **Web API** project templates use the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage), which contains a package reference for [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/).</span></span> <span data-ttu-id="b12e5-111">Portanto, o uso do metapacote `Microsoft.AspNetCore.All` não requer nenhuma ação adicional para disponibilizar o link do navegador para uso.</span><span class="sxs-lookup"><span data-stu-id="b12e5-111">Therefore, using the `Microsoft.AspNetCore.All` metapackage requires no further action to make Browser Link available for use.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="b12e5-112">O modelo de projeto de **aplicativo Web** ASP.NET Core 1. x tem uma referência de pacote para o pacote [Microsoft. VisualStudio. Web. BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) .</span><span class="sxs-lookup"><span data-stu-id="b12e5-112">The ASP.NET Core 1.x **Web Application** project template has a package reference for the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package.</span></span> <span data-ttu-id="b12e5-113">Os projetos de modelo de **API Web** ou **vazios** exigem que você adicione uma referência de pacote a `Microsoft.VisualStudio.Web.BrowserLink`.</span><span class="sxs-lookup"><span data-stu-id="b12e5-113">The **Empty** or **Web API** template projects require you to add a package reference to `Microsoft.VisualStudio.Web.BrowserLink`.</span></span>

<span data-ttu-id="b12e5-114">Como esse é um recurso do Visual Studio, a maneira mais fácil de adicionar o pacote a um projeto de modelo de **API Web** ou **vazio** é abrir o **console do gerenciador de pacotes** (**Exibir** > outro console do **Gerenciador de pacotes**do **Windows** >) e executar o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="b12e5-114">Since this is a Visual Studio feature, the easiest way to add the package to an **Empty** or **Web API** template project is to open the **Package Manager Console** (**View** > **Other Windows** > **Package Manager Console**) and run the following command:</span></span>

```console
install-package Microsoft.VisualStudio.Web.BrowserLink
```

<span data-ttu-id="b12e5-115">Como alternativa, você pode usar o **Gerenciador de pacotes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="b12e5-115">Alternatively, you can use **NuGet Package Manager**.</span></span> <span data-ttu-id="b12e5-116">Clique com o botão direito do mouse no nome do projeto em **Gerenciador de soluções** e escolha **gerenciar pacotes NuGet**:</span><span class="sxs-lookup"><span data-stu-id="b12e5-116">Right-click the project name in **Solution Explorer** and choose **Manage NuGet Packages**:</span></span>

![Abrir o Gerenciador de pacotes do NuGet](using-browserlink/_static/open-nuget-package-manager.png)

<span data-ttu-id="b12e5-118">Localize e instale o pacote:</span><span class="sxs-lookup"><span data-stu-id="b12e5-118">Find and install the package:</span></span>

![Adicionar pacote com o Gerenciador de pacotes NuGet](using-browserlink/_static/add-package-with-nuget-package-manager.png)

::: moniker-end

### <a name="configuration"></a><span data-ttu-id="b12e5-120">Configuração</span><span class="sxs-lookup"><span data-stu-id="b12e5-120">Configuration</span></span>

<span data-ttu-id="b12e5-121">No método `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="b12e5-121">In the `Startup.Configure` method:</span></span>

```csharp
app.UseBrowserLink();
```

<span data-ttu-id="b12e5-122">Normalmente, o código está dentro de um bloco de `if` que habilita apenas o link do navegador no ambiente de desenvolvimento, como mostrado aqui:</span><span class="sxs-lookup"><span data-stu-id="b12e5-122">Usually the code is inside an `if` block that only enables Browser Link in the Development environment, as shown here:</span></span>

```csharp
if (env.IsDevelopment())
{
    app.UseDeveloperExceptionPage();
    app.UseBrowserLink();
}
```

<span data-ttu-id="b12e5-123">Para obter mais informações, veja [Usar vários ambientes](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="b12e5-123">For more information, see [Use multiple environments](xref:fundamentals/environments).</span></span>

## <a name="how-to-use-browser-link"></a><span data-ttu-id="b12e5-124">Como usar o link do navegador</span><span class="sxs-lookup"><span data-stu-id="b12e5-124">How to use Browser Link</span></span>

<span data-ttu-id="b12e5-125">Quando você tem um projeto ASP.NET Core aberto, o Visual Studio mostra o controle da barra de ferramentas link do navegador ao lado do controle da barra de ferramentas de **destino de depuração** :</span><span class="sxs-lookup"><span data-stu-id="b12e5-125">When you have an ASP.NET Core project open, Visual Studio shows the Browser Link toolbar control next to the **Debug Target** toolbar control:</span></span>

![Menu suspenso de link do navegador](using-browserlink/_static/browserLink-dropdown-menu.png)

<span data-ttu-id="b12e5-127">No controle da barra de ferramentas do link do navegador, você pode:</span><span class="sxs-lookup"><span data-stu-id="b12e5-127">From the Browser Link toolbar control, you can:</span></span>

* <span data-ttu-id="b12e5-128">Atualize o aplicativo Web em vários navegadores de uma vez.</span><span class="sxs-lookup"><span data-stu-id="b12e5-128">Refresh the web application in several browsers at once.</span></span>
* <span data-ttu-id="b12e5-129">Abra o **painel de link do navegador**.</span><span class="sxs-lookup"><span data-stu-id="b12e5-129">Open the **Browser Link Dashboard**.</span></span>
* <span data-ttu-id="b12e5-130">Habilitar ou desabilitar o **link do navegador**.</span><span class="sxs-lookup"><span data-stu-id="b12e5-130">Enable or disable **Browser Link**.</span></span> <span data-ttu-id="b12e5-131">Observação: o link do navegador está desabilitado por padrão no Visual Studio 2017 (15,3).</span><span class="sxs-lookup"><span data-stu-id="b12e5-131">Note: Browser Link is disabled by default in Visual Studio 2017 (15.3).</span></span>
* <span data-ttu-id="b12e5-132">Habilitar ou desabilitar [a sincronização automática de CSS](#enable-or-disable-css-auto-sync).</span><span class="sxs-lookup"><span data-stu-id="b12e5-132">Enable or disable [CSS Auto-Sync](#enable-or-disable-css-auto-sync).</span></span>

> [!NOTE]
> <span data-ttu-id="b12e5-133">Alguns plug-ins do Visual Studio, principalmente o *Web Extension pack 2015* e o *pacote de extensões da Web 2017*, oferecem funcionalidade estendida para o link do navegador, mas alguns dos recursos adicionais não funcionam com projetos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b12e5-133">Some Visual Studio plug-ins, most notably *Web Extension Pack 2015* and *Web Extension Pack 2017*, offer extended functionality for Browser Link, but some of the additional features don't work with ASP.NET Core projects.</span></span>

## <a name="refresh-the-web-app-in-several-browsers-at-once"></a><span data-ttu-id="b12e5-134">Atualizar o aplicativo Web em vários navegadores de uma vez</span><span class="sxs-lookup"><span data-stu-id="b12e5-134">Refresh the web app in several browsers at once</span></span>

<span data-ttu-id="b12e5-135">Para escolher um único navegador da Web para iniciar ao iniciar o projeto, use o menu suspenso no controle da barra de ferramentas de **destino de depuração** :</span><span class="sxs-lookup"><span data-stu-id="b12e5-135">To choose a single web browser to launch when starting the project, use the drop-down menu in the **Debug Target** toolbar control:</span></span>

![F5 menu suspenso](using-browserlink/_static/debug-target-dropdown-menu.png)

<span data-ttu-id="b12e5-137">Para abrir vários navegadores de uma vez, escolha **procurar com...** na mesma lista suspensa.</span><span class="sxs-lookup"><span data-stu-id="b12e5-137">To open multiple browsers at once, choose **Browse with...** from the same drop-down.</span></span> <span data-ttu-id="b12e5-138">Mantenha pressionada a tecla CTRL para selecionar os navegadores desejados e clique em **procurar**:</span><span class="sxs-lookup"><span data-stu-id="b12e5-138">Hold down the CTRL key to select the browsers you want, and then click **Browse**:</span></span>

![Abrir vários navegadores de uma vez](using-browserlink/_static/open-many-browsers-at-once.png)

<span data-ttu-id="b12e5-140">Aqui está uma captura de tela mostrando o Visual Studio com a exibição de índice aberta e dois navegadores abertos:</span><span class="sxs-lookup"><span data-stu-id="b12e5-140">Here's a screenshot showing Visual Studio with the Index view open and two open browsers:</span></span>

![Exemplo de sincronização com dois navegadores](using-browserlink/_static/sync-with-two-browsers-example.png)

<span data-ttu-id="b12e5-142">Passe o mouse sobre o controle da barra de ferramentas link do navegador para ver os navegadores que estão conectados ao projeto:</span><span class="sxs-lookup"><span data-stu-id="b12e5-142">Hover over the Browser Link toolbar control to see the browsers that are connected to the project:</span></span>

![Dica de foco](using-browserlink/_static/hoover-tip.png)

<span data-ttu-id="b12e5-144">Altere a exibição do índice e todos os navegadores conectados serão atualizados quando você clicar no botão atualizar do link do navegador:</span><span class="sxs-lookup"><span data-stu-id="b12e5-144">Change the Index view, and all connected browsers are updated when you click the Browser Link refresh button:</span></span>

![navegadores-sincronização-para-alterações](using-browserlink/_static/browsers-sync-to-changes.png)

<span data-ttu-id="b12e5-146">O link do navegador também funciona com navegadores que você inicia de fora do Visual Studio e navega até a URL do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b12e5-146">Browser Link also works with browsers that you launch from outside Visual Studio and navigate to the application URL.</span></span>

### <a name="the-browser-link-dashboard"></a><span data-ttu-id="b12e5-147">O painel do link do navegador</span><span class="sxs-lookup"><span data-stu-id="b12e5-147">The Browser Link Dashboard</span></span>

<span data-ttu-id="b12e5-148">Abra o painel de link do navegador no menu suspenso link do navegador para gerenciar a conexão com navegadores abertos:</span><span class="sxs-lookup"><span data-stu-id="b12e5-148">Open the Browser Link Dashboard from the Browser Link drop down menu to manage the connection with open browsers:</span></span>

![Open-browserslink-Dashboard](using-browserlink/_static/open-browserlink-dashboard.png)

<span data-ttu-id="b12e5-150">Se nenhum navegador estiver conectado, você poderá iniciar uma sessão de não depuração selecionando o link *Exibir no navegador* :</span><span class="sxs-lookup"><span data-stu-id="b12e5-150">If no browser is connected, you can start a non-debugging session by selecting the *View in Browser* link:</span></span>

![browserlink-Dashboard-no-Connections](using-browserlink/_static/browserlink-dashboard-no-connections.png)

<span data-ttu-id="b12e5-152">Caso contrário, os navegadores conectados são mostrados com o caminho para a página que cada navegador está mostrando:</span><span class="sxs-lookup"><span data-stu-id="b12e5-152">Otherwise, the connected browsers are shown with the path to the page that each browser is showing:</span></span>

![browserlink-Dashboard-Two-Connections](using-browserlink/_static/browserlink-dashboard-two-connections.png)

<span data-ttu-id="b12e5-154">Se desejar, você pode clicar em um nome de navegador listado para atualizar esse navegador único.</span><span class="sxs-lookup"><span data-stu-id="b12e5-154">If you like, you can click on a listed browser name to refresh that single browser.</span></span>

### <a name="enable-or-disable-browser-link"></a><span data-ttu-id="b12e5-155">Habilitar ou desabilitar o link do navegador</span><span class="sxs-lookup"><span data-stu-id="b12e5-155">Enable or disable Browser Link</span></span>

<span data-ttu-id="b12e5-156">Ao reabilitar o link do navegador depois de desabilitá-lo, você deve atualizar os navegadores para reconectá-los.</span><span class="sxs-lookup"><span data-stu-id="b12e5-156">When you re-enable Browser Link after disabling it, you must refresh the browsers to reconnect them.</span></span>

### <a name="enable-or-disable-css-auto-sync"></a><span data-ttu-id="b12e5-157">Habilitar ou desabilitar a sincronização automática de CSS</span><span class="sxs-lookup"><span data-stu-id="b12e5-157">Enable or disable CSS Auto-Sync</span></span>

<span data-ttu-id="b12e5-158">Quando a sincronização automática de CSS está habilitada, os navegadores conectados são atualizados automaticamente quando você faz qualquer alteração em arquivos CSS.</span><span class="sxs-lookup"><span data-stu-id="b12e5-158">When CSS Auto-Sync is enabled, connected browsers are automatically refreshed when you make any change to CSS files.</span></span>

## <a name="how-it-works"></a><span data-ttu-id="b12e5-159">Como funciona</span><span class="sxs-lookup"><span data-stu-id="b12e5-159">How it works</span></span>

<span data-ttu-id="b12e5-160">O link do navegador usa SignalR para criar um canal de comunicação entre o Visual Studio e o navegador.</span><span class="sxs-lookup"><span data-stu-id="b12e5-160">Browser Link uses SignalR to create a communication channel between Visual Studio and the browser.</span></span> <span data-ttu-id="b12e5-161">Quando o link do navegador está habilitado, o Visual Studio atua como um SignalR servidor ao qual vários clientes (navegadores) podem se conectar.</span><span class="sxs-lookup"><span data-stu-id="b12e5-161">When Browser Link is enabled, Visual Studio acts as a SignalR server that multiple clients (browsers) can connect to.</span></span> <span data-ttu-id="b12e5-162">O link do navegador também registra um componente de middleware no pipeline de solicitação de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b12e5-162">Browser Link also registers a middleware component in the ASP.NET Core request pipeline.</span></span> <span data-ttu-id="b12e5-163">Esse componente injeta referências especiais de `<script>` em cada solicitação de página do servidor.</span><span class="sxs-lookup"><span data-stu-id="b12e5-163">This component injects special `<script>` references into every page request from the server.</span></span> <span data-ttu-id="b12e5-164">Você pode ver as referências de script selecionando **Exibir origem** no navegador e rolando até o final do `<body>` conteúdo da marca:</span><span class="sxs-lookup"><span data-stu-id="b12e5-164">You can see the script references by selecting **View source** in the browser and scrolling to the end of the `<body>` tag content:</span></span>

```html
    <!-- Visual Studio Browser Link -->
    <script type="application/json" id="__browserLink_initializationData">
        {"requestId":"a717d5a07c1741949a7cefd6fa2bad08","requestMappingFromServer":false}
    </script>
    <script type="text/javascript" src="http://localhost:54139/b6e36e429d034f578ebccd6a79bf19bf/browserLink" async="async"></script>
    <!-- End Browser Link -->
</body>
```

<span data-ttu-id="b12e5-165">Os arquivos de origem não são modificados.</span><span class="sxs-lookup"><span data-stu-id="b12e5-165">Your source files aren't modified.</span></span> <span data-ttu-id="b12e5-166">O componente de middleware injeta as referências de script dinamicamente.</span><span class="sxs-lookup"><span data-stu-id="b12e5-166">The middleware component injects the script references dynamically.</span></span>

<span data-ttu-id="b12e5-167">Como o código do lado do navegador é todo o JavaScript, ele funciona em todos os navegadores que SignalR oferece suporte sem a necessidade de um plug-in de navegador.</span><span class="sxs-lookup"><span data-stu-id="b12e5-167">Because the browser-side code is all JavaScript, it works on all browsers that SignalR supports without requiring a browser plug-in.</span></span>
