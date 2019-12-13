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
ms.openlocfilehash: e368ecaf931d392de7e52ec2d5a2dfd171c2c86f
ms.sourcegitcommit: 851b921080fe8d719f54871770ccf6f78052584e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2019
ms.locfileid: "74943753"
---
# <a name="get-started-with-aspnet-core-opno-locblazor"></a><span data-ttu-id="3f3ef-103">Introdução ao ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="3f3ef-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="3f3ef-104">Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="3f3ef-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="3f3ef-105">Introdução ao Blazor:</span><span class="sxs-lookup"><span data-stu-id="3f3ef-105">Get started with Blazor:</span></span>

::: moniker range=">= aspnetcore-3.1"

1. <span data-ttu-id="3f3ef-106">Instale o [SDK do .NET Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="3f3ef-106">Install the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="3f3ef-107">Opcionalmente, instale o modelo [WebassemblyBlazor](xref:blazor/hosting-models#blazor-webassembly) :</span><span class="sxs-lookup"><span data-stu-id="3f3ef-107">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) template:</span></span>
   * <span data-ttu-id="3f3ef-108">Instale o [SDK do .NET Core 3,1 ou posterior (versão prévia)](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="3f3ef-108">Install the [.NET Core 3.1 or later (Preview) SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>
   * <span data-ttu-id="3f3ef-109">Execute o comando a seguir em um shell de comando.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-109">Run the following command in a command shell.</span></span> <span data-ttu-id="3f3ef-110">O [Microsoft. AspNetCore.Blazor. ](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/)O pacote de modelos tem uma versão de visualização enquanto Blazor Webassembly está em visualização.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-110">The [Microsoft.AspNetCore.Blazor.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/) package has a preview version while Blazor WebAssembly is in preview.</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.1.0-preview4.19579.2
   ```

1. <span data-ttu-id="3f3ef-111">Siga as orientações para sua escolha de ferramentas:</span><span class="sxs-lookup"><span data-stu-id="3f3ef-111">Follow the guidance for your choice of tooling:</span></span>

   # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3f3ef-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3f3ef-112">Visual Studio</span></span>](#tab/visual-studio)

   <span data-ttu-id="3f3ef-113">1 \.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-113">1\.</span></span> <span data-ttu-id="3f3ef-114">Instale o [Visual Studio 16,4 ou posterior](https://visualstudio.microsoft.com/vs/preview/) com a carga de trabalho de **desenvolvimento da web e do ASP.net** .</span><span class="sxs-lookup"><span data-stu-id="3f3ef-114">Install [Visual Studio 16.4 or later](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="3f3ef-115">2 \.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-115">2\.</span></span> <span data-ttu-id="3f3ef-116">Crie um novo projeto.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-116">Create a new project.</span></span>

   <span data-ttu-id="3f3ef-117">3 \.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-117">3\.</span></span> <span data-ttu-id="3f3ef-118">Selecione **Blazor aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-118">Select **Blazor App**.</span></span> <span data-ttu-id="3f3ef-119">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-119">Select **Next**.</span></span>

   <span data-ttu-id="3f3ef-120">4 \.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-120">4\.</span></span> <span data-ttu-id="3f3ef-121">Forneça um nome ao projeto no campo **Nome do projeto** ou aceite o nome do projeto padrão.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-121">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="3f3ef-122">Confirme se a entrada de **local** está correta ou forneça um local para o projeto.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-122">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="3f3ef-123">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-123">Select **Create**.</span></span>

   <span data-ttu-id="3f3ef-124">5 \.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-124">5\.</span></span> <span data-ttu-id="3f3ef-125">Para obter uma experiência de Webassembly Blazor, escolha o modelo de **aplicativoBlazor Webassembly** .</span><span class="sxs-lookup"><span data-stu-id="3f3ef-125">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="3f3ef-126">Para obter uma experiência de Blazor Server, escolha o modelo de **aplicativoBlazor Server** .</span><span class="sxs-lookup"><span data-stu-id="3f3ef-126">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="3f3ef-127">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-127">Select **Create**.</span></span> <span data-ttu-id="3f3ef-128">Para obter informações sobre os dois modelos de hospedagem Blazor, *Blazor Server* e *Blazor Webassembly*, consulte <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-128">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="3f3ef-129">6 \.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-129">6\.</span></span> <span data-ttu-id="3f3ef-130">Pressione **Ctrl**+**F5** para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-130">Press **Ctrl**+**F5** to run the app.</span></span>

   > [!NOTE]
   > <span data-ttu-id="3f3ef-131">Se você instalou a extensão do Blazor Visual Studio para uma versão prévia anterior do ASP.NET Core Blazor (visualização 6 ou anterior), você pode desinstalar a extensão.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-131">If you installed the Blazor Visual Studio extension for a prior preview release of ASP.NET Core Blazor (Preview 6 or earlier), you can uninstall the extension.</span></span> <span data-ttu-id="3f3ef-132">Instalar os modelos de Blazor em um shell de comando agora é suficiente para retonar os modelos no Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-132">Installing the Blazor templates in a command shell is now sufficient to surface the templates in Visual Studio.</span></span>

   # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="3f3ef-133">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3f3ef-133">Visual Studio Code</span></span>](#tab/visual-studio-code)

   <span data-ttu-id="3f3ef-134">1 \.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-134">1\.</span></span> <span data-ttu-id="3f3ef-135">Instalar o [Visual Studio Code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="3f3ef-135">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

   <span data-ttu-id="3f3ef-136">2 \.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-136">2\.</span></span> <span data-ttu-id="3f3ef-137">Instale o mais recente [ C# para a extensão de Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span><span class="sxs-lookup"><span data-stu-id="3f3ef-137">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span></span>

   <span data-ttu-id="3f3ef-138">3 \.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-138">3\.</span></span> <span data-ttu-id="3f3ef-139">Para obter uma experiência de Webassembly Blazor, execute o seguinte comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="3f3ef-139">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorwasm -o WebApplication1
      ```

      <span data-ttu-id="3f3ef-140">Para obter uma experiência de Blazor Server, execute o seguinte comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="3f3ef-140">For a Blazor Server experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorserver -o WebApplication1
      ```

      <span data-ttu-id="3f3ef-141">Para obter informações sobre os dois modelos de hospedagem Blazor, *Blazor Server* e *Blazor Webassembly*, consulte <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-141">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="3f3ef-142">4 \.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-142">4\.</span></span> <span data-ttu-id="3f3ef-143">Abra a pasta *WebApplication1* em Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-143">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

   <span data-ttu-id="3f3ef-144">5 \.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-144">5\.</span></span> <span data-ttu-id="3f3ef-145">Para um projeto do Blazor Server, o IDE solicita que você adicione ativos para compilar e depurar o projeto.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-145">For a Blazor Server project, the IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="3f3ef-146">Selecione **Sim** na barra superior.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-146">Select **Yes**.</span></span>

   <span data-ttu-id="3f3ef-147">6 \.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-147">6\.</span></span> <span data-ttu-id="3f3ef-148">Se estiver usando um aplicativo do Blazor Server, execute o aplicativo usando o depurador de Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-148">If using a Blazor Server app, run the app using the Visual Studio Code debugger.</span></span> <span data-ttu-id="3f3ef-149">Se estiver usando um aplicativo Webassembly Blazor, execute `dotnet run` na pasta do projeto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-149">If using a Blazor WebAssembly app, execute `dotnet run` from the app's project folder.</span></span>

   <span data-ttu-id="3f3ef-150">7 \.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-150">7\.</span></span> <span data-ttu-id="3f3ef-151">Em um navegador, navegue até `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-151">In a browser, navigate to `https://localhost:5001`.</span></span>

   # <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="3f3ef-152">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="3f3ef-152">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

   <span data-ttu-id="3f3ef-153">1 \.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-153">1\.</span></span> <span data-ttu-id="3f3ef-154">Instale o [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="3f3ef-154">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span> <span data-ttu-id="3f3ef-155">Alterne o [canal de atualização para visualização](/visualstudio/mac/install-preview).</span><span class="sxs-lookup"><span data-stu-id="3f3ef-155">Switch the [Update channel to Preview](/visualstudio/mac/install-preview).</span></span>

   <span data-ttu-id="3f3ef-156">2 \.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-156">2\.</span></span> <span data-ttu-id="3f3ef-157">Selecione **arquivo** > **nova solução** ou crie um **novo projeto**.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-157">Select **File** > **New Solution** or create a **New Project**.</span></span>

   <span data-ttu-id="3f3ef-158">3 \.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-158">3\.</span></span> <span data-ttu-id="3f3ef-159">Na barra lateral, selecione **.NET Core** > **aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-159">In the sidebar, select **.NET Core** > **App**.</span></span>

   <span data-ttu-id="3f3ef-160">4 \.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-160">4\.</span></span> <span data-ttu-id="3f3ef-161">Selecione o modelo de **aplicativo doBlazor Server** .</span><span class="sxs-lookup"><span data-stu-id="3f3ef-161">Select the **Blazor Server App** template.</span></span> <span data-ttu-id="3f3ef-162">Somente o modelo do Blazor Server está disponível no Visual Studio para Mac no momento.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-162">Only the Blazor Server template is available in Visual Studio for Mac at this time.</span></span> <span data-ttu-id="3f3ef-163">Para obter uma experiência de Webassembly Blazor, siga as instruções na guia **CLI do .NET Core** . Depois de selecionar o modelo do Blazor Server, selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-163">For a Blazor WebAssembly experience, follow the instructions on the **.NET Core CLI** tab. After selecting the Blazor Server template, select **Next**.</span></span> <span data-ttu-id="3f3ef-164">Para obter informações sobre os dois modelos de hospedagem Blazor, *Blazor Server* e *Blazor Webassembly*, consulte <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-164">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <!-- For a Blazor WebAssembly experience, select the **Blazor WebAssembly App** template. Select **Next**. -->

   <span data-ttu-id="3f3ef-165">5 \.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-165">5\.</span></span> <span data-ttu-id="3f3ef-166">Defina a **estrutura de destino** como **.NET Core 3,1** e selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-166">Set the **Target Framework** to **.NET Core 3.1** and select **Next**.</span></span>

   <span data-ttu-id="3f3ef-167">6 \.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-167">6\.</span></span> <span data-ttu-id="3f3ef-168">No campo **nome do projeto** , nomeie o aplicativo `WebApplication1`.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-168">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="3f3ef-169">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-169">Select **Create**.</span></span>

   <span data-ttu-id="3f3ef-170">7 \.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-170">7\.</span></span> <span data-ttu-id="3f3ef-171">Selecione **executar** > **executar sem depuração** para executar o aplicativo *sem o depurador*.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-171">Select **Run** > **Run Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="3f3ef-172">Execute o aplicativo com **Iniciar Depuração** para executar o aplicativo *com o depurador*.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-172">Run the app with **Start Debugging** to run the app *with the debugger*.</span></span>

       If a prompt appears to trust the development certificate, trust the certificate and continue.

   # <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="3f3ef-173">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="3f3ef-173">.NET Core CLI</span></span>](#tab/netcore-cli/)

   <span data-ttu-id="3f3ef-174">Para obter uma experiência de Webassembly Blazor, execute os seguintes comandos em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="3f3ef-174">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="3f3ef-175">Para obter uma experiência de Blazor Server, execute os seguintes comandos em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="3f3ef-175">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="3f3ef-176">Para obter informações sobre os dois modelos de hospedagem Blazor, *Blazor Server* e *Blazor Webassembly*, consulte <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-176">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="3f3ef-177">Em um navegador, navegue até `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-177">In a browser, navigate to `https://localhost:5001`.</span></span>

   ---

::: moniker-end

::: moniker range="< aspnetcore-3.1"

1. <span data-ttu-id="3f3ef-178">Instale o [SDK do .NET Core 3,0](https://dotnet.microsoft.com/download/dotnet-core/3.0)mais recente.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-178">Install the latest [.NET Core 3.0 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0).</span></span>

1. <span data-ttu-id="3f3ef-179">Opcionalmente, instale o modelo [WebassemblyBlazor](xref:blazor/hosting-models#blazor-webassembly) :</span><span class="sxs-lookup"><span data-stu-id="3f3ef-179">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) template:</span></span>
   * <span data-ttu-id="3f3ef-180">Instale o [SDK do .NET Core 3,1 ou posterior (versão prévia)](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="3f3ef-180">Install the [.NET Core 3.1 or later (Preview) SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>
   * <span data-ttu-id="3f3ef-181">Execute o comando a seguir em um shell de comando.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-181">Run the following command in a command shell.</span></span> <span data-ttu-id="3f3ef-182">O [Microsoft. AspNetCore.Blazor. ](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/)O pacote de modelos tem uma versão de visualização enquanto Blazor Webassembly está em visualização.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-182">The [Microsoft.AspNetCore.Blazor.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/) package has a preview version while Blazor WebAssembly is in preview.</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.1.0-preview4.19579.2
   ```

1. <span data-ttu-id="3f3ef-183">Siga as orientações para sua escolha de ferramentas:</span><span class="sxs-lookup"><span data-stu-id="3f3ef-183">Follow the guidance for your choice of tooling:</span></span>

   # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3f3ef-184">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3f3ef-184">Visual Studio</span></span>](#tab/visual-studio)

   <span data-ttu-id="3f3ef-185">1 \.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-185">1\.</span></span> <span data-ttu-id="3f3ef-186">Instale o [Visual Studio](https://visualstudio.com/vs/) mais recente com a ASP.net e a carga de trabalho de **desenvolvimento Web** .</span><span class="sxs-lookup"><span data-stu-id="3f3ef-186">Install the latest [Visual Studio](https://visualstudio.com/vs/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="3f3ef-187">2 \.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-187">2\.</span></span> <span data-ttu-id="3f3ef-188">Opcionalmente, instale o [Visual Studio 16,4 Preview 2 ou posterior](https://visualstudio.microsoft.com/vs/preview/) com o ASP.net e a carga de trabalho de **desenvolvimento Web** para o desenvolvimento de aplicativos Webassembly Blazor.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-188">Optionally install [Visual Studio 16.4 Preview 2 or later](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload for Blazor WebAssembly app development.</span></span>

   <span data-ttu-id="3f3ef-189">3 \.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-189">3\.</span></span> <span data-ttu-id="3f3ef-190">Crie um novo projeto.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-190">Create a new project.</span></span>

   <span data-ttu-id="3f3ef-191">4 \.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-191">4\.</span></span> <span data-ttu-id="3f3ef-192">Selecione **Blazor aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-192">Select **Blazor App**.</span></span> <span data-ttu-id="3f3ef-193">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-193">Select **Next**.</span></span>

   <span data-ttu-id="3f3ef-194">5 \.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-194">5\.</span></span> <span data-ttu-id="3f3ef-195">Forneça um nome ao projeto no campo **Nome do projeto** ou aceite o nome do projeto padrão.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-195">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="3f3ef-196">Confirme se a entrada de **local** está correta ou forneça um local para o projeto.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-196">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="3f3ef-197">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-197">Select **Create**.</span></span>

   <span data-ttu-id="3f3ef-198">6 \.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-198">6\.</span></span> <span data-ttu-id="3f3ef-199">Para obter uma experiência de Webassembly Blazor, escolha o modelo de **aplicativoBlazor Webassembly** .</span><span class="sxs-lookup"><span data-stu-id="3f3ef-199">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="3f3ef-200">Para obter uma experiência de Blazor Server, escolha o modelo de **aplicativoBlazor Server** .</span><span class="sxs-lookup"><span data-stu-id="3f3ef-200">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="3f3ef-201">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-201">Select **Create**.</span></span> <span data-ttu-id="3f3ef-202">Para obter informações sobre os dois modelos de hospedagem Blazor, *Blazor Server* e *Blazor Webassembly*, consulte <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-202">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="3f3ef-203">7 \.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-203">7\.</span></span> <span data-ttu-id="3f3ef-204">Pressione **F5** para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-204">Press **F5** to run the app.</span></span>

   > [!NOTE]
   > <span data-ttu-id="3f3ef-205">Se você instalou a extensão do Blazor Visual Studio para uma versão prévia anterior do ASP.NET Core Blazor (visualização 6 ou anterior), você pode desinstalar a extensão.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-205">If you installed the Blazor Visual Studio extension for a prior preview release of ASP.NET Core Blazor (Preview 6 or earlier), you can uninstall the extension.</span></span> <span data-ttu-id="3f3ef-206">Instalar os modelos de Blazor em um shell de comando agora é suficiente para retonar os modelos no Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-206">Installing the Blazor templates in a command shell is now sufficient to surface the templates in Visual Studio.</span></span>

   # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="3f3ef-207">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3f3ef-207">Visual Studio Code</span></span>](#tab/visual-studio-code)

   <span data-ttu-id="3f3ef-208">1 \.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-208">1\.</span></span> <span data-ttu-id="3f3ef-209">Instalar o [Visual Studio Code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="3f3ef-209">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

   <span data-ttu-id="3f3ef-210">2 \.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-210">2\.</span></span> <span data-ttu-id="3f3ef-211">Instale o mais recente [ C# para a extensão de Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span><span class="sxs-lookup"><span data-stu-id="3f3ef-211">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span></span>

   <span data-ttu-id="3f3ef-212">3 \.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-212">3\.</span></span> <span data-ttu-id="3f3ef-213">Para obter uma experiência de Webassembly Blazor, execute o seguinte comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="3f3ef-213">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorwasm -o WebApplication1
      ```

      <span data-ttu-id="3f3ef-214">Para obter uma experiência de Blazor Server, execute o seguinte comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="3f3ef-214">For a Blazor Server experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorserver -o WebApplication1
      ```

      <span data-ttu-id="3f3ef-215">Para obter informações sobre os dois modelos de hospedagem Blazor, *Blazor Server* e *Blazor Webassembly*, consulte <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-215">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="3f3ef-216">4 \.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-216">4\.</span></span> <span data-ttu-id="3f3ef-217">Abra a pasta *WebApplication1* em Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-217">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

   <span data-ttu-id="3f3ef-218">5 \.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-218">5\.</span></span> <span data-ttu-id="3f3ef-219">Para um projeto do Blazor Server, o IDE solicita que você adicione ativos para compilar e depurar o projeto.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-219">For a Blazor Server project, the IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="3f3ef-220">Selecione **Sim** na barra superior.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-220">Select **Yes**.</span></span>

   <span data-ttu-id="3f3ef-221">6 \.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-221">6\.</span></span> <span data-ttu-id="3f3ef-222">Se estiver usando um aplicativo do Blazor Server, execute o aplicativo usando o depurador de Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-222">If using a Blazor Server app, run the app using the Visual Studio Code debugger.</span></span> <span data-ttu-id="3f3ef-223">Se estiver usando um aplicativo Webassembly Blazor, execute `dotnet run` na pasta do projeto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-223">If using a Blazor WebAssembly app, execute `dotnet run` from the app's project folder.</span></span>

   <span data-ttu-id="3f3ef-224">7 \.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-224">7\.</span></span> <span data-ttu-id="3f3ef-225">Em um navegador, navegue até `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-225">In a browser, navigate to `https://localhost:5001`.</span></span>

   # <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="3f3ef-226">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="3f3ef-226">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

   <span data-ttu-id="3f3ef-227">1 \.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-227">1\.</span></span> <span data-ttu-id="3f3ef-228">Instale o [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="3f3ef-228">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span> <span data-ttu-id="3f3ef-229">Alterne o [canal de atualização para visualização](/visualstudio/mac/install-preview).</span><span class="sxs-lookup"><span data-stu-id="3f3ef-229">Switch the [Update channel to Preview](/visualstudio/mac/install-preview).</span></span>

   <span data-ttu-id="3f3ef-230">2 \.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-230">2\.</span></span> <span data-ttu-id="3f3ef-231">Selecione **arquivo** > **nova solução** ou crie um **novo projeto**.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-231">Select **File** > **New Solution** or create a **New Project**.</span></span>

   <span data-ttu-id="3f3ef-232">3 \.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-232">3\.</span></span> <span data-ttu-id="3f3ef-233">Na barra lateral, selecione **.NET Core** > **aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-233">In the sidebar, select **.NET Core** > **App**.</span></span>

   <span data-ttu-id="3f3ef-234">4 \.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-234">4\.</span></span> <span data-ttu-id="3f3ef-235">Selecione o modelo de **aplicativo doBlazor Server** .</span><span class="sxs-lookup"><span data-stu-id="3f3ef-235">Select the **Blazor Server App** template.</span></span> <span data-ttu-id="3f3ef-236">Somente o modelo do Blazor Server está disponível no Visual Studio para Mac no momento.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-236">Only the Blazor Server template is available in Visual Studio for Mac at this time.</span></span> <span data-ttu-id="3f3ef-237">Para obter uma experiência de Webassembly Blazor, siga as instruções na guia **CLI do .NET Core** . Depois de selecionar o modelo do Blazor Server, selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-237">For a Blazor WebAssembly experience, follow the instructions on the **.NET Core CLI** tab. After selecting the Blazor Server template, select **Next**.</span></span> <span data-ttu-id="3f3ef-238">Para obter informações sobre os dois modelos de hospedagem Blazor, *Blazor Server* e *Blazor Webassembly*, consulte <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-238">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <!-- For a Blazor WebAssembly experience, select the **Blazor WebAssembly App** template. Select **Next**. -->

   <span data-ttu-id="3f3ef-239">5 \.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-239">5\.</span></span> <span data-ttu-id="3f3ef-240">Defina a **estrutura de destino** como **.NET Core 3,0** e selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-240">Set the **Target Framework** to **.NET Core 3.0** and select **Next**.</span></span>

   <span data-ttu-id="3f3ef-241">6 \.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-241">6\.</span></span> <span data-ttu-id="3f3ef-242">No campo **nome do projeto** , nomeie o aplicativo `WebApplication1`.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-242">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="3f3ef-243">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-243">Select **Create**.</span></span>

   <span data-ttu-id="3f3ef-244">7 \.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-244">7\.</span></span> <span data-ttu-id="3f3ef-245">Selecione **executar** > **executar sem depuração** para executar o aplicativo *sem o depurador*.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-245">Select **Run** > **Run Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="3f3ef-246">Execute o aplicativo com **Iniciar Depuração** para executar o aplicativo *com o depurador*.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-246">Run the app with **Start Debugging** to run the app *with the debugger*.</span></span>

       If a prompt appears to trust the development certificate, trust the certificate and continue.

   # <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="3f3ef-247">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="3f3ef-247">.NET Core CLI</span></span>](#tab/netcore-cli/)

   <span data-ttu-id="3f3ef-248">Para obter uma experiência de Webassembly Blazor, execute os seguintes comandos em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="3f3ef-248">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="3f3ef-249">Para obter uma experiência de Blazor Server, execute os seguintes comandos em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="3f3ef-249">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="3f3ef-250">Para obter informações sobre os dois modelos de hospedagem Blazor, *Blazor Server* e *Blazor Webassembly*, consulte <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-250">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="3f3ef-251">Em um navegador, navegue até `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-251">In a browser, navigate to `https://localhost:5001`.</span></span>

   ---

::: moniker-end

<span data-ttu-id="3f3ef-252">Várias páginas estão disponíveis em guias na barra lateral:</span><span class="sxs-lookup"><span data-stu-id="3f3ef-252">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="3f3ef-253">Home</span><span class="sxs-lookup"><span data-stu-id="3f3ef-253">Home</span></span>
* <span data-ttu-id="3f3ef-254">Contador</span><span class="sxs-lookup"><span data-stu-id="3f3ef-254">Counter</span></span>
* <span data-ttu-id="3f3ef-255">Buscar dados</span><span class="sxs-lookup"><span data-stu-id="3f3ef-255">Fetch data</span></span>

<span data-ttu-id="3f3ef-256">Na página Contador, selecione o botão **Clique aqui** para incrementar o contador sem uma atualização de página.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-256">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="3f3ef-257">Incrementar um contador em uma página da Web normalmente requer a escrita de JavaScript, mas com Blazor C#você pode usar.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-257">Incrementing a counter in a webpage normally requires writing JavaScript, but with Blazor you can use C#.</span></span>

<span data-ttu-id="3f3ef-258">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="3f3ef-258">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="3f3ef-259">Uma solicitação de `/counter` no navegador, conforme especificado pela diretiva `@page` na parte superior, faz com que o componente `Counter` processe seu conteúdo.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-259">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="3f3ef-260">Os componentes são renderizados em uma representação na memória da árvore de renderização que pode ser usada para atualizar a interface do usuário de maneira flexível e eficiente.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-260">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="3f3ef-261">Sempre que o botão **clicar em mim** estiver selecionado:</span><span class="sxs-lookup"><span data-stu-id="3f3ef-261">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="3f3ef-262">O evento `onclick` é acionado.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-262">The `onclick` event is fired.</span></span>
* <span data-ttu-id="3f3ef-263">O método `IncrementCount` é chamado.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-263">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="3f3ef-264">O `currentCount` é incrementado.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-264">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="3f3ef-265">O componente é renderizado novamente.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-265">The component is rendered again.</span></span>

<span data-ttu-id="3f3ef-266">O tempo de execução compara o novo conteúdo com o conteúdo anterior e aplica apenas o conteúdo alterado para o Modelo de Objeto do Documento (DOM).</span><span class="sxs-lookup"><span data-stu-id="3f3ef-266">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="3f3ef-267">Adicione um componente a outro componente usando a sintaxe HTML.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-267">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="3f3ef-268">Por exemplo, adicione o componente `Counter` à Home Page do aplicativo adicionando um elemento `<Counter />` ao componente `Index`.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-268">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="3f3ef-269">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="3f3ef-269">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="3f3ef-270">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-270">Run the app.</span></span> <span data-ttu-id="3f3ef-271">A Home Page tem seu próprio contador fornecido pelo componente `Counter`.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-271">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="3f3ef-272">Os parâmetros de componente são especificados usando atributos ou [conteúdo filho](xref:blazor/components#child-content), que permitem definir propriedades no componente filho.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-272">Component parameters are specified using attributes or [child content](xref:blazor/components#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="3f3ef-273">Para adicionar um parâmetro ao componente `Counter`, atualize o bloco de `@code` do componente:</span><span class="sxs-lookup"><span data-stu-id="3f3ef-273">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="3f3ef-274">Adicione uma propriedade pública para `IncrementAmount` com um atributo `[Parameter]`.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-274">Add a public property for `IncrementAmount` with a `[Parameter]` attribute.</span></span>
* <span data-ttu-id="3f3ef-275">Altere o método `IncrementCount` para usar o `IncrementAmount` ao aumentar o valor de `currentCount`.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-275">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="3f3ef-276">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="3f3ef-276">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="3f3ef-277">Especifique o `IncrementAmount` no elemento `<Counter>` do componente de `Index` usando um atributo.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-277">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="3f3ef-278">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="3f3ef-278">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="3f3ef-279">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-279">Run the app.</span></span> <span data-ttu-id="3f3ef-280">O componente `Index` tem seu próprio contador que é incrementado em dez cada vez que o botão **Click me** está selecionado.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-280">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="3f3ef-281">O componente `Counter` (*Counter. Razor*) em `/counter` continua a incrementar um.</span><span class="sxs-lookup"><span data-stu-id="3f3ef-281">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="3f3ef-282">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="3f3ef-282">Next steps</span></span>

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="3f3ef-283">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="3f3ef-283">Additional resources</span></span>

* <xref:blazor/templates>
* <xref:signalr/introduction>
