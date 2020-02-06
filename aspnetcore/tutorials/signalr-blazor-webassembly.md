---
title: Usar ASP.NET Core SignalR com Blazor Webassembly
author: guardrex
description: Crie um aplicativo de chat que usa ASP.NET Core SignalR com Blazor Webassembly.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/31/2020
no-loc:
- Blazor
- SignalR
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: d3605c0823e9ec3ce34fb781da66a7470aa00622
ms.sourcegitcommit: 0e21d4f8111743bcb205a2ae0f8e57910c3e8c25
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77034174"
---
# <a name="use-aspnet-core-signalr-with-blazor-webassembly"></a><span data-ttu-id="23fa7-103">Usar Signalr ASP.NET Core com Webassembly de mais incrivelmente</span><span class="sxs-lookup"><span data-stu-id="23fa7-103">Use ASP.NET Core SignalR with Blazor WebAssembly</span></span>

<span data-ttu-id="23fa7-104">Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="23fa7-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="23fa7-105">Este tutorial ensina as noções básicas da criação de um aplicativo em tempo real usando Signalr com Webassembly mais alto.</span><span class="sxs-lookup"><span data-stu-id="23fa7-105">This tutorial teaches the basics of building a real-time app using SignalR with Blazor WebAssembly.</span></span> <span data-ttu-id="23fa7-106">Você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="23fa7-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="23fa7-107">Criar um projeto de aplicativo hospedado Webassembly de mais incrivelmente</span><span class="sxs-lookup"><span data-stu-id="23fa7-107">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="23fa7-108">Adicionar a biblioteca de clientes do SignalR</span><span class="sxs-lookup"><span data-stu-id="23fa7-108">Add the SignalR client library</span></span>
> * <span data-ttu-id="23fa7-109">Adicionar um Hub do Signalr</span><span class="sxs-lookup"><span data-stu-id="23fa7-109">Add a SignalR hub</span></span>
> * <span data-ttu-id="23fa7-110">Adicionar serviços do Signalr e um ponto de extremidade para o Hub do Signalr</span><span class="sxs-lookup"><span data-stu-id="23fa7-110">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="23fa7-111">Adicionar código de componente Razor para chat</span><span class="sxs-lookup"><span data-stu-id="23fa7-111">Add Razor component code for chat</span></span>

<span data-ttu-id="23fa7-112">No final deste tutorial, você terá um aplicativo de chat em funcionamento.</span><span class="sxs-lookup"><span data-stu-id="23fa7-112">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="23fa7-113">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="23fa7-113">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="23fa7-114">Prerequisites</span><span class="sxs-lookup"><span data-stu-id="23fa7-114">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="23fa7-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="23fa7-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="23fa7-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="23fa7-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="23fa7-117">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="23fa7-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="23fa7-118">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="23fa7-118">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

## <a name="create-a-hosted-blazor-webassembly-app-project"></a><span data-ttu-id="23fa7-119">Criar um projeto de aplicativo Webassembly de mais incrivelmente hospedado</span><span class="sxs-lookup"><span data-stu-id="23fa7-119">Create a hosted Blazor WebAssembly app project</span></span>

