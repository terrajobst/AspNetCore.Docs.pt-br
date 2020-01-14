---
title: Introdução ao ASP.NET Core Blazor
author: guardrex
description: Comece a usar o Blazor criando um aplicativo Blazor com as ferramentas de sua escolha.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/09/2019
no-loc:
- Blazor
uid: blazor/get-started
ms.openlocfilehash: 554f4daff92a0839ee7679287a4618e9b51e0fe5
ms.sourcegitcommit: 925cdbd94613243f33bc7613a62ea34006219931
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/13/2020
ms.locfileid: "75921302"
---
# <a name="get-started-with-aspnet-core-opno-locblazor"></a><span data-ttu-id="6ac48-103">Introdução ao ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="6ac48-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="6ac48-104">Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="6ac48-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="6ac48-105">Introdução ao Blazor:</span><span class="sxs-lookup"><span data-stu-id="6ac48-105">Get started with Blazor:</span></span>

::: moniker range=">= aspnetcore-3.1"

1. <span data-ttu-id="6ac48-106">Instale o [SDK do .NET Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="6ac48-106">Install the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="6ac48-107">Opcionalmente, instale o modelo [WebassemblyBlazor](xref:blazor/hosting-models#blazor-webassembly) :</span><span class="sxs-lookup"><span data-stu-id="6ac48-107">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) template:</span></span>
   * <span data-ttu-id="6ac48-108">Instale o [SDK do .NET Core 3,1 ou posterior (versão prévia)](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="6ac48-108">Install the [.NET Core 3.1 or later (Preview) SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>
   * <span data-ttu-id="6ac48-109">Execute o comando a seguir em um shell de comando.</span><span class="sxs-lookup"><span data-stu-id="6ac48-109">Run the following command in a command shell.</span></span> <span data-ttu-id="6ac48-110">O [Microsoft.AspNetCore.Blazor.](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/)O pacote de modelos tem uma versão de visualização enquanto Blazor Webassembly está em visualização.</span><span class="sxs-lookup"><span data-stu-id="6ac48-110">The [Microsoft.AspNetCore.Blazor.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/) package has a preview version while Blazor WebAssembly is in preview.</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.1.0-preview4.19579.2
   ```

1. <span data-ttu-id="6ac48-111">Siga as orientações para sua escolha de ferramentas:</span><span class="sxs-lookup"><span data-stu-id="6ac48-111">Follow the guidance for your choice of tooling:</span></span>

   # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="6ac48-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6ac48-112">Visual Studio</span></span>](#tab/visual-studio)

   <span data-ttu-id="6ac48-113">1 \.</span><span class="sxs-lookup"><span data-stu-id="6ac48-113">1\.</span></span> <span data-ttu-id="6ac48-114">Instale o [Visual Studio 16,4 ou posterior](https://visualstudio.microsoft.com/vs/preview/) com a carga de trabalho de **desenvolvimento da web e do ASP.net** .</span><span class="sxs-lookup"><span data-stu-id="6ac48-114">Install [Visual Studio 16.4 or later](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="6ac48-115">2 \.</span><span class="sxs-lookup"><span data-stu-id="6ac48-115">2\.</span></span> <span data-ttu-id="6ac48-116">Crie um novo projeto.</span><span class="sxs-lookup"><span data-stu-id="6ac48-116">Create a new project.</span></span>

   <span data-ttu-id="6ac48-117">3 \.</span><span class="sxs-lookup"><span data-stu-id="6ac48-117">3\.</span></span> <span data-ttu-id="6ac48-118">Selecione **Blazor aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="6ac48-118">Select **Blazor App**.</span></span> <span data-ttu-id="6ac48-119">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="6ac48-119">Select **Next**.</span></span>

   <span data-ttu-id="6ac48-120">4 \.</span><span class="sxs-lookup"><span data-stu-id="6ac48-120">4\.</span></span> <span data-ttu-id="6ac48-121">Forneça um nome ao projeto no campo **Nome do projeto** ou aceite o nome do projeto padrão.</span><span class="sxs-lookup"><span data-stu-id="6ac48-121">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="6ac48-122">Confirme se a entrada de **local** está correta ou forneça um local para o projeto.</span><span class="sxs-lookup"><span data-stu-id="6ac48-122">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="6ac48-123">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="6ac48-123">Select **Create**.</span></span>

   <span data-ttu-id="6ac48-124">5 \.</span><span class="sxs-lookup"><span data-stu-id="6ac48-124">5\.</span></span> <span data-ttu-id="6ac48-125">Para obter uma experiência de Webassembly Blazor, escolha o modelo de **aplicativoBlazor Webassembly** .</span><span class="sxs-lookup"><span data-stu-id="6ac48-125">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="6ac48-126">Para obter uma experiência de Blazor Server, escolha o modelo de **aplicativoBlazor Server** .</span><span class="sxs-lookup"><span data-stu-id="6ac48-126">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="6ac48-127">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="6ac48-127">Select **Create**.</span></span> <span data-ttu-id="6ac48-128">Para obter informações sobre os dois modelos de hospedagem Blazor, *Blazor Server* e *Blazor Webassembly*, consulte <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="6ac48-128">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="6ac48-129">6 \.</span><span class="sxs-lookup"><span data-stu-id="6ac48-129">6\.</span></span> <span data-ttu-id="6ac48-130">Pressione **Ctrl**+**F5** para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6ac48-130">Press **Ctrl**+**F5** to run the app.</span></span>

   > [!NOTE]
   > <span data-ttu-id="6ac48-131">Se você instalou a extensão do Blazor Visual Studio para uma versão prévia anterior do ASP.NET Core Blazor (visualização 6 ou anterior), você pode desinstalar a extensão.</span><span class="sxs-lookup"><span data-stu-id="6ac48-131">If you installed the Blazor Visual Studio extension for a prior preview release of ASP.NET Core Blazor (Preview 6 or earlier), you can uninstall the extension.</span></span> <span data-ttu-id="6ac48-132">Instalar os modelos de Blazor em um shell de comando agora é suficiente para retonar os modelos no Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="6ac48-132">Installing the Blazor templates in a command shell is now sufficient to surface the templates in Visual Studio.</span></span>

   # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="6ac48-133">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6ac48-133">Visual Studio Code</span></span>](#tab/visual-studio-code)

   <span data-ttu-id="6ac48-134">1 \.</span><span class="sxs-lookup"><span data-stu-id="6ac48-134">1\.</span></span> <span data-ttu-id="6ac48-135">Instalar o [Visual Studio Code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="6ac48-135">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

   <span data-ttu-id="6ac48-136">2 \.</span><span class="sxs-lookup"><span data-stu-id="6ac48-136">2\.</span></span> <span data-ttu-id="6ac48-137">Instale o mais recente [ C# para a extensão de Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span><span class="sxs-lookup"><span data-stu-id="6ac48-137">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span></span>

   <span data-ttu-id="6ac48-138">3 \.</span><span class="sxs-lookup"><span data-stu-id="6ac48-138">3\.</span></span> <span data-ttu-id="6ac48-139">Para obter uma experiência de Webassembly Blazor, execute o seguinte comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="6ac48-139">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorwasm -o WebApplication1
      ```

      <span data-ttu-id="6ac48-140">Para obter uma experiência de Blazor Server, execute o seguinte comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="6ac48-140">For a Blazor Server experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorserver -o WebApplication1
      ```

      <span data-ttu-id="6ac48-141">Para obter informações sobre os dois modelos de hospedagem Blazor, *Blazor Server* e *Blazor Webassembly*, consulte <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="6ac48-141">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="6ac48-142">4 \.</span><span class="sxs-lookup"><span data-stu-id="6ac48-142">4\.</span></span> <span data-ttu-id="6ac48-143">Abra a pasta *WebApplication1* em Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="6ac48-143">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

   <span data-ttu-id="6ac48-144">5 \.</span><span class="sxs-lookup"><span data-stu-id="6ac48-144">5\.</span></span> <span data-ttu-id="6ac48-145">Para um projeto do Blazor Server, o IDE solicita que você adicione ativos para compilar e depurar o projeto.</span><span class="sxs-lookup"><span data-stu-id="6ac48-145">For a Blazor Server project, the IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="6ac48-146">Selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="6ac48-146">Select **Yes**.</span></span>

   <span data-ttu-id="6ac48-147">6 \.</span><span class="sxs-lookup"><span data-stu-id="6ac48-147">6\.</span></span> <span data-ttu-id="6ac48-148">Se estiver usando um aplicativo do Blazor Server, execute o aplicativo usando o depurador de Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="6ac48-148">If using a Blazor Server app, run the app using the Visual Studio Code debugger.</span></span> <span data-ttu-id="6ac48-149">Se estiver usando um aplicativo Webassembly Blazor, execute `dotnet run` na pasta do projeto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6ac48-149">If using a Blazor WebAssembly app, execute `dotnet run` from the app's project folder.</span></span>

   <span data-ttu-id="6ac48-150">7 \.</span><span class="sxs-lookup"><span data-stu-id="6ac48-150">7\.</span></span> <span data-ttu-id="6ac48-151">Em um navegador, navegue até `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="6ac48-151">In a browser, navigate to `https://localhost:5001`.</span></span>

   # <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="6ac48-152">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6ac48-152">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

   <span data-ttu-id="6ac48-153">1 \.</span><span class="sxs-lookup"><span data-stu-id="6ac48-153">1\.</span></span> <span data-ttu-id="6ac48-154">Instale o [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="6ac48-154">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

   <span data-ttu-id="6ac48-155">2 \.</span><span class="sxs-lookup"><span data-stu-id="6ac48-155">2\.</span></span> <span data-ttu-id="6ac48-156">Selecione **arquivo** > **nova solução** ou crie um **novo projeto**.</span><span class="sxs-lookup"><span data-stu-id="6ac48-156">Select **File** > **New Solution** or create a **New Project**.</span></span>

   <span data-ttu-id="6ac48-157">3 \.</span><span class="sxs-lookup"><span data-stu-id="6ac48-157">3\.</span></span> <span data-ttu-id="6ac48-158">Na barra lateral, selecione **.NET Core** > **aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="6ac48-158">In the sidebar, select **.NET Core** > **App**.</span></span>

   <span data-ttu-id="6ac48-159">4 \.</span><span class="sxs-lookup"><span data-stu-id="6ac48-159">4\.</span></span> <span data-ttu-id="6ac48-160">Selecione o modelo de **aplicativo doBlazor Server** .</span><span class="sxs-lookup"><span data-stu-id="6ac48-160">Select the **Blazor Server App** template.</span></span> <span data-ttu-id="6ac48-161">Somente o modelo do Blazor Server está disponível no Visual Studio para Mac no momento.</span><span class="sxs-lookup"><span data-stu-id="6ac48-161">Only the Blazor Server template is available in Visual Studio for Mac at this time.</span></span> <span data-ttu-id="6ac48-162">Para obter uma experiência de Webassembly Blazor, siga as instruções na guia **CLI do .NET Core** . Depois de selecionar o modelo do Blazor Server, selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="6ac48-162">For a Blazor WebAssembly experience, follow the instructions on the **.NET Core CLI** tab. After selecting the Blazor Server template, select **Next**.</span></span> <span data-ttu-id="6ac48-163">Para obter informações sobre os dois modelos de hospedagem Blazor, *Blazor Server* e *Blazor Webassembly*, consulte <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="6ac48-163">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <!-- For a Blazor WebAssembly experience, select the **Blazor WebAssembly App** template. Select **Next**. -->

   <span data-ttu-id="6ac48-164">5 \.</span><span class="sxs-lookup"><span data-stu-id="6ac48-164">5\.</span></span> <span data-ttu-id="6ac48-165">Defina a **estrutura de destino** como **.NET Core 3,1** e selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="6ac48-165">Set the **Target Framework** to **.NET Core 3.1** and select **Next**.</span></span>

   <span data-ttu-id="6ac48-166">6 \.</span><span class="sxs-lookup"><span data-stu-id="6ac48-166">6\.</span></span> <span data-ttu-id="6ac48-167">No campo **nome do projeto** , nomeie o aplicativo `WebApplication1`.</span><span class="sxs-lookup"><span data-stu-id="6ac48-167">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="6ac48-168">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="6ac48-168">Select **Create**.</span></span>

   <span data-ttu-id="6ac48-169">7 \.</span><span class="sxs-lookup"><span data-stu-id="6ac48-169">7\.</span></span> <span data-ttu-id="6ac48-170">Selecione **executar** > **executar sem depuração** para executar o aplicativo *sem o depurador*.</span><span class="sxs-lookup"><span data-stu-id="6ac48-170">Select **Run** > **Run Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="6ac48-171">Execute o aplicativo com **Iniciar Depuração** para executar o aplicativo *com o depurador*.</span><span class="sxs-lookup"><span data-stu-id="6ac48-171">Run the app with **Start Debugging** to run the app *with the debugger*.</span></span>

       If a prompt appears to trust the development certificate, trust the certificate and continue.

   # <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="6ac48-172">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="6ac48-172">.NET Core CLI</span></span>](#tab/netcore-cli/)

   <span data-ttu-id="6ac48-173">Para obter uma experiência de Webassembly Blazor, execute os seguintes comandos em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="6ac48-173">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="6ac48-174">Para obter uma experiência de Blazor Server, execute os seguintes comandos em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="6ac48-174">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="6ac48-175">Para obter informações sobre os dois modelos de hospedagem Blazor, *Blazor Server* e *Blazor Webassembly*, consulte <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="6ac48-175">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="6ac48-176">Em um navegador, navegue até `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="6ac48-176">In a browser, navigate to `https://localhost:5001`.</span></span>

   ---

::: moniker-end

::: moniker range="< aspnetcore-3.1"

1. <span data-ttu-id="6ac48-177">Instale o [SDK do .NET Core 3,0](https://dotnet.microsoft.com/download/dotnet-core/3.0)mais recente.</span><span class="sxs-lookup"><span data-stu-id="6ac48-177">Install the latest [.NET Core 3.0 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0).</span></span>

1. <span data-ttu-id="6ac48-178">Opcionalmente, instale o modelo [WebassemblyBlazor](xref:blazor/hosting-models#blazor-webassembly) :</span><span class="sxs-lookup"><span data-stu-id="6ac48-178">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) template:</span></span>
   * <span data-ttu-id="6ac48-179">Instale o [SDK do .NET Core 3,1 ou posterior (versão prévia)](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="6ac48-179">Install the [.NET Core 3.1 or later (Preview) SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>
   * <span data-ttu-id="6ac48-180">Execute o comando a seguir em um shell de comando.</span><span class="sxs-lookup"><span data-stu-id="6ac48-180">Run the following command in a command shell.</span></span> <span data-ttu-id="6ac48-181">O [Microsoft.AspNetCore.Blazor.](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/)O pacote de modelos tem uma versão de visualização enquanto Blazor Webassembly está em visualização.</span><span class="sxs-lookup"><span data-stu-id="6ac48-181">The [Microsoft.AspNetCore.Blazor.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/) package has a preview version while Blazor WebAssembly is in preview.</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.1.0-preview4.19579.2
   ```

1. <span data-ttu-id="6ac48-182">Siga as orientações para sua escolha de ferramentas:</span><span class="sxs-lookup"><span data-stu-id="6ac48-182">Follow the guidance for your choice of tooling:</span></span>

   # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="6ac48-183">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6ac48-183">Visual Studio</span></span>](#tab/visual-studio)

   <span data-ttu-id="6ac48-184">1 \.</span><span class="sxs-lookup"><span data-stu-id="6ac48-184">1\.</span></span> <span data-ttu-id="6ac48-185">Instale o [Visual Studio](https://visualstudio.com/vs/) mais recente com a ASP.net e a carga de trabalho de **desenvolvimento Web** .</span><span class="sxs-lookup"><span data-stu-id="6ac48-185">Install the latest [Visual Studio](https://visualstudio.com/vs/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="6ac48-186">2 \.</span><span class="sxs-lookup"><span data-stu-id="6ac48-186">2\.</span></span> <span data-ttu-id="6ac48-187">Opcionalmente, instale o [Visual Studio 16,4 Preview 2 ou posterior](https://visualstudio.microsoft.com/vs/preview/) com o ASP.net e a carga de trabalho de **desenvolvimento Web** para o desenvolvimento de aplicativos Webassembly Blazor.</span><span class="sxs-lookup"><span data-stu-id="6ac48-187">Optionally install [Visual Studio 16.4 Preview 2 or later](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload for Blazor WebAssembly app development.</span></span>

   <span data-ttu-id="6ac48-188">3 \.</span><span class="sxs-lookup"><span data-stu-id="6ac48-188">3\.</span></span> <span data-ttu-id="6ac48-189">Crie um novo projeto.</span><span class="sxs-lookup"><span data-stu-id="6ac48-189">Create a new project.</span></span>

   <span data-ttu-id="6ac48-190">4 \.</span><span class="sxs-lookup"><span data-stu-id="6ac48-190">4\.</span></span> <span data-ttu-id="6ac48-191">Selecione **Blazor aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="6ac48-191">Select **Blazor App**.</span></span> <span data-ttu-id="6ac48-192">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="6ac48-192">Select **Next**.</span></span>

   <span data-ttu-id="6ac48-193">5 \.</span><span class="sxs-lookup"><span data-stu-id="6ac48-193">5\.</span></span> <span data-ttu-id="6ac48-194">Forneça um nome ao projeto no campo **Nome do projeto** ou aceite o nome do projeto padrão.</span><span class="sxs-lookup"><span data-stu-id="6ac48-194">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="6ac48-195">Confirme se a entrada de **local** está correta ou forneça um local para o projeto.</span><span class="sxs-lookup"><span data-stu-id="6ac48-195">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="6ac48-196">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="6ac48-196">Select **Create**.</span></span>

   <span data-ttu-id="6ac48-197">6 \.</span><span class="sxs-lookup"><span data-stu-id="6ac48-197">6\.</span></span> <span data-ttu-id="6ac48-198">Para obter uma experiência de Webassembly Blazor, escolha o modelo de **aplicativoBlazor Webassembly** .</span><span class="sxs-lookup"><span data-stu-id="6ac48-198">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="6ac48-199">Para obter uma experiência de Blazor Server, escolha o modelo de **aplicativoBlazor Server** .</span><span class="sxs-lookup"><span data-stu-id="6ac48-199">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="6ac48-200">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="6ac48-200">Select **Create**.</span></span> <span data-ttu-id="6ac48-201">Para obter informações sobre os dois modelos de hospedagem Blazor, *Blazor Server* e *Blazor Webassembly*, consulte <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="6ac48-201">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="6ac48-202">7 \.</span><span class="sxs-lookup"><span data-stu-id="6ac48-202">7\.</span></span> <span data-ttu-id="6ac48-203">Pressione **F5** para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6ac48-203">Press **F5** to run the app.</span></span>

   > [!NOTE]
   > <span data-ttu-id="6ac48-204">Se você instalou a extensão do Blazor Visual Studio para uma versão prévia anterior do ASP.NET Core Blazor (visualização 6 ou anterior), você pode desinstalar a extensão.</span><span class="sxs-lookup"><span data-stu-id="6ac48-204">If you installed the Blazor Visual Studio extension for a prior preview release of ASP.NET Core Blazor (Preview 6 or earlier), you can uninstall the extension.</span></span> <span data-ttu-id="6ac48-205">Instalar os modelos de Blazor em um shell de comando agora é suficiente para retonar os modelos no Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="6ac48-205">Installing the Blazor templates in a command shell is now sufficient to surface the templates in Visual Studio.</span></span>

   # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="6ac48-206">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6ac48-206">Visual Studio Code</span></span>](#tab/visual-studio-code)

   <span data-ttu-id="6ac48-207">1 \.</span><span class="sxs-lookup"><span data-stu-id="6ac48-207">1\.</span></span> <span data-ttu-id="6ac48-208">Instalar o [Visual Studio Code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="6ac48-208">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

   <span data-ttu-id="6ac48-209">2 \.</span><span class="sxs-lookup"><span data-stu-id="6ac48-209">2\.</span></span> <span data-ttu-id="6ac48-210">Instale o mais recente [ C# para a extensão de Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span><span class="sxs-lookup"><span data-stu-id="6ac48-210">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span></span>

   <span data-ttu-id="6ac48-211">3 \.</span><span class="sxs-lookup"><span data-stu-id="6ac48-211">3\.</span></span> <span data-ttu-id="6ac48-212">Para obter uma experiência de Webassembly Blazor, execute o seguinte comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="6ac48-212">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorwasm -o WebApplication1
      ```

      <span data-ttu-id="6ac48-213">Para obter uma experiência de Blazor Server, execute o seguinte comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="6ac48-213">For a Blazor Server experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorserver -o WebApplication1
      ```

      <span data-ttu-id="6ac48-214">Para obter informações sobre os dois modelos de hospedagem Blazor, *Blazor Server* e *Blazor Webassembly*, consulte <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="6ac48-214">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="6ac48-215">4 \.</span><span class="sxs-lookup"><span data-stu-id="6ac48-215">4\.</span></span> <span data-ttu-id="6ac48-216">Abra a pasta *WebApplication1* em Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="6ac48-216">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

   <span data-ttu-id="6ac48-217">5 \.</span><span class="sxs-lookup"><span data-stu-id="6ac48-217">5\.</span></span> <span data-ttu-id="6ac48-218">Para um projeto do Blazor Server, o IDE solicita que você adicione ativos para compilar e depurar o projeto.</span><span class="sxs-lookup"><span data-stu-id="6ac48-218">For a Blazor Server project, the IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="6ac48-219">Selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="6ac48-219">Select **Yes**.</span></span>

   <span data-ttu-id="6ac48-220">6 \.</span><span class="sxs-lookup"><span data-stu-id="6ac48-220">6\.</span></span> <span data-ttu-id="6ac48-221">Se estiver usando um aplicativo do Blazor Server, execute o aplicativo usando o depurador de Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="6ac48-221">If using a Blazor Server app, run the app using the Visual Studio Code debugger.</span></span> <span data-ttu-id="6ac48-222">Se estiver usando um aplicativo Webassembly Blazor, execute `dotnet run` na pasta do projeto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6ac48-222">If using a Blazor WebAssembly app, execute `dotnet run` from the app's project folder.</span></span>

   <span data-ttu-id="6ac48-223">7 \.</span><span class="sxs-lookup"><span data-stu-id="6ac48-223">7\.</span></span> <span data-ttu-id="6ac48-224">Em um navegador, navegue até `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="6ac48-224">In a browser, navigate to `https://localhost:5001`.</span></span>

   # <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="6ac48-225">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6ac48-225">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

   <span data-ttu-id="6ac48-226">1 \.</span><span class="sxs-lookup"><span data-stu-id="6ac48-226">1\.</span></span> <span data-ttu-id="6ac48-227">Instale o [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="6ac48-227">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span> <span data-ttu-id="6ac48-228">Alterne o [canal de atualização para visualização](/visualstudio/mac/install-preview).</span><span class="sxs-lookup"><span data-stu-id="6ac48-228">Switch the [Update channel to Preview](/visualstudio/mac/install-preview).</span></span>

   <span data-ttu-id="6ac48-229">2 \.</span><span class="sxs-lookup"><span data-stu-id="6ac48-229">2\.</span></span> <span data-ttu-id="6ac48-230">Selecione **arquivo** > **nova solução** ou crie um **novo projeto**.</span><span class="sxs-lookup"><span data-stu-id="6ac48-230">Select **File** > **New Solution** or create a **New Project**.</span></span>

   <span data-ttu-id="6ac48-231">3 \.</span><span class="sxs-lookup"><span data-stu-id="6ac48-231">3\.</span></span> <span data-ttu-id="6ac48-232">Na barra lateral, selecione **.NET Core** > **aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="6ac48-232">In the sidebar, select **.NET Core** > **App**.</span></span>

   <span data-ttu-id="6ac48-233">4 \.</span><span class="sxs-lookup"><span data-stu-id="6ac48-233">4\.</span></span> <span data-ttu-id="6ac48-234">Selecione o modelo de **aplicativo doBlazor Server** .</span><span class="sxs-lookup"><span data-stu-id="6ac48-234">Select the **Blazor Server App** template.</span></span> <span data-ttu-id="6ac48-235">Somente o modelo do Blazor Server está disponível no Visual Studio para Mac no momento.</span><span class="sxs-lookup"><span data-stu-id="6ac48-235">Only the Blazor Server template is available in Visual Studio for Mac at this time.</span></span> <span data-ttu-id="6ac48-236">Para obter uma experiência de Webassembly Blazor, siga as instruções na guia **CLI do .NET Core** . Depois de selecionar o modelo do Blazor Server, selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="6ac48-236">For a Blazor WebAssembly experience, follow the instructions on the **.NET Core CLI** tab. After selecting the Blazor Server template, select **Next**.</span></span> <span data-ttu-id="6ac48-237">Para obter informações sobre os dois modelos de hospedagem Blazor, *Blazor Server* e *Blazor Webassembly*, consulte <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="6ac48-237">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <!-- For a Blazor WebAssembly experience, select the **Blazor WebAssembly App** template. Select **Next**. -->

   <span data-ttu-id="6ac48-238">5 \.</span><span class="sxs-lookup"><span data-stu-id="6ac48-238">5\.</span></span> <span data-ttu-id="6ac48-239">Defina a **estrutura de destino** como **.NET Core 3,0** e selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="6ac48-239">Set the **Target Framework** to **.NET Core 3.0** and select **Next**.</span></span>

   <span data-ttu-id="6ac48-240">6 \.</span><span class="sxs-lookup"><span data-stu-id="6ac48-240">6\.</span></span> <span data-ttu-id="6ac48-241">No campo **nome do projeto** , nomeie o aplicativo `WebApplication1`.</span><span class="sxs-lookup"><span data-stu-id="6ac48-241">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="6ac48-242">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="6ac48-242">Select **Create**.</span></span>

   <span data-ttu-id="6ac48-243">7 \.</span><span class="sxs-lookup"><span data-stu-id="6ac48-243">7\.</span></span> <span data-ttu-id="6ac48-244">Selecione **executar** > **executar sem depuração** para executar o aplicativo *sem o depurador*.</span><span class="sxs-lookup"><span data-stu-id="6ac48-244">Select **Run** > **Run Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="6ac48-245">Execute o aplicativo com **Iniciar Depuração** para executar o aplicativo *com o depurador*.</span><span class="sxs-lookup"><span data-stu-id="6ac48-245">Run the app with **Start Debugging** to run the app *with the debugger*.</span></span>

       If a prompt appears to trust the development certificate, trust the certificate and continue.

   # <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="6ac48-246">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="6ac48-246">.NET Core CLI</span></span>](#tab/netcore-cli/)

   <span data-ttu-id="6ac48-247">Para obter uma experiência de Webassembly Blazor, execute os seguintes comandos em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="6ac48-247">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="6ac48-248">Para obter uma experiência de Blazor Server, execute os seguintes comandos em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="6ac48-248">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="6ac48-249">Para obter informações sobre os dois modelos de hospedagem Blazor, *Blazor Server* e *Blazor Webassembly*, consulte <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="6ac48-249">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="6ac48-250">Em um navegador, navegue até `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="6ac48-250">In a browser, navigate to `https://localhost:5001`.</span></span>

   ---

::: moniker-end

<span data-ttu-id="6ac48-251">Várias páginas estão disponíveis em guias na barra lateral:</span><span class="sxs-lookup"><span data-stu-id="6ac48-251">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="6ac48-252">Página inicial do</span><span class="sxs-lookup"><span data-stu-id="6ac48-252">Home</span></span>
* <span data-ttu-id="6ac48-253">Contador</span><span class="sxs-lookup"><span data-stu-id="6ac48-253">Counter</span></span>
* <span data-ttu-id="6ac48-254">Buscar dados</span><span class="sxs-lookup"><span data-stu-id="6ac48-254">Fetch data</span></span>

<span data-ttu-id="6ac48-255">Na página Contador, selecione o botão **Clique aqui** para incrementar o contador sem uma atualização de página.</span><span class="sxs-lookup"><span data-stu-id="6ac48-255">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="6ac48-256">Incrementar um contador em uma página da Web normalmente requer a escrita de JavaScript, mas com Blazor C#você pode usar.</span><span class="sxs-lookup"><span data-stu-id="6ac48-256">Incrementing a counter in a webpage normally requires writing JavaScript, but with Blazor you can use C#.</span></span>

<span data-ttu-id="6ac48-257">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="6ac48-257">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="6ac48-258">Uma solicitação de `/counter` no navegador, conforme especificado pela diretiva `@page` na parte superior, faz com que o componente `Counter` processe seu conteúdo.</span><span class="sxs-lookup"><span data-stu-id="6ac48-258">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="6ac48-259">Os componentes são renderizados em uma representação na memória da árvore de renderização que pode ser usada para atualizar a interface do usuário de maneira flexível e eficiente.</span><span class="sxs-lookup"><span data-stu-id="6ac48-259">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="6ac48-260">Sempre que o botão **clicar em mim** estiver selecionado:</span><span class="sxs-lookup"><span data-stu-id="6ac48-260">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="6ac48-261">O evento `onclick` é acionado.</span><span class="sxs-lookup"><span data-stu-id="6ac48-261">The `onclick` event is fired.</span></span>
* <span data-ttu-id="6ac48-262">O método `IncrementCount` é chamado.</span><span class="sxs-lookup"><span data-stu-id="6ac48-262">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="6ac48-263">O `currentCount` é incrementado.</span><span class="sxs-lookup"><span data-stu-id="6ac48-263">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="6ac48-264">O componente é renderizado novamente.</span><span class="sxs-lookup"><span data-stu-id="6ac48-264">The component is rendered again.</span></span>

<span data-ttu-id="6ac48-265">O tempo de execução compara o novo conteúdo com o conteúdo anterior e aplica apenas o conteúdo alterado para o Modelo de Objeto do Documento (DOM).</span><span class="sxs-lookup"><span data-stu-id="6ac48-265">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="6ac48-266">Adicione um componente a outro componente usando a sintaxe HTML.</span><span class="sxs-lookup"><span data-stu-id="6ac48-266">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="6ac48-267">Por exemplo, adicione o componente `Counter` à Home Page do aplicativo adicionando um elemento `<Counter />` ao componente `Index`.</span><span class="sxs-lookup"><span data-stu-id="6ac48-267">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="6ac48-268">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="6ac48-268">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="6ac48-269">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6ac48-269">Run the app.</span></span> <span data-ttu-id="6ac48-270">A Home Page tem seu próprio contador fornecido pelo componente `Counter`.</span><span class="sxs-lookup"><span data-stu-id="6ac48-270">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="6ac48-271">Os parâmetros de componente são especificados usando atributos ou [conteúdo filho](xref:blazor/components#child-content), que permitem definir propriedades no componente filho.</span><span class="sxs-lookup"><span data-stu-id="6ac48-271">Component parameters are specified using attributes or [child content](xref:blazor/components#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="6ac48-272">Para adicionar um parâmetro ao componente `Counter`, atualize o bloco de `@code` do componente:</span><span class="sxs-lookup"><span data-stu-id="6ac48-272">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="6ac48-273">Adicione uma propriedade pública para `IncrementAmount` com um atributo `[Parameter]`.</span><span class="sxs-lookup"><span data-stu-id="6ac48-273">Add a public property for `IncrementAmount` with a `[Parameter]` attribute.</span></span>
* <span data-ttu-id="6ac48-274">Altere o método `IncrementCount` para usar o `IncrementAmount` ao aumentar o valor de `currentCount`.</span><span class="sxs-lookup"><span data-stu-id="6ac48-274">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="6ac48-275">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="6ac48-275">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="6ac48-276">Especifique o `IncrementAmount` no elemento `<Counter>` do componente de `Index` usando um atributo.</span><span class="sxs-lookup"><span data-stu-id="6ac48-276">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="6ac48-277">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="6ac48-277">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="6ac48-278">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6ac48-278">Run the app.</span></span> <span data-ttu-id="6ac48-279">O componente `Index` tem seu próprio contador que é incrementado em dez cada vez que o botão **Click me** está selecionado.</span><span class="sxs-lookup"><span data-stu-id="6ac48-279">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="6ac48-280">O componente `Counter` (*Counter. Razor*) em `/counter` continua a incrementar um.</span><span class="sxs-lookup"><span data-stu-id="6ac48-280">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="6ac48-281">{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}</span><span class="sxs-lookup"><span data-stu-id="6ac48-281">Next steps</span></span>

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="6ac48-282">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="6ac48-282">Additional resources</span></span>

* <xref:blazor/templates>
* <xref:signalr/introduction>
