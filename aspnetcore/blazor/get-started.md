---
title: Introdução ao ASP.NET Core Blazor
author: guardrex
description: Comece a usar o Blazor criando um aplicativo Blazor com as ferramentas de sua escolha.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
no-loc:
- Blazor
- SignalR
uid: blazor/get-started
ms.openlocfilehash: 09400a076849bdec35beb284a488d01feb8a84c2
ms.sourcegitcommit: 9ee99300a48c810ca6fd4f7700cd95c3ccb85972
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/17/2020
ms.locfileid: "76159996"
---
# <a name="get-started-with-aspnet-core-opno-locblazor"></a><span data-ttu-id="7c4d7-103">Introdução ao ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="7c4d7-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="7c4d7-104">Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="7c4d7-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="7c4d7-105">Introdução ao Blazor:</span><span class="sxs-lookup"><span data-stu-id="7c4d7-105">Get started with Blazor:</span></span>

1. <span data-ttu-id="7c4d7-106">Instale o [SDK do .NET Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="7c4d7-106">Install the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="7c4d7-107">Opcionalmente, instale o modelo [WebassemblyBlazor](xref:blazor/hosting-models#blazor-webassembly) :</span><span class="sxs-lookup"><span data-stu-id="7c4d7-107">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) template:</span></span>
   * <span data-ttu-id="7c4d7-108">Instale o [SDK do .NET Core 3,1 ou posterior (versão prévia)](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="7c4d7-108">Install the [.NET Core 3.1 or later (Preview) SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>
   * <span data-ttu-id="7c4d7-109">Execute o comando a seguir em um shell de comando.</span><span class="sxs-lookup"><span data-stu-id="7c4d7-109">Run the following command in a command shell.</span></span> <span data-ttu-id="7c4d7-110">O [Microsoft. AspNetCore.Blazor. ](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/)O pacote de modelos tem uma versão de visualização enquanto Blazor Webassembly está em visualização.</span><span class="sxs-lookup"><span data-stu-id="7c4d7-110">The [Microsoft.AspNetCore.Blazor.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/) package has a preview version while Blazor WebAssembly is in preview.</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.1.0-preview4.19579.2
   ```

1. <span data-ttu-id="7c4d7-111">Siga as orientações para sua escolha de ferramentas:</span><span class="sxs-lookup"><span data-stu-id="7c4d7-111">Follow the guidance for your choice of tooling:</span></span>

   # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7c4d7-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7c4d7-112">Visual Studio</span></span>](#tab/visual-studio)

   <span data-ttu-id="7c4d7-113">1 \.</span><span class="sxs-lookup"><span data-stu-id="7c4d7-113">1\.</span></span> <span data-ttu-id="7c4d7-114">Instale o [Visual Studio 16,4 ou posterior](https://visualstudio.microsoft.com/vs/preview/) com a carga de trabalho de **desenvolvimento da web e do ASP.net** .</span><span class="sxs-lookup"><span data-stu-id="7c4d7-114">Install [Visual Studio 16.4 or later](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="7c4d7-115">2 \.</span><span class="sxs-lookup"><span data-stu-id="7c4d7-115">2\.</span></span> <span data-ttu-id="7c4d7-116">Crie um novo projeto.</span><span class="sxs-lookup"><span data-stu-id="7c4d7-116">Create a new project.</span></span>

   <span data-ttu-id="7c4d7-117">3 \.</span><span class="sxs-lookup"><span data-stu-id="7c4d7-117">3\.</span></span> <span data-ttu-id="7c4d7-118">Selecione **Blazor aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="7c4d7-118">Select **Blazor App**.</span></span> <span data-ttu-id="7c4d7-119">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="7c4d7-119">Select **Next**.</span></span>

   <span data-ttu-id="7c4d7-120">4 \.</span><span class="sxs-lookup"><span data-stu-id="7c4d7-120">4\.</span></span> <span data-ttu-id="7c4d7-121">Forneça um nome ao projeto no campo **Nome do projeto** ou aceite o nome do projeto padrão.</span><span class="sxs-lookup"><span data-stu-id="7c4d7-121">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="7c4d7-122">Confirme se a entrada de **local** está correta ou forneça um local para o projeto.</span><span class="sxs-lookup"><span data-stu-id="7c4d7-122">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="7c4d7-123">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="7c4d7-123">Select **Create**.</span></span>

   <span data-ttu-id="7c4d7-124">5 \.</span><span class="sxs-lookup"><span data-stu-id="7c4d7-124">5\.</span></span> <span data-ttu-id="7c4d7-125">Para obter uma experiência de Webassembly Blazor, escolha o modelo de **aplicativoBlazor Webassembly** .</span><span class="sxs-lookup"><span data-stu-id="7c4d7-125">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="7c4d7-126">Para obter uma experiência de Blazor Server, escolha o modelo de **aplicativoBlazor Server** .</span><span class="sxs-lookup"><span data-stu-id="7c4d7-126">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="7c4d7-127">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="7c4d7-127">Select **Create**.</span></span> <span data-ttu-id="7c4d7-128">Para obter informações sobre os dois modelos de hospedagem Blazor, *Blazor Server* e *Blazor Webassembly*, consulte <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="7c4d7-128">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="7c4d7-129">6 \.</span><span class="sxs-lookup"><span data-stu-id="7c4d7-129">6\.</span></span> <span data-ttu-id="7c4d7-130">Pressione **Ctrl**+**F5** para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7c4d7-130">Press **Ctrl**+**F5** to run the app.</span></span>

   > [!NOTE]
   > <span data-ttu-id="7c4d7-131">Se você instalou a extensão do Blazor Visual Studio para uma versão prévia anterior do ASP.NET Core Blazor (visualização 6 ou anterior), você pode desinstalar a extensão.</span><span class="sxs-lookup"><span data-stu-id="7c4d7-131">If you installed the Blazor Visual Studio extension for a prior preview release of ASP.NET Core Blazor (Preview 6 or earlier), you can uninstall the extension.</span></span> <span data-ttu-id="7c4d7-132">Instalar os modelos de Blazor em um shell de comando agora é suficiente para retonar os modelos no Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="7c4d7-132">Installing the Blazor templates in a command shell is now sufficient to surface the templates in Visual Studio.</span></span>

   # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="7c4d7-133">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7c4d7-133">Visual Studio Code</span></span>](#tab/visual-studio-code)

   <span data-ttu-id="7c4d7-134">1 \.</span><span class="sxs-lookup"><span data-stu-id="7c4d7-134">1\.</span></span> <span data-ttu-id="7c4d7-135">Instalar o [Visual Studio Code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="7c4d7-135">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

   <span data-ttu-id="7c4d7-136">2 \.</span><span class="sxs-lookup"><span data-stu-id="7c4d7-136">2\.</span></span> <span data-ttu-id="7c4d7-137">Instale o mais recente [ C# para a extensão de Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span><span class="sxs-lookup"><span data-stu-id="7c4d7-137">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span></span>

   <span data-ttu-id="7c4d7-138">3 \.</span><span class="sxs-lookup"><span data-stu-id="7c4d7-138">3\.</span></span> <span data-ttu-id="7c4d7-139">Para obter uma experiência de Webassembly Blazor, execute o seguinte comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="7c4d7-139">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorwasm -o WebApplication1
      ```

      <span data-ttu-id="7c4d7-140">Para obter uma experiência de Blazor Server, execute o seguinte comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="7c4d7-140">For a Blazor Server experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorserver -o WebApplication1
      ```

      <span data-ttu-id="7c4d7-141">Para obter informações sobre os dois modelos de hospedagem Blazor, *Blazor Server* e *Blazor Webassembly*, consulte <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="7c4d7-141">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="7c4d7-142">4 \.</span><span class="sxs-lookup"><span data-stu-id="7c4d7-142">4\.</span></span> <span data-ttu-id="7c4d7-143">Abra a pasta *WebApplication1* em Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="7c4d7-143">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

   <span data-ttu-id="7c4d7-144">5 \.</span><span class="sxs-lookup"><span data-stu-id="7c4d7-144">5\.</span></span> <span data-ttu-id="7c4d7-145">Para um projeto do Blazor Server, o IDE solicita que você adicione ativos para compilar e depurar o projeto.</span><span class="sxs-lookup"><span data-stu-id="7c4d7-145">For a Blazor Server project, the IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="7c4d7-146">Selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="7c4d7-146">Select **Yes**.</span></span>

   <span data-ttu-id="7c4d7-147">6 \.</span><span class="sxs-lookup"><span data-stu-id="7c4d7-147">6\.</span></span> <span data-ttu-id="7c4d7-148">Se estiver usando um aplicativo do Blazor Server, execute o aplicativo usando o depurador de Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="7c4d7-148">If using a Blazor Server app, run the app using the Visual Studio Code debugger.</span></span> <span data-ttu-id="7c4d7-149">Se estiver usando um aplicativo Webassembly Blazor, execute `dotnet run` na pasta do projeto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7c4d7-149">If using a Blazor WebAssembly app, execute `dotnet run` from the app's project folder.</span></span>

   <span data-ttu-id="7c4d7-150">7 \.</span><span class="sxs-lookup"><span data-stu-id="7c4d7-150">7\.</span></span> <span data-ttu-id="7c4d7-151">Em um navegador, navegue até `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="7c4d7-151">In a browser, navigate to `https://localhost:5001`.</span></span>

   # <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="7c4d7-152">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="7c4d7-152">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

   <span data-ttu-id="7c4d7-153">1 \.</span><span class="sxs-lookup"><span data-stu-id="7c4d7-153">1\.</span></span> <span data-ttu-id="7c4d7-154">Instale o [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="7c4d7-154">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

   <span data-ttu-id="7c4d7-155">2 \.</span><span class="sxs-lookup"><span data-stu-id="7c4d7-155">2\.</span></span> <span data-ttu-id="7c4d7-156">Selecione **arquivo** > **nova solução** ou crie um **novo projeto**.</span><span class="sxs-lookup"><span data-stu-id="7c4d7-156">Select **File** > **New Solution** or create a **New Project**.</span></span>

   <span data-ttu-id="7c4d7-157">3 \.</span><span class="sxs-lookup"><span data-stu-id="7c4d7-157">3\.</span></span> <span data-ttu-id="7c4d7-158">Na barra lateral, selecione **.NET Core** > **aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="7c4d7-158">In the sidebar, select **.NET Core** > **App**.</span></span>

   <span data-ttu-id="7c4d7-159">4 \.</span><span class="sxs-lookup"><span data-stu-id="7c4d7-159">4\.</span></span> <span data-ttu-id="7c4d7-160">Selecione o modelo de **aplicativo doBlazor Server** .</span><span class="sxs-lookup"><span data-stu-id="7c4d7-160">Select the **Blazor Server App** template.</span></span> <span data-ttu-id="7c4d7-161">Somente o modelo do Blazor Server está disponível no Visual Studio para Mac no momento.</span><span class="sxs-lookup"><span data-stu-id="7c4d7-161">Only the Blazor Server template is available in Visual Studio for Mac at this time.</span></span> <span data-ttu-id="7c4d7-162">Para obter uma experiência de Webassembly Blazor, siga as instruções na guia **CLI do .NET Core** . Depois de selecionar o modelo do Blazor Server, selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="7c4d7-162">For a Blazor WebAssembly experience, follow the instructions on the **.NET Core CLI** tab. After selecting the Blazor Server template, select **Next**.</span></span> <span data-ttu-id="7c4d7-163">Para obter informações sobre os dois modelos de hospedagem Blazor, *Blazor Server* e *Blazor Webassembly*, consulte <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="7c4d7-163">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <!-- For a Blazor WebAssembly experience, select the **Blazor WebAssembly App** template. Select **Next**. -->

   <span data-ttu-id="7c4d7-164">5 \.</span><span class="sxs-lookup"><span data-stu-id="7c4d7-164">5\.</span></span> <span data-ttu-id="7c4d7-165">Defina a **estrutura de destino** como **.NET Core 3,1** e selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="7c4d7-165">Set the **Target Framework** to **.NET Core 3.1** and select **Next**.</span></span>

   <span data-ttu-id="7c4d7-166">6 \.</span><span class="sxs-lookup"><span data-stu-id="7c4d7-166">6\.</span></span> <span data-ttu-id="7c4d7-167">No campo **nome do projeto** , nomeie o aplicativo `WebApplication1`.</span><span class="sxs-lookup"><span data-stu-id="7c4d7-167">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="7c4d7-168">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="7c4d7-168">Select **Create**.</span></span>

   <span data-ttu-id="7c4d7-169">7 \.</span><span class="sxs-lookup"><span data-stu-id="7c4d7-169">7\.</span></span> <span data-ttu-id="7c4d7-170">Selecione **executar** > **executar sem depuração** para executar o aplicativo *sem o depurador*.</span><span class="sxs-lookup"><span data-stu-id="7c4d7-170">Select **Run** > **Run Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="7c4d7-171">Execute o aplicativo com **Iniciar Depuração** para executar o aplicativo *com o depurador*.</span><span class="sxs-lookup"><span data-stu-id="7c4d7-171">Run the app with **Start Debugging** to run the app *with the debugger*.</span></span>

   <span data-ttu-id="7c4d7-172">Se aparecer um prompt para confiar no certificado de desenvolvimento, confie no certificado e continue.</span><span class="sxs-lookup"><span data-stu-id="7c4d7-172">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span>

   # <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="7c4d7-173">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="7c4d7-173">.NET Core CLI</span></span>](#tab/netcore-cli/)

   <span data-ttu-id="7c4d7-174">Para obter uma experiência de Webassembly Blazor, execute os seguintes comandos em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="7c4d7-174">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="7c4d7-175">Para obter uma experiência de Blazor Server, execute os seguintes comandos em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="7c4d7-175">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="7c4d7-176">Para obter informações sobre os dois modelos de hospedagem Blazor, *Blazor Server* e *Blazor Webassembly*, consulte <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="7c4d7-176">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="7c4d7-177">Em um navegador, navegue até `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="7c4d7-177">In a browser, navigate to `https://localhost:5001`.</span></span>

   ---

<span data-ttu-id="7c4d7-178">Várias páginas estão disponíveis em guias na barra lateral:</span><span class="sxs-lookup"><span data-stu-id="7c4d7-178">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="7c4d7-179">Página inicial do</span><span class="sxs-lookup"><span data-stu-id="7c4d7-179">Home</span></span>
* <span data-ttu-id="7c4d7-180">Contador</span><span class="sxs-lookup"><span data-stu-id="7c4d7-180">Counter</span></span>
* <span data-ttu-id="7c4d7-181">Buscar dados</span><span class="sxs-lookup"><span data-stu-id="7c4d7-181">Fetch data</span></span>

<span data-ttu-id="7c4d7-182">Na página Contador, selecione o botão **Clique aqui** para incrementar o contador sem uma atualização de página.</span><span class="sxs-lookup"><span data-stu-id="7c4d7-182">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="7c4d7-183">Incrementar um contador em uma página da Web normalmente requer a escrita de JavaScript, mas com Blazor C#você pode usar.</span><span class="sxs-lookup"><span data-stu-id="7c4d7-183">Incrementing a counter in a webpage normally requires writing JavaScript, but with Blazor you can use C#.</span></span>

<span data-ttu-id="7c4d7-184">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="7c4d7-184">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="7c4d7-185">Uma solicitação de `/counter` no navegador, conforme especificado pela diretiva `@page` na parte superior, faz com que o componente `Counter` processe seu conteúdo.</span><span class="sxs-lookup"><span data-stu-id="7c4d7-185">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="7c4d7-186">Os componentes são renderizados em uma representação na memória da árvore de renderização que pode ser usada para atualizar a interface do usuário de maneira flexível e eficiente.</span><span class="sxs-lookup"><span data-stu-id="7c4d7-186">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="7c4d7-187">Sempre que o botão **clicar em mim** estiver selecionado:</span><span class="sxs-lookup"><span data-stu-id="7c4d7-187">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="7c4d7-188">O evento `onclick` é acionado.</span><span class="sxs-lookup"><span data-stu-id="7c4d7-188">The `onclick` event is fired.</span></span>
* <span data-ttu-id="7c4d7-189">O método `IncrementCount` é chamado.</span><span class="sxs-lookup"><span data-stu-id="7c4d7-189">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="7c4d7-190">O `currentCount` é incrementado.</span><span class="sxs-lookup"><span data-stu-id="7c4d7-190">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="7c4d7-191">O componente é renderizado novamente.</span><span class="sxs-lookup"><span data-stu-id="7c4d7-191">The component is rendered again.</span></span>

<span data-ttu-id="7c4d7-192">O tempo de execução compara o novo conteúdo com o conteúdo anterior e aplica apenas o conteúdo alterado para o Modelo de Objeto do Documento (DOM).</span><span class="sxs-lookup"><span data-stu-id="7c4d7-192">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="7c4d7-193">Adicione um componente a outro componente usando a sintaxe HTML.</span><span class="sxs-lookup"><span data-stu-id="7c4d7-193">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="7c4d7-194">Por exemplo, adicione o componente `Counter` à Home Page do aplicativo adicionando um elemento `<Counter />` ao componente `Index`.</span><span class="sxs-lookup"><span data-stu-id="7c4d7-194">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="7c4d7-195">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="7c4d7-195">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="7c4d7-196">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7c4d7-196">Run the app.</span></span> <span data-ttu-id="7c4d7-197">A Home Page tem seu próprio contador fornecido pelo componente `Counter`.</span><span class="sxs-lookup"><span data-stu-id="7c4d7-197">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="7c4d7-198">Os parâmetros de componente são especificados usando atributos ou [conteúdo filho](xref:blazor/components#child-content), que permitem definir propriedades no componente filho.</span><span class="sxs-lookup"><span data-stu-id="7c4d7-198">Component parameters are specified using attributes or [child content](xref:blazor/components#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="7c4d7-199">Para adicionar um parâmetro ao componente `Counter`, atualize o bloco de `@code` do componente:</span><span class="sxs-lookup"><span data-stu-id="7c4d7-199">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="7c4d7-200">Adicione uma propriedade pública para `IncrementAmount` com um atributo `[Parameter]`.</span><span class="sxs-lookup"><span data-stu-id="7c4d7-200">Add a public property for `IncrementAmount` with a `[Parameter]` attribute.</span></span>
* <span data-ttu-id="7c4d7-201">Altere o método `IncrementCount` para usar o `IncrementAmount` ao aumentar o valor de `currentCount`.</span><span class="sxs-lookup"><span data-stu-id="7c4d7-201">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="7c4d7-202">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="7c4d7-202">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="7c4d7-203">Especifique o `IncrementAmount` no elemento `<Counter>` do componente de `Index` usando um atributo.</span><span class="sxs-lookup"><span data-stu-id="7c4d7-203">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="7c4d7-204">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="7c4d7-204">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="7c4d7-205">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7c4d7-205">Run the app.</span></span> <span data-ttu-id="7c4d7-206">O componente `Index` tem seu próprio contador que é incrementado em dez cada vez que o botão **Click me** está selecionado.</span><span class="sxs-lookup"><span data-stu-id="7c4d7-206">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="7c4d7-207">O componente `Counter` (*Counter. Razor*) em `/counter` continua a incrementar um.</span><span class="sxs-lookup"><span data-stu-id="7c4d7-207">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="7c4d7-208">{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}</span><span class="sxs-lookup"><span data-stu-id="7c4d7-208">Next steps</span></span>

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="7c4d7-209">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="7c4d7-209">Additional resources</span></span>

* <xref:blazor/templates>
* <xref:signalr/introduction>
