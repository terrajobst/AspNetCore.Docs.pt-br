---
title: Introdução ao ASP.NET Core mais incrivelmente
author: guardrex
description: Comece com o mais elaborado criando um aplicativo mais incrivelmente com as ferramentas de sua escolha.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/15/2019
uid: blazor/get-started
ms.openlocfilehash: fc368be5eb2e5d8f7c80071dc86a02ae986a685f
ms.sourcegitcommit: 35a86ce48041caaf6396b1e88b0472578ba24483
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72391047"
---
# <a name="get-started-with-aspnet-core-blazor"></a><span data-ttu-id="d95ab-103">Introdução ao ASP.NET Core mais incrivelmente</span><span class="sxs-lookup"><span data-stu-id="d95ab-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="d95ab-104">Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="d95ab-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="d95ab-105">Introdução ao mais incrivelmente:</span><span class="sxs-lookup"><span data-stu-id="d95ab-105">Get started with Blazor:</span></span>

::: moniker range=">= aspnetcore-3.1"

1. <span data-ttu-id="d95ab-106">Instale o [SDK da versão prévia do .NET Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="d95ab-106">Install the [.NET Core 3.1 Preview SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="d95ab-107">Instale o modelo [Webassembly mais incrivelmente](xref:blazor/hosting-models#blazor-webassembly) executando o comando a seguir em um shell de comando.</span><span class="sxs-lookup"><span data-stu-id="d95ab-107">Install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) template by running the following command in a command shell.</span></span> <span data-ttu-id="d95ab-108">O pacote [Microsoft. AspNetCore. incrivelmente. templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/) tem uma versão de visualização enquanto o Webassembly de mais de baixo está em visualização.</span><span class="sxs-lookup"><span data-stu-id="d95ab-108">The [Microsoft.AspNetCore.Blazor.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/) package has a preview version while Blazor WebAssembly is in preview.</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.1.0-preview1.19508.20
   ```

1. <span data-ttu-id="d95ab-109">Siga as orientações para sua escolha de ferramentas:</span><span class="sxs-lookup"><span data-stu-id="d95ab-109">Follow the guidance for your choice of tooling:</span></span>

   # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d95ab-110">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d95ab-110">Visual Studio</span></span>](#tab/visual-studio)

   <span data-ttu-id="d95ab-111">1 \.</span><span class="sxs-lookup"><span data-stu-id="d95ab-111">1\.</span></span> <span data-ttu-id="d95ab-112">Instale o [Visual Studio 16,4 Preview 2 ou posterior](https://visualstudio.microsoft.com/vs/preview/) com a ASP.net e a carga de trabalho de **desenvolvimento Web** .</span><span class="sxs-lookup"><span data-stu-id="d95ab-112">Install [Visual Studio 16.4 Preview 2 or later](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="d95ab-113">2 \.</span><span class="sxs-lookup"><span data-stu-id="d95ab-113">2\.</span></span> <span data-ttu-id="d95ab-114">Crie um novo projeto.</span><span class="sxs-lookup"><span data-stu-id="d95ab-114">Create a new project.</span></span>

   <span data-ttu-id="d95ab-115">3 \.</span><span class="sxs-lookup"><span data-stu-id="d95ab-115">3\.</span></span> <span data-ttu-id="d95ab-116">Selecione **aplicativo mais incrivelmente**.</span><span class="sxs-lookup"><span data-stu-id="d95ab-116">Select **Blazor App**.</span></span> <span data-ttu-id="d95ab-117">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="d95ab-117">Select **Next**.</span></span>

   <span data-ttu-id="d95ab-118">4 \.</span><span class="sxs-lookup"><span data-stu-id="d95ab-118">4\.</span></span> <span data-ttu-id="d95ab-119">Forneça um nome ao projeto no campo **Nome do projeto** ou aceite o nome do projeto padrão.</span><span class="sxs-lookup"><span data-stu-id="d95ab-119">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="d95ab-120">Confirme se a entrada de **local** está correta ou forneça um local para o projeto.</span><span class="sxs-lookup"><span data-stu-id="d95ab-120">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="d95ab-121">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="d95ab-121">Select **Create**.</span></span>

   <span data-ttu-id="d95ab-122">5 \.</span><span class="sxs-lookup"><span data-stu-id="d95ab-122">5\.</span></span> <span data-ttu-id="d95ab-123">Para obter uma experiência de Webassembly mais experiente, escolha o modelo de **aplicativo Webassembly mais incrivelmente** .</span><span class="sxs-lookup"><span data-stu-id="d95ab-123">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="d95ab-124">Para uma experiência de servidor mais incrivelmente, escolha o modelo de **aplicativo de servidor** mais experiente.</span><span class="sxs-lookup"><span data-stu-id="d95ab-124">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="d95ab-125">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="d95ab-125">Select **Create**.</span></span> <span data-ttu-id="d95ab-126">Para obter informações sobre os dois modelos de hospedagem mais fáceis, confira o *servidor* e o *Webassembly*mais alto, consulte <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="d95ab-126">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="d95ab-127">6 \.</span><span class="sxs-lookup"><span data-stu-id="d95ab-127">6\.</span></span> <span data-ttu-id="d95ab-128">Pressione **F5** para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d95ab-128">Press **F5** to run the app.</span></span>

   > [!NOTE]
   > <span data-ttu-id="d95ab-129">Se você instalou a extensão do Visual Studio para uma versão prévia anterior do ASP.NET Core mais recente (visualização 6 ou anterior), você pode desinstalar a extensão.</span><span class="sxs-lookup"><span data-stu-id="d95ab-129">If you installed the Blazor Visual Studio extension for a prior preview release of ASP.NET Core Blazor (Preview 6 or earlier), you can uninstall the extension.</span></span> <span data-ttu-id="d95ab-130">Instalar os modelos mais bem em um shell de comando agora é suficiente para trazer os modelos no Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="d95ab-130">Installing the Blazor templates in a command shell is now sufficient to surface the templates in Visual Studio.</span></span>

   # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="d95ab-131">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d95ab-131">Visual Studio Code</span></span>](#tab/visual-studio-code)

   <span data-ttu-id="d95ab-132">1 \.</span><span class="sxs-lookup"><span data-stu-id="d95ab-132">1\.</span></span> <span data-ttu-id="d95ab-133">Instalar o [Visual Studio Code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="d95ab-133">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

   <span data-ttu-id="d95ab-134">2 \.</span><span class="sxs-lookup"><span data-stu-id="d95ab-134">2\.</span></span> <span data-ttu-id="d95ab-135">Instale o mais recente [ C# para a extensão de Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span><span class="sxs-lookup"><span data-stu-id="d95ab-135">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span></span>

   <span data-ttu-id="d95ab-136">3 \.</span><span class="sxs-lookup"><span data-stu-id="d95ab-136">3\.</span></span> <span data-ttu-id="d95ab-137">Para uma experiência de Webassembly mais experiente, execute o seguinte comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="d95ab-137">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorwasm -o WebApplication1
      ```

      <span data-ttu-id="d95ab-138">Para uma experiência de servidor mais incrivelmente, execute o seguinte comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="d95ab-138">For a Blazor Server experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorserver -o WebApplication1
      ```

      <span data-ttu-id="d95ab-139">Para obter informações sobre os dois modelos de hospedagem mais fáceis, confira o *servidor* e o *Webassembly*mais alto, consulte <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="d95ab-139">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="d95ab-140">4 \.</span><span class="sxs-lookup"><span data-stu-id="d95ab-140">4\.</span></span> <span data-ttu-id="d95ab-141">Abra a pasta *WebApplication1* em Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="d95ab-141">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

   <span data-ttu-id="d95ab-142">5 \.</span><span class="sxs-lookup"><span data-stu-id="d95ab-142">5\.</span></span> <span data-ttu-id="d95ab-143">Para um projeto de servidor mais incrivelmente, o IDE solicita que você adicione ativos para compilar e depurar o projeto.</span><span class="sxs-lookup"><span data-stu-id="d95ab-143">For a Blazor Server project, the IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="d95ab-144">Selecione **Sim** na barra superior.</span><span class="sxs-lookup"><span data-stu-id="d95ab-144">Select **Yes**.</span></span>

   <span data-ttu-id="d95ab-145">6 \.</span><span class="sxs-lookup"><span data-stu-id="d95ab-145">6\.</span></span> <span data-ttu-id="d95ab-146">Se estiver usando um aplicativo de servidor mais incrivelmente, execute o aplicativo usando o depurador de Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="d95ab-146">If using a Blazor Server app, run the app using the Visual Studio Code debugger.</span></span> <span data-ttu-id="d95ab-147">Se estiver usando um aplicativo Webassembly mais incrivelmente, execute `dotnet run` na pasta do projeto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d95ab-147">If using a Blazor WebAssembly app, execute `dotnet run` from the app's project folder.</span></span>

   <span data-ttu-id="d95ab-148">7 \.</span><span class="sxs-lookup"><span data-stu-id="d95ab-148">7\.</span></span> <span data-ttu-id="d95ab-149">Em um navegador, navegue até `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="d95ab-149">In a browser, navigate to `https://localhost:5001`.</span></span>

   <!--

   # [Visual Studio for Mac](#tab/visual-studio-mac)

   1\. Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/). Switch the [Update channel to Preview](/visualstudio/mac/install-preview).

   2\. Select **File** > **New Solution** or **New Project**.

   3\. In the sidebar, select **.NET Core** > **App**.

   4\. For a Blazor Server experience, select the **Blazor Server App** template. For a Blazor WebAssembly experience, select the **Blazor WebAssembly App** template. Select **Next**. For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.

   5\. The **Target Framework** defaults to **.NET Core 3.0**. Select **Next**.

   6\. In the **Project Name** field, enter `WebApplication1`. Select **Create**.

   7\. Select **Run** > **Run Without Debugging** to run the app *without the debugger*. Running with the debugger isn't supported at this time.

   -->

   # <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="d95ab-150">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="d95ab-150">.NET Core CLI</span></span>](#tab/netcore-cli/)

   <span data-ttu-id="d95ab-151">Para uma experiência de Webassembly mais experiente, execute os seguintes comandos em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="d95ab-151">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="d95ab-152">Para uma experiência de servidor mais incrivelmente, execute os seguintes comandos em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="d95ab-152">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="d95ab-153">Para obter informações sobre os dois modelos de hospedagem mais fáceis, confira o *servidor* e o *Webassembly*mais alto, consulte <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="d95ab-153">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="d95ab-154">Em um navegador, navegue até `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="d95ab-154">In a browser, navigate to `https://localhost:5001`.</span></span>

   ---

::: moniker-end

::: moniker range="< aspnetcore-3.1"

1. <span data-ttu-id="d95ab-155">Instale a versão mais recente do [SDK do .NET Core 3,0](https://dotnet.microsoft.com/download/dotnet-core/3.0) .</span><span class="sxs-lookup"><span data-stu-id="d95ab-155">Install the latest [.NET Core 3.0 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) release.</span></span>

1. <span data-ttu-id="d95ab-156">Opcionalmente, instale o modelo [Webassembly](xref:blazor/hosting-models#blazor-webassembly) mais importante instalando o [SDK do .NET Core 3,1 Preview](https://dotnet.microsoft.com/download/dotnet-core/3.1) e, em seguida, executando o seguinte comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="d95ab-156">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) template by installing the [.NET Core 3.1 Preview SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1) and then running the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.1.0-preview1.19508.20
   ```

1. <span data-ttu-id="d95ab-157">Siga as orientações para sua escolha de ferramentas:</span><span class="sxs-lookup"><span data-stu-id="d95ab-157">Follow the guidance for your choice of tooling:</span></span>

   # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d95ab-158">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d95ab-158">Visual Studio</span></span>](#tab/visual-studio)

   <span data-ttu-id="d95ab-159">1 \.</span><span class="sxs-lookup"><span data-stu-id="d95ab-159">1\.</span></span> <span data-ttu-id="d95ab-160">Instale o [Visual Studio](https://visualstudio.com/vs/) mais recente com a ASP.net e a carga de trabalho de **desenvolvimento Web** .</span><span class="sxs-lookup"><span data-stu-id="d95ab-160">Install the latest [Visual Studio](https://visualstudio.com/vs/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="d95ab-161">2 \.</span><span class="sxs-lookup"><span data-stu-id="d95ab-161">2\.</span></span> <span data-ttu-id="d95ab-162">Opcionalmente, instale o [Visual Studio 16,4 Preview 2 ou posterior](https://visualstudio.microsoft.com/vs/preview/) com o ASP.net e a carga de trabalho de **desenvolvimento Web** para o desenvolvimento de aplicativos Webassembly mais incrivelmente.</span><span class="sxs-lookup"><span data-stu-id="d95ab-162">Optionally install [Visual Studio 16.4 Preview 2 or later](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload for Blazor WebAssembly app development.</span></span>

   <span data-ttu-id="d95ab-163">3 \.</span><span class="sxs-lookup"><span data-stu-id="d95ab-163">3\.</span></span> <span data-ttu-id="d95ab-164">Crie um novo projeto.</span><span class="sxs-lookup"><span data-stu-id="d95ab-164">Create a new project.</span></span>

   <span data-ttu-id="d95ab-165">4 \.</span><span class="sxs-lookup"><span data-stu-id="d95ab-165">4\.</span></span> <span data-ttu-id="d95ab-166">Selecione **aplicativo mais incrivelmente**.</span><span class="sxs-lookup"><span data-stu-id="d95ab-166">Select **Blazor App**.</span></span> <span data-ttu-id="d95ab-167">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="d95ab-167">Select **Next**.</span></span>

   <span data-ttu-id="d95ab-168">5 \.</span><span class="sxs-lookup"><span data-stu-id="d95ab-168">5\.</span></span> <span data-ttu-id="d95ab-169">Forneça um nome ao projeto no campo **Nome do projeto** ou aceite o nome do projeto padrão.</span><span class="sxs-lookup"><span data-stu-id="d95ab-169">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="d95ab-170">Confirme se a entrada de **local** está correta ou forneça um local para o projeto.</span><span class="sxs-lookup"><span data-stu-id="d95ab-170">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="d95ab-171">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="d95ab-171">Select **Create**.</span></span>

   <span data-ttu-id="d95ab-172">6 \.</span><span class="sxs-lookup"><span data-stu-id="d95ab-172">6\.</span></span> <span data-ttu-id="d95ab-173">Para obter uma experiência de Webassembly mais experiente, escolha o modelo de **aplicativo Webassembly mais incrivelmente** .</span><span class="sxs-lookup"><span data-stu-id="d95ab-173">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="d95ab-174">Para uma experiência de servidor mais incrivelmente, escolha o modelo de **aplicativo de servidor** mais experiente.</span><span class="sxs-lookup"><span data-stu-id="d95ab-174">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="d95ab-175">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="d95ab-175">Select **Create**.</span></span> <span data-ttu-id="d95ab-176">Para obter informações sobre os dois modelos de hospedagem mais fáceis, confira o *servidor* e o *Webassembly*mais alto, consulte <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="d95ab-176">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="d95ab-177">7 \.</span><span class="sxs-lookup"><span data-stu-id="d95ab-177">7\.</span></span> <span data-ttu-id="d95ab-178">Pressione **F5** para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d95ab-178">Press **F5** to run the app.</span></span>

   > [!NOTE]
   > <span data-ttu-id="d95ab-179">Se você instalou a extensão do Visual Studio para uma versão prévia anterior do ASP.NET Core mais recente (visualização 6 ou anterior), você pode desinstalar a extensão.</span><span class="sxs-lookup"><span data-stu-id="d95ab-179">If you installed the Blazor Visual Studio extension for a prior preview release of ASP.NET Core Blazor (Preview 6 or earlier), you can uninstall the extension.</span></span> <span data-ttu-id="d95ab-180">Instalar os modelos mais bem em um shell de comando agora é suficiente para trazer os modelos no Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="d95ab-180">Installing the Blazor templates in a command shell is now sufficient to surface the templates in Visual Studio.</span></span>

   # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="d95ab-181">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d95ab-181">Visual Studio Code</span></span>](#tab/visual-studio-code)

   <span data-ttu-id="d95ab-182">1 \.</span><span class="sxs-lookup"><span data-stu-id="d95ab-182">1\.</span></span> <span data-ttu-id="d95ab-183">Instalar o [Visual Studio Code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="d95ab-183">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

   <span data-ttu-id="d95ab-184">2 \.</span><span class="sxs-lookup"><span data-stu-id="d95ab-184">2\.</span></span> <span data-ttu-id="d95ab-185">Instale o mais recente [ C# para a extensão de Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span><span class="sxs-lookup"><span data-stu-id="d95ab-185">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span></span>

   <span data-ttu-id="d95ab-186">3 \.</span><span class="sxs-lookup"><span data-stu-id="d95ab-186">3\.</span></span> <span data-ttu-id="d95ab-187">Para uma experiência de Webassembly mais experiente, execute o seguinte comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="d95ab-187">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorwasm -o WebApplication1
      ```

      <span data-ttu-id="d95ab-188">Para uma experiência de servidor mais incrivelmente, execute o seguinte comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="d95ab-188">For a Blazor Server experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorserver -o WebApplication1
      ```

      <span data-ttu-id="d95ab-189">Para obter informações sobre os dois modelos de hospedagem mais fáceis, confira o *servidor* e o *Webassembly*mais alto, consulte <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="d95ab-189">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="d95ab-190">4 \.</span><span class="sxs-lookup"><span data-stu-id="d95ab-190">4\.</span></span> <span data-ttu-id="d95ab-191">Abra a pasta *WebApplication1* em Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="d95ab-191">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

   <span data-ttu-id="d95ab-192">5 \.</span><span class="sxs-lookup"><span data-stu-id="d95ab-192">5\.</span></span> <span data-ttu-id="d95ab-193">Para um projeto de servidor mais incrivelmente, o IDE solicita que você adicione ativos para compilar e depurar o projeto.</span><span class="sxs-lookup"><span data-stu-id="d95ab-193">For a Blazor Server project, the IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="d95ab-194">Selecione **Sim** na barra superior.</span><span class="sxs-lookup"><span data-stu-id="d95ab-194">Select **Yes**.</span></span>

   <span data-ttu-id="d95ab-195">6 \.</span><span class="sxs-lookup"><span data-stu-id="d95ab-195">6\.</span></span> <span data-ttu-id="d95ab-196">Se estiver usando um aplicativo de servidor mais incrivelmente, execute o aplicativo usando o depurador de Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="d95ab-196">If using a Blazor Server app, run the app using the Visual Studio Code debugger.</span></span> <span data-ttu-id="d95ab-197">Se estiver usando um aplicativo Webassembly mais incrivelmente, execute `dotnet run` na pasta do projeto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d95ab-197">If using a Blazor WebAssembly app, execute `dotnet run` from the app's project folder.</span></span>

   <span data-ttu-id="d95ab-198">7 \.</span><span class="sxs-lookup"><span data-stu-id="d95ab-198">7\.</span></span> <span data-ttu-id="d95ab-199">Em um navegador, navegue até `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="d95ab-199">In a browser, navigate to `https://localhost:5001`.</span></span>

   <!--

   # [Visual Studio for Mac](#tab/visual-studio-mac)

   1\. Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/). Switch the [Update channel to Preview](/visualstudio/mac/install-preview).

   2\. Select **File** > **New Solution** or **New Project**.

   3\. In the sidebar, select **.NET Core** > **App**.

   4\. For a Blazor Server experience, select the **Blazor Server App** template. For a Blazor WebAssembly experience, select the **Blazor WebAssembly App** template. Select **Next**. For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.

   5\. The **Target Framework** defaults to **.NET Core 3.0**. Select **Next**.

   6\. In the **Project Name** field, enter `WebApplication1`. Select **Create**.

   7\. Select **Run** > **Run Without Debugging** to run the app *without the debugger*. Running with the debugger isn't supported at this time.

   -->

   # <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="d95ab-200">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="d95ab-200">.NET Core CLI</span></span>](#tab/netcore-cli/)

   <span data-ttu-id="d95ab-201">Para uma experiência de Webassembly mais experiente, execute os seguintes comandos em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="d95ab-201">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="d95ab-202">Para uma experiência de servidor mais incrivelmente, execute os seguintes comandos em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="d95ab-202">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="d95ab-203">Para obter informações sobre os dois modelos de hospedagem mais fáceis, confira o *servidor* e o *Webassembly*mais alto, consulte <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="d95ab-203">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="d95ab-204">Em um navegador, navegue até `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="d95ab-204">In a browser, navigate to `https://localhost:5001`.</span></span>

   ---

::: moniker-end

<span data-ttu-id="d95ab-205">Várias páginas estão disponíveis em guias na barra lateral:</span><span class="sxs-lookup"><span data-stu-id="d95ab-205">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="d95ab-206">Home</span><span class="sxs-lookup"><span data-stu-id="d95ab-206">Home</span></span>
* <span data-ttu-id="d95ab-207">Contador</span><span class="sxs-lookup"><span data-stu-id="d95ab-207">Counter</span></span>
* <span data-ttu-id="d95ab-208">Buscar dados</span><span class="sxs-lookup"><span data-stu-id="d95ab-208">Fetch data</span></span>

<span data-ttu-id="d95ab-209">Na página Contador, selecione o botão **Clique aqui** para incrementar o contador sem uma atualização de página.</span><span class="sxs-lookup"><span data-stu-id="d95ab-209">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="d95ab-210">Incrementar um contador em uma página da Web normalmente requer a escrita de JavaScript, mas com um mais C#elaborador que você pode usar.</span><span class="sxs-lookup"><span data-stu-id="d95ab-210">Incrementing a counter in a webpage normally requires writing JavaScript, but with Blazor you can use C#.</span></span>

<span data-ttu-id="d95ab-211">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="d95ab-211">*Pages/Counter.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="d95ab-212">Uma solicitação de `/counter` no navegador, conforme especificado pela diretiva `@page` na parte superior, faz com que o componente `Counter` processe seu conteúdo.</span><span class="sxs-lookup"><span data-stu-id="d95ab-212">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="d95ab-213">Os componentes são renderizados em uma representação na memória da árvore de renderização que pode ser usada para atualizar a interface do usuário de maneira flexível e eficiente.</span><span class="sxs-lookup"><span data-stu-id="d95ab-213">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="d95ab-214">Sempre que o botão **clicar em mim** estiver selecionado:</span><span class="sxs-lookup"><span data-stu-id="d95ab-214">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="d95ab-215">O evento `onclick` é acionado.</span><span class="sxs-lookup"><span data-stu-id="d95ab-215">The `onclick` event is fired.</span></span>
* <span data-ttu-id="d95ab-216">O método `IncrementCount` é chamado.</span><span class="sxs-lookup"><span data-stu-id="d95ab-216">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="d95ab-217">O `currentCount` é incrementado.</span><span class="sxs-lookup"><span data-stu-id="d95ab-217">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="d95ab-218">O componente é renderizado novamente.</span><span class="sxs-lookup"><span data-stu-id="d95ab-218">The component is rendered again.</span></span>

<span data-ttu-id="d95ab-219">O tempo de execução compara o novo conteúdo com o conteúdo anterior e aplica apenas o conteúdo alterado para o Modelo de Objeto do Documento (DOM).</span><span class="sxs-lookup"><span data-stu-id="d95ab-219">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="d95ab-220">Adicione um componente a outro componente usando a sintaxe HTML.</span><span class="sxs-lookup"><span data-stu-id="d95ab-220">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="d95ab-221">Por exemplo, adicione o componente `Counter` à página inicial do aplicativo adicionando um elemento `<Counter />` ao componente `Index`.</span><span class="sxs-lookup"><span data-stu-id="d95ab-221">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="d95ab-222">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="d95ab-222">*Pages/Index.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="d95ab-223">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d95ab-223">Run the app.</span></span> <span data-ttu-id="d95ab-224">A Home Page tem seu próprio contador fornecido pelo componente `Counter`.</span><span class="sxs-lookup"><span data-stu-id="d95ab-224">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="d95ab-225">Os parâmetros de componente são especificados usando atributos ou [conteúdo filho](xref:blazor/components#child-content), que permitem definir propriedades no componente filho.</span><span class="sxs-lookup"><span data-stu-id="d95ab-225">Component parameters are specified using attributes or [child content](xref:blazor/components#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="d95ab-226">Para adicionar um parâmetro ao componente `Counter`, atualize o bloco `@code` do componente:</span><span class="sxs-lookup"><span data-stu-id="d95ab-226">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="d95ab-227">Adicione uma propriedade pública para `IncrementAmount` com um atributo `[Parameter]`.</span><span class="sxs-lookup"><span data-stu-id="d95ab-227">Add a public property for `IncrementAmount` with a `[Parameter]` attribute.</span></span>
* <span data-ttu-id="d95ab-228">Altere o método `IncrementCount` para usar o `IncrementAmount` ao aumentar o valor de `currentCount`.</span><span class="sxs-lookup"><span data-stu-id="d95ab-228">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="d95ab-229">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="d95ab-229">*Pages/Counter.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="d95ab-230">Especifique o `IncrementAmount` no elemento `<Counter>` do componente `Index` usando um atributo.</span><span class="sxs-lookup"><span data-stu-id="d95ab-230">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="d95ab-231">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="d95ab-231">*Pages/Index.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="d95ab-232">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d95ab-232">Run the app.</span></span> <span data-ttu-id="d95ab-233">O componente `Index` tem seu próprio contador que é incrementado em dez cada vez que o botão **Click me** é selecionado.</span><span class="sxs-lookup"><span data-stu-id="d95ab-233">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="d95ab-234">O componente `Counter` (*Counter. Razor*) em `/counter` continua a incrementar um.</span><span class="sxs-lookup"><span data-stu-id="d95ab-234">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="d95ab-235">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="d95ab-235">Next steps</span></span>

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="d95ab-236">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="d95ab-236">Additional resources</span></span>

* <xref:signalr/introduction>
