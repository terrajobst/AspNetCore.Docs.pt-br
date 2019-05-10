---
title: Link do navegador no ASP.NET Core
author: ncarandini
description: Explica como o Link do navegador é um recurso do Visual Studio que vincula o ambiente de desenvolvimento com um ou mais navegadores da web.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 09/22/2017
uid: client-side/using-browserlink
ms.openlocfilehash: 452ba5149563c186750466f471c7b950f0017614
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64894703"
---
# <a name="browser-link-in-aspnet-core"></a><span data-ttu-id="41108-103">Link do navegador no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="41108-103">Browser Link in ASP.NET Core</span></span>

<span data-ttu-id="41108-104">Por [Nicolò Carandini](https://github.com/ncarandini), [Mike Wasson](https://github.com/MikeWasson), e [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="41108-104">By [Nicolò Carandini](https://github.com/ncarandini), [Mike Wasson](https://github.com/MikeWasson), and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="41108-105">Link do navegador é um recurso no Visual Studio que cria um canal de comunicação entre o ambiente de desenvolvimento e um ou mais navegadores da web.</span><span class="sxs-lookup"><span data-stu-id="41108-105">Browser Link is a feature in Visual Studio that creates a communication channel between the development environment and one or more web browsers.</span></span> <span data-ttu-id="41108-106">Você pode usar o Link do navegador para atualizar seu aplicativo web em vários navegadores ao mesmo tempo, que é útil para testes entre navegadores.</span><span class="sxs-lookup"><span data-stu-id="41108-106">You can use Browser Link to refresh your web application in several browsers at once, which is useful for cross-browser testing.</span></span>

## <a name="browser-link-setup"></a><span data-ttu-id="41108-107">Configuração do Link de navegador</span><span class="sxs-lookup"><span data-stu-id="41108-107">Browser Link setup</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="41108-108">Ao converter um projeto do ASP.NET Core 2.0 para ASP.NET Core 2.1 e fazer a transição para o [metapacote do Microsoft](xref:fundamentals/metapackage-app), instale o [browserlink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) do pacote para Funcionalidade BrowserLink.</span><span class="sxs-lookup"><span data-stu-id="41108-108">When converting an ASP.NET Core 2.0 project to ASP.NET Core 2.1 and transitioning to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), install the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package for BrowserLink functionality.</span></span> <span data-ttu-id="41108-109">Usam os modelos de projeto do ASP.NET Core 2.1 a `Microsoft.AspNetCore.App` metapacote por padrão.</span><span class="sxs-lookup"><span data-stu-id="41108-109">The ASP.NET Core 2.1 project templates use the `Microsoft.AspNetCore.App` metapackage by default.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="41108-110">O ASP.NET Core 2.0 **aplicativo Web**, **vazia**, e **API da Web** uso de modelos de projeto a [Microsoft.AspNetCore.All metapacote](xref:fundamentals/metapackage) , que contém uma referência de pacote para [browserlink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/).</span><span class="sxs-lookup"><span data-stu-id="41108-110">The ASP.NET Core 2.0 **Web Application**, **Empty**, and **Web API** project templates use the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage), which contains a package reference for [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/).</span></span> <span data-ttu-id="41108-111">Portanto, usando o `Microsoft.AspNetCore.All` metapacote não requer nenhuma ação adicional para disponibilizar o Link do navegador para uso.</span><span class="sxs-lookup"><span data-stu-id="41108-111">Therefore, using the `Microsoft.AspNetCore.All` metapackage requires no further action to make Browser Link available for use.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="41108-112">O ASP.NET Core 1.x **aplicativo Web** modelo de projeto possui uma referência de pacote para o [browserlink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) pacote.</span><span class="sxs-lookup"><span data-stu-id="41108-112">The ASP.NET Core 1.x **Web Application** project template has a package reference for the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package.</span></span> <span data-ttu-id="41108-113">O **vazio** ou **API da Web** exigem que você adicione uma referência de pacote para projetos de modelo `Microsoft.VisualStudio.Web.BrowserLink`.</span><span class="sxs-lookup"><span data-stu-id="41108-113">The **Empty** or **Web API** template projects require you to add a package reference to `Microsoft.VisualStudio.Web.BrowserLink`.</span></span>

<span data-ttu-id="41108-114">Como esse é um recurso do Visual Studio, a maneira mais fácil para adicionar o pacote para um **vazio** ou **API da Web** projeto modelo é abrir o **Package Manager Console** (**Modo de exibição** > **Other Windows** > **Package Manager Console**) e execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="41108-114">Since this is a Visual Studio feature, the easiest way to add the package to an **Empty** or **Web API** template project is to open the **Package Manager Console** (**View** > **Other Windows** > **Package Manager Console**) and run the following command:</span></span>

```console
install-package Microsoft.VisualStudio.Web.BrowserLink
```

<span data-ttu-id="41108-115">Como alternativa, você pode usar **Gerenciador de pacotes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="41108-115">Alternatively, you can use **NuGet Package Manager**.</span></span> <span data-ttu-id="41108-116">Clique com botão direito no nome do projeto no **Gerenciador de soluções** e escolha **Manage NuGet Packages**:</span><span class="sxs-lookup"><span data-stu-id="41108-116">Right-click the project name in **Solution Explorer** and choose **Manage NuGet Packages**:</span></span>

![Gerenciador de pacotes NuGet aberto](using-browserlink/_static/open-nuget-package-manager.png)

<span data-ttu-id="41108-118">Localizar e instalar o pacote:</span><span class="sxs-lookup"><span data-stu-id="41108-118">Find and install the package:</span></span>

![Adicionar o pacote com o Gerenciador de pacotes do NuGet](using-browserlink/_static/add-package-with-nuget-package-manager.png)

::: moniker-end

### <a name="configuration"></a><span data-ttu-id="41108-120">Configuração</span><span class="sxs-lookup"><span data-stu-id="41108-120">Configuration</span></span>

<span data-ttu-id="41108-121">No método `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="41108-121">In the `Startup.Configure` method:</span></span>

```csharp
app.UseBrowserLink();
```

<span data-ttu-id="41108-122">Normalmente, o código está dentro de um `if` bloco que apenas permite que o Link do navegador no ambiente de desenvolvimento, como mostrado aqui:</span><span class="sxs-lookup"><span data-stu-id="41108-122">Usually the code is inside an `if` block that only enables Browser Link in the Development environment, as shown here:</span></span>

```csharp
if (env.IsDevelopment())
{
    app.UseDeveloperExceptionPage();
    app.UseBrowserLink();
}
```

<span data-ttu-id="41108-123">Para obter mais informações, veja [Usar vários ambientes](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="41108-123">For more information, see [Use multiple environments](xref:fundamentals/environments).</span></span>

## <a name="how-to-use-browser-link"></a><span data-ttu-id="41108-124">Como usar o Link do navegador</span><span class="sxs-lookup"><span data-stu-id="41108-124">How to use Browser Link</span></span>

<span data-ttu-id="41108-125">Quando um projeto do ASP.NET Core está aberto, o Visual Studio mostra o controle de barra de ferramentas do Link do navegador ao lado do controle de barra de ferramentas do **Destino de Depuração**:</span><span class="sxs-lookup"><span data-stu-id="41108-125">When you have an ASP.NET Core project open, Visual Studio shows the Browser Link toolbar control next to the **Debug Target** toolbar control:</span></span>

![Menu de lista suspensa de Link do navegador](using-browserlink/_static/browserLink-dropdown-menu.png)

<span data-ttu-id="41108-127">O controle de barra de ferramentas do Link do navegador, você pode:</span><span class="sxs-lookup"><span data-stu-id="41108-127">From the Browser Link toolbar control, you can:</span></span>

* <span data-ttu-id="41108-128">Atualizar o aplicativo Web em vários navegadores de uma vez.</span><span class="sxs-lookup"><span data-stu-id="41108-128">Refresh the web application in several browsers at once.</span></span>
* <span data-ttu-id="41108-129">Abrir o **Painel de link do navegador**.</span><span class="sxs-lookup"><span data-stu-id="41108-129">Open the **Browser Link Dashboard**.</span></span>
* <span data-ttu-id="41108-130">Habilitar ou desabilitar **Link do navegador**.</span><span class="sxs-lookup"><span data-stu-id="41108-130">Enable or disable **Browser Link**.</span></span> <span data-ttu-id="41108-131">Observação: Link do navegador está desabilitado por padrão no Visual Studio 2017 (15.3).</span><span class="sxs-lookup"><span data-stu-id="41108-131">Note: Browser Link is disabled by default in Visual Studio 2017 (15.3).</span></span>
* <span data-ttu-id="41108-132">Habilitar ou desabilitar [sincronização automática de CSS](#enable-or-disable-css-auto-sync).</span><span class="sxs-lookup"><span data-stu-id="41108-132">Enable or disable [CSS Auto-Sync](#enable-or-disable-css-auto-sync).</span></span>

> [!NOTE]
> <span data-ttu-id="41108-133">Alguns plug-ins do Visual Studio, mais notavelmente *Web extensão Pack 2015* e *2017 de pacote de extensão do Web*, oferecem funcionalidade estendida para o Link do navegador, mas alguns dos recursos adicionais não funcionam com o ASP. Projetos do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="41108-133">Some Visual Studio plug-ins, most notably *Web Extension Pack 2015* and *Web Extension Pack 2017*, offer extended functionality for Browser Link, but some of the additional features don't work with ASP.NET Core projects.</span></span>

## <a name="refresh-the-web-app-in-several-browsers-at-once"></a><span data-ttu-id="41108-134">Atualizar o aplicativo web em vários navegadores ao mesmo tempo</span><span class="sxs-lookup"><span data-stu-id="41108-134">Refresh the web app in several browsers at once</span></span>

<span data-ttu-id="41108-135">Para escolher um único navegador Web para abrir ao iniciar o projeto, use o menu suspenso do controle de barra de ferramentas do **Destino de depuração**:</span><span class="sxs-lookup"><span data-stu-id="41108-135">To choose a single web browser to launch when starting the project, use the drop-down menu in the **Debug Target** toolbar control:</span></span>

![Menu suspenso de F5](using-browserlink/_static/debug-target-dropdown-menu.png)

<span data-ttu-id="41108-137">Para abrir vários navegadores, ao mesmo tempo, escolha **procurar com...**  da mesma lista suspensa.</span><span class="sxs-lookup"><span data-stu-id="41108-137">To open multiple browsers at once, choose **Browse with...** from the same drop-down.</span></span> <span data-ttu-id="41108-138">Mantenha pressionada a tecla CTRL para selecionar os navegadores que você deseja e, em seguida, clique em **procurar**:</span><span class="sxs-lookup"><span data-stu-id="41108-138">Hold down the CTRL key to select the browsers you want, and then click **Browse**:</span></span>

![Abrir muitos navegadores ao mesmo tempo](using-browserlink/_static/open-many-browsers-at-once.png)

<span data-ttu-id="41108-140">Aqui está uma captura de tela mostrando o Visual Studio com o modo de exibição de índice aberto e dois navegadores abertas:</span><span class="sxs-lookup"><span data-stu-id="41108-140">Here's a screenshot showing Visual Studio with the Index view open and two open browsers:</span></span>

![Sincronizar com o exemplo de dois navegadores](using-browserlink/_static/sync-with-two-browsers-example.png)

<span data-ttu-id="41108-142">Passe o mouse sobre o controle de barra de ferramentas do Link do navegador para ver os navegadores que estão conectados ao projeto:</span><span class="sxs-lookup"><span data-stu-id="41108-142">Hover over the Browser Link toolbar control to see the browsers that are connected to the project:</span></span>

![Passe o mouse de dica](using-browserlink/_static/hoover-tip.png)

<span data-ttu-id="41108-144">Altere o modo de exibição de índice e todos os navegadores conectados são atualizados quando você clicar no botão de atualização de Link do navegador:</span><span class="sxs-lookup"><span data-stu-id="41108-144">Change the Index view, and all connected browsers are updated when you click the Browser Link refresh button:</span></span>

![browsers-sync-to-changes](using-browserlink/_static/browsers-sync-to-changes.png)

<span data-ttu-id="41108-146">Link do navegador também funciona com navegadores que você inicie de fora do Visual Studio e navegue até a URL do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="41108-146">Browser Link also works with browsers that you launch from outside Visual Studio and navigate to the application URL.</span></span>

### <a name="the-browser-link-dashboard"></a><span data-ttu-id="41108-147">O painel de Link do navegador</span><span class="sxs-lookup"><span data-stu-id="41108-147">The Browser Link Dashboard</span></span>

<span data-ttu-id="41108-148">Abra o painel de Link do navegador de menu para gerenciar a conexão com o navegador abertas suspenso Link do navegador:</span><span class="sxs-lookup"><span data-stu-id="41108-148">Open the Browser Link Dashboard from the Browser Link drop down menu to manage the connection with open browsers:</span></span>

![open-browserslink-dashboard](using-browserlink/_static/open-browserlink-dashboard.png)

<span data-ttu-id="41108-150">Se nenhum navegador estiver conectado, você pode iniciar uma sessão de depuração não, selecionando Se nenhum navegador estiver conectado, você poderá iniciar uma sessão de não depuração selecionando o link *exibir no navegador*:</span><span class="sxs-lookup"><span data-stu-id="41108-150">If no browser is connected, you can start a non-debugging session by selecting the *View in Browser* link:</span></span>

![browserlink-dashboard-no-connections](using-browserlink/_static/browserlink-dashboard-no-connections.png)

<span data-ttu-id="41108-152">Caso contrário, os navegadores conectados são mostrados com o caminho para a página que mostra cada navegador:</span><span class="sxs-lookup"><span data-stu-id="41108-152">Otherwise, the connected browsers are shown with the path to the page that each browser is showing:</span></span>

![browserlink-dashboard-two-connections](using-browserlink/_static/browserlink-dashboard-two-connections.png)

<span data-ttu-id="41108-154">Se desejar, você pode clicar em um nome de navegador listados para atualizar esse único navegador.</span><span class="sxs-lookup"><span data-stu-id="41108-154">If you like, you can click on a listed browser name to refresh that single browser.</span></span>

### <a name="enable-or-disable-browser-link"></a><span data-ttu-id="41108-155">Habilitar ou desabilitar o Link do navegador</span><span class="sxs-lookup"><span data-stu-id="41108-155">Enable or disable Browser Link</span></span>

<span data-ttu-id="41108-156">Quando você habilita novamente o Link do navegador depois de desabilitá-lo, você deve atualizar os navegadores para reconectar-se-los.</span><span class="sxs-lookup"><span data-stu-id="41108-156">When you re-enable Browser Link after disabling it, you must refresh the browsers to reconnect them.</span></span>

### <a name="enable-or-disable-css-auto-sync"></a><span data-ttu-id="41108-157">Habilitar ou desabilitar a sincronização automática de CSS</span><span class="sxs-lookup"><span data-stu-id="41108-157">Enable or disable CSS Auto-Sync</span></span>

<span data-ttu-id="41108-158">Quando a sincronização automática de CSS está habilitada, os navegadores conectados serão atualizados automaticamente quando você fizer qualquer alteração em arquivos CSS.</span><span class="sxs-lookup"><span data-stu-id="41108-158">When CSS Auto-Sync is enabled, connected browsers are automatically refreshed when you make any change to CSS files.</span></span>

## <a name="how-it-works"></a><span data-ttu-id="41108-159">Como ele funciona</span><span class="sxs-lookup"><span data-stu-id="41108-159">How it works</span></span>

<span data-ttu-id="41108-160">Link do navegador usa o SignalR para criar um canal de comunicação entre o Visual Studio e o navegador.</span><span class="sxs-lookup"><span data-stu-id="41108-160">Browser Link uses SignalR to create a communication channel between Visual Studio and the browser.</span></span> <span data-ttu-id="41108-161">Quando o Link do navegador está habilitado, o Visual Studio atua como um servidor de SignalR que vários clientes (navegadores) podem se conectar ao.</span><span class="sxs-lookup"><span data-stu-id="41108-161">When Browser Link is enabled, Visual Studio acts as a SignalR server that multiple clients (browsers) can connect to.</span></span> <span data-ttu-id="41108-162">Link do navegador também registra um componente de middleware no pipeline de solicitação do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="41108-162">Browser Link also registers a middleware component in the ASP.NET Core request pipeline.</span></span> <span data-ttu-id="41108-163">Esse componente injeta especial `<script>` referências em cada solicitação de página do servidor.</span><span class="sxs-lookup"><span data-stu-id="41108-163">This component injects special `<script>` references into every page request from the server.</span></span> <span data-ttu-id="41108-164">Você pode ver as referências de script selecionando **Exibir código-fonte** no navegador e rolar até o final do `<body>` conteúdo de marca:</span><span class="sxs-lookup"><span data-stu-id="41108-164">You can see the script references by selecting **View source** in the browser and scrolling to the end of the `<body>` tag content:</span></span>

```html
    <!-- Visual Studio Browser Link -->
    <script type="application/json" id="__browserLink_initializationData">
        {"requestId":"a717d5a07c1741949a7cefd6fa2bad08","requestMappingFromServer":false}
    </script>
    <script type="text/javascript" src="http://localhost:54139/b6e36e429d034f578ebccd6a79bf19bf/browserLink" async="async"></script>
    <!-- End Browser Link -->
</body>
```

<span data-ttu-id="41108-165">Os arquivos de origem não são modificados.</span><span class="sxs-lookup"><span data-stu-id="41108-165">Your source files aren't modified.</span></span> <span data-ttu-id="41108-166">O componente de middleware injeta as referências de script dinamicamente.</span><span class="sxs-lookup"><span data-stu-id="41108-166">The middleware component injects the script references dynamically.</span></span>

<span data-ttu-id="41108-167">Como o código do navegador é todo JavaScript, ele funciona em todos os navegadores SignalR dá suporte a sem a necessidade de um plug-in de navegador.</span><span class="sxs-lookup"><span data-stu-id="41108-167">Because the browser-side code is all JavaScript, it works on all browsers that SignalR supports without requiring a browser plug-in.</span></span>