<span data-ttu-id="23fa7-120">Instale o modelo [Webassembly mais incrivelmente](xref:blazor/hosting-models#blazor-webassembly) .</span><span class="sxs-lookup"><span data-stu-id="23fa7-120">Install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) template.</span></span> <span data-ttu-id="23fa7-121">O pacote [Microsoft. AspNetCore. incrivelmente. templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/) tem uma versão de visualização enquanto o Webassembly de mais de baixo está em visualização.</span><span class="sxs-lookup"><span data-stu-id="23fa7-121">The [Microsoft.AspNetCore.Blazor.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/) package has a preview version while Blazor WebAssembly is in preview.</span></span> <span data-ttu-id="23fa7-122">Em um shell de comando, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="23fa7-122">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.2.0-preview1.20073.1
```

<span data-ttu-id="23fa7-123">Siga as orientações para sua escolha de ferramentas:</span><span class="sxs-lookup"><span data-stu-id="23fa7-123">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="23fa7-124">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="23fa7-124">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="23fa7-125">Criar um novo projeto.</span><span class="sxs-lookup"><span data-stu-id="23fa7-125">Create a new project.</span></span>

1. <span data-ttu-id="23fa7-126">Selecione **aplicativo** mais novo e selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="23fa7-126">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="23fa7-127">Digite "BlazorSignalRApp" no campo **nome do projeto** .</span><span class="sxs-lookup"><span data-stu-id="23fa7-127">Type "BlazorSignalRApp" in the **Project name** field.</span></span> <span data-ttu-id="23fa7-128">Confirme se a entrada de **local** está correta ou forneça um local para o projeto.</span><span class="sxs-lookup"><span data-stu-id="23fa7-128">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="23fa7-129">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="23fa7-129">Select **Create**.</span></span>

1. <span data-ttu-id="23fa7-130">Escolha o modelo de **aplicativo Webassembly mais incrivelmente** .</span><span class="sxs-lookup"><span data-stu-id="23fa7-130">Choose the **Blazor WebAssembly App** template.</span></span>

1. <span data-ttu-id="23fa7-131">Em **avançado**, marque a caixa de seleção **ASP.NET Core hospedado** .</span><span class="sxs-lookup"><span data-stu-id="23fa7-131">Under **Advanced**, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="23fa7-132">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="23fa7-132">Select **Create**.</span></span>

> [!NOTE]
> <span data-ttu-id="23fa7-133">Se você atualizou ou instalou uma nova versão do Visual Studio e o modelo Webassembly mais novo não aparece na interface do usuário do VS, reinstale o modelo usando o comando `dotnet new` mostrado anteriormente.</span><span class="sxs-lookup"><span data-stu-id="23fa7-133">If you upgraded or installed a new version of Visual Studio and the Blazor WebAssembly template doesn't appear in the VS UI, reinstall the template using the `dotnet new` command shown previously.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="23fa7-134">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="23fa7-134">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="23fa7-135">Em um shell de comando, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="23fa7-135">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. <span data-ttu-id="23fa7-136">Em Visual Studio Code, abra a pasta do projeto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="23fa7-136">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="23fa7-137">Quando a caixa de diálogo aparecer para adicionar ativos para compilar e depurar o aplicativo, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="23fa7-137">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="23fa7-138">Visual Studio Code adiciona automaticamente a pasta *. vscode* com arquivos *Launch. JSON* e *Tasks. JSON* gerados.</span><span class="sxs-lookup"><span data-stu-id="23fa7-138">Visual Studio Code automatically adds the *.vscode* folder with generated *launch.json* and *tasks.json* files.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="23fa7-139">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="23fa7-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="23fa7-140">Em um shell de comando, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="23fa7-140">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. <span data-ttu-id="23fa7-141">No Visual Studio para Mac, abra o projeto navegando até a pasta do projeto e abrindo o arquivo de solução do projeto ( *. sln*).</span><span class="sxs-lookup"><span data-stu-id="23fa7-141">In Visual Studio for Mac, open the project by navigating to the project folder and opening the project's solution file (*.sln*).</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="23fa7-142">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="23fa7-142">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="23fa7-143">Em um shell de comando, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="23fa7-143">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="23fa7-144">Adicionar a biblioteca de clientes do SignalR</span><span class="sxs-lookup"><span data-stu-id="23fa7-144">Add the SignalR client library</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="23fa7-145">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="23fa7-145">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="23fa7-146">Em **Gerenciador de soluções**, clique com o botão direito do mouse no projeto **BlazorSignalRApp. Client** e selecione **gerenciar pacotes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="23fa7-146">In **Solution Explorer**, right-click the **BlazorSignalRApp.Client** project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="23fa7-147">Na caixa de diálogo **gerenciar pacotes NuGet** , confirme se a **origem do pacote** está definida como *NuGet.org*.</span><span class="sxs-lookup"><span data-stu-id="23fa7-147">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to *nuget.org*.</span></span>

1. <span data-ttu-id="23fa7-148">Com **procurar** selecionado, digite "Microsoft. AspNetCore. signaler. Client" na caixa de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="23fa7-148">With **Browse** selected, type "Microsoft.AspNetCore.SignalR.Client" in the search box.</span></span>

1. <span data-ttu-id="23fa7-149">Nos resultados da pesquisa, selecione o pacote `Microsoft.AspNetCore.SignalR.Client` e selecione **instalar**.</span><span class="sxs-lookup"><span data-stu-id="23fa7-149">In the search results, select the `Microsoft.AspNetCore.SignalR.Client` package and select **Install**.</span></span>

1. <span data-ttu-id="23fa7-150">Se a caixa de diálogo **Visualizar alterações** for exibida, selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="23fa7-150">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="23fa7-151">Se a caixa de diálogo de **aceitação da licença** for exibida **, selecione aceito** se você concordar com os termos de licença.</span><span class="sxs-lookup"><span data-stu-id="23fa7-151">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="23fa7-152">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="23fa7-152">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="23fa7-153">No **terminal integrado** (**Exibir** > **terminal** na barra de ferramentas), execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="23fa7-153">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="23fa7-154">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="23fa7-154">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="23fa7-155">Na barra lateral da **solução** , clique com o botão direito do mouse no projeto **BlazorSignalRApp. Client** e selecione **gerenciar pacotes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="23fa7-155">In the **Solution** sidebar, right-click the **BlazorSignalRApp.Client** project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="23fa7-156">Na caixa de diálogo **gerenciar pacotes NuGet** , confirme se a lista suspensa origem está definida como *NuGet.org*.</span><span class="sxs-lookup"><span data-stu-id="23fa7-156">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to *nuget.org*.</span></span>

1. <span data-ttu-id="23fa7-157">Com **procurar** selecionado, digite "Microsoft. AspNetCore. signaler. Client" na caixa de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="23fa7-157">With **Browse** selected, type "Microsoft.AspNetCore.SignalR.Client" in the search box.</span></span>

1. <span data-ttu-id="23fa7-158">Nos resultados da pesquisa, marque a caixa de seleção ao lado do pacote de `Microsoft.AspNetCore.SignalR.Client` e selecione **Adicionar pacote**.</span><span class="sxs-lookup"><span data-stu-id="23fa7-158">In the search results, select the check box next to the `Microsoft.AspNetCore.SignalR.Client` package and select **Add Package**.</span></span>

1. <span data-ttu-id="23fa7-159">Se a caixa de diálogo de **aceitação da licença** for exibida, selecione **aceitar** se você concordar com os termos de licença.</span><span class="sxs-lookup"><span data-stu-id="23fa7-159">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="23fa7-160">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="23fa7-160">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="23fa7-161">Em um shell de comando, execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="23fa7-161">In a command shell, execute the following commands:</span></span>

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="add-a-signalr-hub"></a><span data-ttu-id="23fa7-162">Adicionar um Hub do Signalr</span><span class="sxs-lookup"><span data-stu-id="23fa7-162">Add a SignalR hub</span></span>

<span data-ttu-id="23fa7-163">No projeto **BlazorSignalRApp. Server** , crie uma pasta *hubs* (plural) e adicione a seguinte classe de `ChatHub` (*hubs/ChatHub. cs*):</span><span class="sxs-lookup"><span data-stu-id="23fa7-163">In the **BlazorSignalRApp.Server** project, create a *Hubs* (plural) folder and add the following `ChatHub` class (*Hubs/ChatHub.cs*):</span></span>

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

## <a name="add-signalr-services-and-an-endpoint-for-the-signalr-hub"></a><span data-ttu-id="23fa7-164">Adicionar serviços do Signalr e um ponto de extremidade para o Hub do Signalr</span><span class="sxs-lookup"><span data-stu-id="23fa7-164">Add SignalR services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="23fa7-165">No projeto **BlazorSignalRApp. Server** , abra o arquivo *Startup.cs* .</span><span class="sxs-lookup"><span data-stu-id="23fa7-165">In the **BlazorSignalRApp.Server** project, open the *Startup.cs* file.</span></span>

1. <span data-ttu-id="23fa7-166">Adicione o namespace para a classe de `ChatHub` na parte superior do arquivo:</span><span class="sxs-lookup"><span data-stu-id="23fa7-166">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

1. <span data-ttu-id="23fa7-167">Adicione os serviços de sinalização a `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="23fa7-167">Add the SignalR services to `Startup.ConfigureServices`:</span></span>

   ```csharp
   services.AddSignalR();
   ```

1. <span data-ttu-id="23fa7-168">Em `Startup.Configure` entre os pontos de extremidade para a rota do controlador padrão e o fallback do lado do cliente, adicione um ponto final para o Hub:</span><span class="sxs-lookup"><span data-stu-id="23fa7-168">In `Startup.Configure` between the endpoints for the default controller route and the client-side fallback, add an endpoint for the hub:</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet&highlight=4)]

## <a name="add-razor-component-code-for-chat"></a><span data-ttu-id="23fa7-169">Adicionar código de componente Razor para chat</span><span class="sxs-lookup"><span data-stu-id="23fa7-169">Add Razor component code for chat</span></span>

1. <span data-ttu-id="23fa7-170">No projeto **BlazorSignalRApp. Client** , abra o arquivo *pages/index. Razor* .</span><span class="sxs-lookup"><span data-stu-id="23fa7-170">In the **BlazorSignalRApp.Client** project, open the *Pages/Index.razor* file.</span></span>

1. <span data-ttu-id="23fa7-171">Substitua a marcação pelo código a seguir:</span><span class="sxs-lookup"><span data-stu-id="23fa7-171">Replace the markup with the following code:</span></span>

[!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

## <a name="run-the-app"></a><span data-ttu-id="23fa7-172">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="23fa7-172">Run the app</span></span>

1. <span data-ttu-id="23fa7-173">Siga as orientações para suas ferramentas:</span><span class="sxs-lookup"><span data-stu-id="23fa7-173">Follow the guidance for your tooling:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="23fa7-174">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="23fa7-174">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="23fa7-175">Em **Gerenciador de soluções**, selecione o projeto **BlazorSignalRApp. Server** .</span><span class="sxs-lookup"><span data-stu-id="23fa7-175">In **Solution Explorer**, select the **BlazorSignalRApp.Server** project.</span></span> <span data-ttu-id="23fa7-176">Pressione **Ctrl + F5** para executar o aplicativo sem depuração.</span><span class="sxs-lookup"><span data-stu-id="23fa7-176">Press **Ctrl+F5** to run the app without debugging.</span></span>

1. <span data-ttu-id="23fa7-177">Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="23fa7-177">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="23fa7-178">Escolha qualquer navegador, insira um nome e uma mensagem e selecione o botão **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="23fa7-178">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="23fa7-179">O nome e a mensagem são exibidos em ambas as páginas instantaneamente:</span><span class="sxs-lookup"><span data-stu-id="23fa7-179">The name and message are displayed on both pages instantly:</span></span>

   ![Aplicativo de exemplo Webassembly do signalr mais do que é aberto em duas janelas de navegador mostrando mensagens trocadas.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="23fa7-181">Aspas: *Star Trek VI: o país não descoberto* &copy;1991 de [extrema importância](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="23fa7-181">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="23fa7-182">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="23fa7-182">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="23fa7-183">Selecione **depurar** > **executar sem depuração** na barra de ferramentas.</span><span class="sxs-lookup"><span data-stu-id="23fa7-183">Select **Debug** > **Run Without Debugging** from the toolbar.</span></span>

1. <span data-ttu-id="23fa7-184">Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="23fa7-184">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="23fa7-185">Escolha qualquer navegador, insira um nome e uma mensagem e selecione o botão **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="23fa7-185">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="23fa7-186">O nome e a mensagem são exibidos em ambas as páginas instantaneamente:</span><span class="sxs-lookup"><span data-stu-id="23fa7-186">The name and message are displayed on both pages instantly:</span></span>

   ![Aplicativo de exemplo Webassembly do signalr mais do que é aberto em duas janelas de navegador mostrando mensagens trocadas.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="23fa7-188">Aspas: *Star Trek VI: o país não descoberto* &copy;1991 de [extrema importância](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="23fa7-188">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="23fa7-189">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="23fa7-189">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="23fa7-190">Na barra lateral da **solução** , selecione o projeto **BlazorSignalRApp. Server** .</span><span class="sxs-lookup"><span data-stu-id="23fa7-190">In the **Solution** sidebar, select the **BlazorSignalRApp.Server** project.</span></span> <span data-ttu-id="23fa7-191">No menu, selecione **executar** > **Iniciar sem depuração**.</span><span class="sxs-lookup"><span data-stu-id="23fa7-191">From the menu, select **Run** > **Start Without Debugging**.</span></span>

1. <span data-ttu-id="23fa7-192">Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="23fa7-192">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="23fa7-193">Escolha qualquer navegador, insira um nome e uma mensagem e selecione o botão **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="23fa7-193">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="23fa7-194">O nome e a mensagem são exibidos em ambas as páginas instantaneamente:</span><span class="sxs-lookup"><span data-stu-id="23fa7-194">The name and message are displayed on both pages instantly:</span></span>

   ![Aplicativo de exemplo Webassembly do signalr mais do que é aberto em duas janelas de navegador mostrando mensagens trocadas.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="23fa7-196">Aspas: *Star Trek VI: o país não descoberto* &copy;1991 de [extrema importância](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="23fa7-196">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="23fa7-197">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="23fa7-197">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="23fa7-198">Em um shell de comando, execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="23fa7-198">In a command shell, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="23fa7-199">Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="23fa7-199">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="23fa7-200">Escolha qualquer navegador, insira um nome e uma mensagem e selecione o botão **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="23fa7-200">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="23fa7-201">O nome e a mensagem são exibidos em ambas as páginas instantaneamente:</span><span class="sxs-lookup"><span data-stu-id="23fa7-201">The name and message are displayed on both pages instantly:</span></span>

   ![Aplicativo de exemplo Webassembly do signalr mais do que é aberto em duas janelas de navegador mostrando mensagens trocadas.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="23fa7-203">Aspas: *Star Trek VI: o país não descoberto* &copy;1991 de [extrema importância](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="23fa7-203">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

## <a name="next-steps"></a><span data-ttu-id="23fa7-204">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="23fa7-204">Next steps</span></span>

<span data-ttu-id="23fa7-205">Neste tutorial, você aprendeu a:</span><span class="sxs-lookup"><span data-stu-id="23fa7-205">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="23fa7-206">Criar um projeto de aplicativo hospedado do Webassembly Blazor</span><span class="sxs-lookup"><span data-stu-id="23fa7-206">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="23fa7-207">Adicionar a biblioteca de cliente SignalR</span><span class="sxs-lookup"><span data-stu-id="23fa7-207">Add the SignalR client library</span></span>
> * <span data-ttu-id="23fa7-208">Adicionar um hub de SignalR</span><span class="sxs-lookup"><span data-stu-id="23fa7-208">Add a SignalR hub</span></span>
> * <span data-ttu-id="23fa7-209">Adicionar serviços de SignalR e um ponto de extremidade para o Hub de SignalR</span><span class="sxs-lookup"><span data-stu-id="23fa7-209">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="23fa7-210">Adicionar código de componente Razor para chat</span><span class="sxs-lookup"><span data-stu-id="23fa7-210">Add Razor component code for chat</span></span>

<span data-ttu-id="23fa7-211">Para saber mais sobre como criar Blazor aplicativos, consulte a documentação do Blazor:</span><span class="sxs-lookup"><span data-stu-id="23fa7-211">To learn more about building Blazor apps, see the Blazor documentation:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/index>

## <a name="additional-resources"></a><span data-ttu-id="23fa7-212">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="23fa7-212">Additional resources</span></span>

* <xref:signalr/introduction>
